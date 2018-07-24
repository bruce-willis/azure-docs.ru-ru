---
title: Субъект-служба для кластера Azure Kubernetes
description: Сведения о настройке субъекта-службы Azure Active Directory для кластера Kubernetes и управлении им в AKS.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4dbb8b7abf6da77115d0e1d12621ec20ec60d174
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035206"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Субъекты-службы со службой Azure Kubernetes

Для взаимодействия с API-интерфейсами Azure кластеру Службы контейнеров Azure требуется [субъект-служба Azure Active Directory][aad-service-principal]. Субъект-служба используется для динамического создания и администрирования ресурсов, например [Azure Load Balancer][azure-load-balancer-overview].

В этой статье показано, как настроить субъект-службу для кластера Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом работы


Чтобы создать субъект-службу в Azure AD, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Если у вас нет необходимых разрешений, вам может потребоваться попросить администратора Azure AD или администратора подписки предоставить их или предварительно создать субъект-службу для кластера Kubernetes.

Кроме того, нужно установить и настроить Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Создание субъекта-службы с кластером AKS

При развертывании кластера AKS с помощью команды `az aks create` вы можете автоматически создать субъект-службу.

В следующем примере создается кластер AKS и новый субъект-служба для кластера, так как существующий субъект-служба не указан. Чтобы завершить эту операцию, учетной записи требуются необходимые права для создания субъекта-службы.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Использование имеющегося субъекта-службы

Вы можете использовать с кластером AKS имеющийся субъект-службу Azure AD или заранее создать для него новый. Это полезно при развертывании кластера с портала Azure, когда требуется указать сведения о субъекте-службе. При использовании существующего субъекта-службы секрет клиента следует настроить как пароль.

## <a name="pre-create-a-new-sp"></a>Предварительное создание субъекта-службы

Чтобы создать субъект-службу с помощью Azure CLI, используйте команду [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Результат аналогичен приведенному ниже. Запишите значения `appId` и `password`. Эти значения нужны для создания кластера AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Использование имеющегося субъекта-службы

При использовании предварительно созданного субъекта-службы укажите `appId` и `password` как значения аргументов в команде `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Если вы развертываете кластер AKS с помощью портала Azure, введите значение `appId` в поле **Идентификатор клиента для субъекта-службы**, а значение `password` — в поле **Service principal client secret** (Секрет клиента для субъекта-службы) в форме конфигурации кластера AKS.

![Изображение перехода к приложению Azure для голосования](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Дополнительные замечания

При работе с субъектами-службами AKS и Azure AD имейте в виду следующее.

* Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.
* Все субъекты-службы связаны с определенными приложениями Azure AD. Субъект-служба для кластера Kubernetes может быть связан с любым допустимым именем приложения Azure AD, например `https://www.contoso.org/example`. URL-адрес приложения не обязательно должен быть реальной конечной точкой.
* Указывая **идентификатор клиента** субъекта-службы, используйте значение `appId` (как показано в этой статье) или соответствующее имя (`name`) субъекта-службы, например `https://www.contoso.org/example`.
* На главной виртуальной машине и виртуальной машине узла в кластере Kubernetes учетные данные субъекта-службы хранятся в файле `/etc/kubernetes/azure.json`.
* Если вы используете команду `az aks create`, чтобы автоматически создать субъект-службу, учетные данные субъекта-службы записываются в файл `~/.azure/aksServicePrincipal.json` на компьютере, с которого выполняется команда.
* При удалении кластера AKS, который был создан с помощью команды `az aks create`, созданный автоматически субъект-служба не удаляется. Чтобы удалить субъект-службу, сначала нужно получить идентификатор для службы участника с помощью команды [az ad app list][az-ad-app-list]. Следующий пример выполняет запрос к кластеру с именем *myAKSCluster*, а затем удаляет идентификатор приложения с помощью команды [az ad app delete][az-ad-app-delete]. Замените эти имена своими значениями:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о субъектах-службах Azure Active Directory см. в документации по приложениям Azure AD.

> [!div class="nextstepaction"]
> [Объекты приложения и субъекта-службы в Azure Active Directory (Azure AD)][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
