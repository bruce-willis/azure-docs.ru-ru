---
title: Создание разрешенного IP-адреса для исходящего трафика из кластера службы Azure Kubernetes (AKS)
description: Создание разрешенного IP-адреса для исходящего трафика из кластера службы Azure Kubernetes (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: nepeters
ms.openlocfilehash: 0bafb62fcdc8a24084cf7170a0e0f72bfd7824b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659814"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Исходящий трафик службы Azure Kubernetes (AKS)

По умолчанию адрес исходящего трафика из кластера службы Azure Kubernetes (AKS) назначается случайным образом. Эта конфигурация не обеспечивает точность, когда необходимо идентифицировать IP-адрес для доступа ко внешним службам. В этой статье описывается процесс создания и поддержки статически назначенного IP-адреса исходящего трафика в кластере AKS.

## <a name="egress-overview"></a>Обзор исходящего трафика

Исходящий трафик из кластера AKS регулируется на основе соглашений Azure Load Balancer, описанных [здесь][outbound-connections]. До создания первой службы Kubernetes типа `LoadBalancer` узлы агента не являются частью любого пула Azure Load Balancer. В этой конфигурации у узлов нет общедоступного IP-адреса уровня экземпляра. Azure преобразует исходящий поток в общедоступный исходный IP-адрес, который не является настраиваемым или детерминированным.

После создания службы Kubernetes типа `LoadBalancer` узлы агента добавляются в пул Azure Load Balancer. Azure преобразует исходящий поток в первый общедоступный IP-адрес, настроенный в подсистеме балансировки нагрузки.

## <a name="create-a-static-public-ip"></a>Создание статического общедоступного IP-адреса

Чтобы предотвратить использование случайных IP-адресов, создайте статический IP-адрес и убедитесь, что он используется подсистемой балансировки нагрузки. IP-адрес нужно создать в группе ресурсов **узла** AKS.

Получите имя группы ресурсов, выполнив команду [az resource show][az-resource-show]. Обновите имена группы ресурсов и кластера в соответствии со своей средой.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Затем выполните команду [az network public-ip create][public-ip-create], чтобы создать статический общедоступный IP-адрес. Обновите имя группы ресурсов в соответствии с именем, полученным на последнем шаге.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Создание службы со статическим IP-адресом

Теперь, когда у вас есть IP-адрес, создайте службу Kubernetes с типом `LoadBalancer` и назначьте ей этот IP-адрес.

Создайте файл `egress-service.yaml` и скопируйте в него следующий код YAML. Обновите IP-адрес в соответствии со своей средой.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Выполните команду `kubectl apply` для создания службы и развертывания.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

При создании этой службы в Azure Load Balancer настраивается новый интерфейсный IP-адрес. Если у вас нет других настроенных IP-адресов, тогда этот адрес должен использоваться **всем** исходящим трафиком. Если в Azure Load Balancer настроены несколько адресов, исходящий трафик использует первый IP-адрес в этой подсистеме балансировки нагрузки.

## <a name="verify-egress-address"></a>Проверка адреса исходящего трафика

Чтобы проверить использование общедоступного IP-адреса, используйте такую службу, как `checkip.dyndns.org`.

Запустите ее и подключите к pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

При необходимости установите программу cURL в контейнере:

```console
$ apt-get update && apt-get install curl -y
```

Примените ее к службе `checkip.dyndns.org`. Вернется IP-адрес исходящего трафика.

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

IP-адрес должен соответствовать статическому IP-адресу, прикрепленному к подсистеме балансировки нагрузки Azure.

## <a name="ingress-controller"></a>Контроллер входящего трафика

Чтобы избежать поддержки нескольких общедоступных IP-адресов в Azure Load Balancer, можно использовать контроллер входящего трафика. Контроллеры входящего трафика обеспечивают такие преимущества, как балансировка нагрузки, обработка подключений SSL и TLS, поддержка повторных записей URI и восходящее шифрование протоколов SSL и TLS. Дополнительные сведения о контроллерах входящего трафика в службе AKS см. в статье [Входящий трафик HTTPS в Службе Azure Kubernetes (AKS)][ingress-aks-cluster].

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о программном обеспечении, рассматриваемом в этом документе:

- [Интерфейс командной строки Helm][helm-cli-install]
- [Контроллер входящего трафика NGINX][nginx-ingress]
- [Исходящие подключения Azure Load Balancer][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
