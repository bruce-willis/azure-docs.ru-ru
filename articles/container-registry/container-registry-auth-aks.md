---
title: Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes
description: Узнайте, как обеспечить доступ к образам в закрытом реестре контейнеров из Службы Azure Kubernetes с помощью субъекта-службы Azure Active Directory.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: d08fc0cb8e3203a60cbd426145ec50bb3636e758
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857131"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes

При использовании реестра контейнеров Azure (ACR) со Службой Azure Kubernetes (AKS) необходимо установить механизм аутентификации. В этой статье описаны рекомендуемые конфигурации для аутентификации между этими двумя службами Azure.

## <a name="grant-aks-access-to-acr"></a>Предоставление AKS доступа к ACR

Когда вы создаете кластер AKS, Azure также создает субъект-службу для взаимодействия кластера с другими ресурсами Azure. Автоматически созданный субъект-службу можно использовать для аутентификации в реестре ACR. Для этого в Azure AD нужно [назначить субъекту-службе кластера роль](../role-based-access-control/overview.md#role-assignments), у которой есть доступ к реестру контейнеров.

Используйте следующий скрипт для предоставления субъекту-службе, созданному службой AKS, доступа к реестру контейнеров Azure. Прежде чем выполнять этот скрипт, измените переменные `AKS_*` и `ACR_*` в соответствии с условиями своей среды.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Доступ с помощью секрета Kubernetes

В некоторых случаях вы не сможете назначить необходимую роль субъекту-службе, который автоматически создала служба AKS, чтобы предоставить ему доступ к ACR. Например, модель безопасности вашей организации может ограничивать ваши права в каталоге Azure AD, необходимые для назначения роли субъекту-службе, который создала служба AKS. В этом случае вы можете создать новый субъект-службу и предоставить ему доступ к реестру контейнера, используя секрет Kubernetes для получения образа.

Создать субъект-службу можно с помощью следующего скрипта. Учетные данные этого субъекта-службы вы укажете в качестве секрета Kubernetes для получения образа. Прежде чем выполнять этот скрипт, измените переменную `ACR_NAME` в соответствии с условиями своей среды.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a 'Reader' role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Теперь вы можете сохранить учетные данные субъекта-службы в качестве [секрета для получения образа][image-pull-secret] Kubernetes, и кластер AKS будет ссылаться на него при запуске контейнеров.

Чтобы создать секрет Kubernetes, используйте следующую команду **kubectl**. Замените `<acr-login-server>` полным доменным именем вашего реестра контейнеров Azure (в формате "acrname.azurecr.io"). Замените `<service-principal-ID>` и `<service-principal-password>` значениями, полученными при выполнении предыдущего скрипта. Замените `<email-address>` на любой адрес электронной почты правильного формата.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Теперь вы можете использовать секрет Kubernetes в развертываниях pod, указывая его имя (в нашем примере это acr-auth) в параметре `imagePullSecrets`.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
