| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число узлов в кластере | 100 |
| Максимальное количество групп pod на один узел ([базовое сетевое взаимодействие с Kubenet][basic-networking]) | 110 |
| Максимальное количество групп pod на один узел ([расширенное сетевое взаимодействие с Azure CNI][advanced-networking]) | 30<sup>1</sup> |
| Максимальное число кластеров в подписке | 100 |

<sup>1</sup> Это значение можно настроить с помощью шаблона-развертывания ARM. Примеры см. [здесь][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
