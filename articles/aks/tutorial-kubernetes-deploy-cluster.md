---
title: Руководство по Kubernetes в Azure. Развертывание кластера
description: Руководство по AKS. Развертывание кластера
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341405"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Руководство. Развертывание кластера службы Azure Kubernetes

Kubernetes предоставляет распределенную платформу для контейнерных приложений. AKS позволяет быстро подготовить производственный кластер Kubernetes. В этом руководстве (третья часть из восьми) кластер Kubernetes развертывается в AKS. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * создание субъекта-службы для взаимодействия ресурсов;
> * развертывание кластера AKS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

В последующих руководствах приложение Azure для голосования развертывается в кластере, масштабируется и обновляется.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Руководство. Подготовка приложения для службы Azure Kubernetes][aks-tutorial-prepare-app].

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется субъект-служба Azure Active Directory. Этот субъект-служба может быть автоматически создан с помощью интерфейса командной строки или портала Azure либо вы можете сами предварительно создать этот компонент и назначить дополнительные разрешения. В этом руководстве вы создадите субъект-службу, предоставите доступ к экземпляру ACR Azure, созданному в предыдущем руководстве, а затем создадите кластер AKS.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac][]. Параметр `--skip-assignment` ограничивает назначение дополнительных разрешений.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Запишите значения параметров *appId* и *password*. Эти значения используются в приведенных далее действиях.

## <a name="configure-acr-authentication"></a>Настройка проверки подлинности ACR

Для доступа к образам, хранящимся в ACR, субъекту-службе AKS необходимо предоставить соответствующие права для извлечения образов из ACR.

Сначала получите идентификатор ресурса ACR с помощью команды [az acr show][]. Измените имя реестра `<acrName>` на имя реестра своего экземпляра ACR, а имя группы ресурсов — на имя той группы ресурсов, в которой находится экземпляр ACR.

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

Чтобы предоставить кластеру AKS соответствующие права доступа для использования образов, хранящихся в ACR, создайте назначение роли с помощью команды [az role assignment create][]. Замените `<appId`> и `<acrId>` на значения, собранных на предыдущих двух шагах.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

Теперь создайте кластер AKS с помощью команды [az aks create][]. В следующем примере создается кластер с именем *myAKSCluster* в группе ресурсов *myResourceGroup*. Эта группа ресурсов была создана в [предыдущем руководстве][aks-tutorial-prepare-acr]. Укажите собственные `<appId>` и `<password>` из предыдущего шага, на котором вы создали субъект-службу.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Через несколько минут развертывание завершится и отобразятся сведения о развертывании AKS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure Cloud Shell, клиент kubectl уже установлен. Его также можно установить локально с помощью команды[az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить подключение kubectl к кластеру Kubernetes, выполните команду [az aks get-credentials][]. В следующем примере возвращаются учетные данные для имени кластера AKS *myAKSCluster* в группе ресурсов *myResourceGroup*:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

Чтобы проверить подключение к кластеру, выполните команду [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Выходные данные:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы развернули кластер Kubernetes в AKS. Были выполнены следующие действия:

> [!div class="checklist"]
> * создан субъект-службы для взаимодействия ресурсов;
> * развернут кластер AKS;
> * установлен интерфейс командной строки Kubernetes (kubectl);
> * настроен kubectl.

Перейдите к следующему руководству, чтобы узнать о выполнении приложения в кластере.

> [!div class="nextstepaction"]
> [Развертывание приложений в Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
