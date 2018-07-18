---
title: Квоты виртуальных ЦП для Azure | Документация Майкрософт
description: Дополнительные сведения о квотах виртуальных ЦП для Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716522"
---
# <a name="virtual-machine-vcpu-quotas"></a>Квоты виртуальных ЦП виртуальной машины

Квоты виртуальных ЦП для виртуальных машин и масштабируемых наборов виртуальных машин разделяются на два уровня для каждой подписки в каждом регионе. Первый уровень — это общие региональные виртуальные ЦП, а второй — ядра для различных семейств размеров виртуальных машин, например виртуальные ЦП серии D. При развертывании новой виртуальной машины ее виртуальные ЦП не должны превышать квоту на виртуальные ЦП для определенного семейства размеров виртуальных машин или квоту на общие региональные виртуальные ЦП. При превышении любой из этих квот развертывание виртуальной машины будет запрещено. Имеется также квота на общее количество виртуальных машин в регионе. Сведения о каждой из этих квот можно просмотреть в разделе **Использование и квоты** на странице **Подписки** [портала Azure](https://portal.azure.com). Кроме того, можно запросить значения с помощью Azure CLI.


## <a name="check-usage"></a>Проверка использования

Данные об использовании квоты можно проверить с помощью команды [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Результат должен выглядеть следующим образом.


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Зарезервированные экземпляры виртуальных машин
Зарезервированные экземпляры виртуальных машин, которые ограничиваются одной подпиской, добавляют новый аспект к квотам на виртуальные ЦП. Эти значения описывают число экземпляров указанного размера, которые можно развернуть в подписке. Они выступают в качестве заполнителя в системе квоты, позволяя убедиться, что квота зарезервирована, а также обеспечить возможность развертывания зарезервированных экземпляров в подписке. Например, если в определенной подписке есть 10 зарезервированных экземпляров Standard_D1, лимит использования для них будет составлять 10 единиц. Таким образом в Azure будет обеспечиваться наличие по крайней мере 10 ЦП в рамках квоты на общие региональные виртуальные ЦП, которые будут использоваться для экземпляров Standard_D1, а также наличие хотя бы 10 виртуальных ЦП в рамках квоты на ЦП семейства Standard D, которые будут использоваться для экземпляров Standard_D1.

Если увеличение квоты необходимо для приобретения зарезервированного экземпляра с одной подпиской, вы можете [запросить увеличение квоты](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) по подписке.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о выставлении счетов и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
