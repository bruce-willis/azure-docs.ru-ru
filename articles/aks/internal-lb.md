---
title: Создание внутренней подсистемы балансировки нагрузки Службы Azure Kubernetes (AKS)
description: Узнайте, как создать и использовать внутреннюю подсистему балансировки нагрузки для предоставления доступа к службам через AKS (Службу Azure Kubernetes).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001401"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Использование внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS)

Внутренняя балансировка нагрузки позволяет сделать службу Kubernetes доступной для приложений, работающих в той же виртуальной сети, что и кластер Kubernetes. В этой статье описаны процессы создания и использования внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS). Доступно два типа SKU Azure Load Balancer: уровня "Базовый" и "Стандартный". AKS использует SKU уровня "Базовый".

## <a name="create-an-internal-load-balancer"></a>Создание внутреннего балансировщика нагрузки

Чтобы создать внутреннюю подсистему балансировки нагрузки, создайте манифест службы с типом службы *LoadBalancer* и заметкой *azure-load-balancer-internal*, как показано в следующем примере:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

После развертывания с `kubetctl apply` подсистема балансировки нагрузки Azure создается и публикуется в той же виртуальной сети, что и кластер AKS.

![Изображение внутренней подсистемы балансировки нагрузки AKS](media/internal-lb/internal-lb.png)

При просмотре сведений о службе в столбце *EXTERNAL-IP* вы увидите IP-адрес внутренней подсистемы балансировки нагрузки, как показано в следующем примере:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Указание IP-адреса

Если вы хотите использовать определенный IP-адрес для внутренней подсистемы балансировки нагрузки, добавьте в ее спецификацию свойство *loadBalancerIP*. Указанный IP-адрес должен находиться в той же подсети, что и кластер AKS, и не должен быть назначен какому-либо ресурсу.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

При просмотре сведений о службе указанный IP-адрес должен отображаться в столбце *EXTERNAL-IP*.

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Использование частных сетей

При создании кластера AKS вы можете указать дополнительные параметры сети. Такой подход позволяет развернуть кластер в существующей виртуальной сети и подсети (подсетях). Один из вариантов — развернуть кластер AKS в частной сети, подключенной к локальной среде, и предоставить только внутренний доступ к запущенным службам. Подробнее этот вариант описан в статье [о расширенных возможностях настройки сети в AKS][advanced-networking].

При развертывания внутренней подсистемы балансировки нагрузки в кластере AKS, который использует частную сеть, можно применить описанную выше процедуру без каких-либо изменений. Подсистема балансировки нагрузки создается в той же группе ресурсов, где расположен кластер AKS, но она подключается к виртуальной частной сети и подсети, как показано в следующем примере:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Возможно, субъекту-службе для кластера AKS потребуется предоставить роль *участника сети* в группе ресурсов, где развернуты ресурсы виртуальной сети Azure. Чтобы просмотреть параметры субъекта-службы, используйте команду [az aks show][az-aks-show], например так: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Чтобы создать назначение роли, используйте команду [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Назначение другой подсети

Чтобы указать подсеть для подсистемы балансировки нагрузки, добавьте к вашей службе заметку *azure-load-balancer-internal-subnet*. Здесь нужно указать подсеть в той же виртуальной сети, где расположен кластер AKS. При развертывании адрес *EXTERNAL-IP* для подсистемы балансировки нагрузки назначается из указанной подсети.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>удаление подсистемы балансировки нагрузки

Если удалены все службы, использующие внутреннюю подсистему балансировки нагрузки, сама подсистема также удаляется.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о службах Kubernetes в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create