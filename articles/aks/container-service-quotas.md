---
title: Квоты службы Azure Kubernetes (AKS) и ее доступность в регионах
description: Квоты по умолчанию и доступность в регионах для службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.openlocfilehash: 1610ea93eed03fe6efe28e63a7151409e1946f5b
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988720"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Квоты и доступность в регионах для службы Azure Kubernetes (AKS)

Все службы Azure включают несколько стандартных ограничений и квот для ресурсов и функций. В разделах ниже содержится подробная информация об ограничениях по умолчанию для нескольких ресурсов службы Azure Kubernetes (AKS), а также сведения о доступности службы AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Сведения о соответствующих ограничениях см. в статье о [подписке Azure и ограничениях службы](../azure-subscription-service-limits.md).

## <a name="region-availability"></a>Регионы доступности

Служба Azure Kubernetes (AKS) доступна в следующих регионах:

- Восточная часть Австралии
- Центральная Канада
- Восточная Канада
- Центральный регион США
- Восточная часть США
- Восток США 2
- Восточная часть Японии
- Северная Европа
- Южная часть Великобритании
- Западная Европа
- Запад США
- Западный регион США 2

## <a name="next-steps"></a>Дополнительная информация

Вы можете повысить лимит нескольких ограничений и квот. Чтобы запросить увеличение квоты одного или нескольких ресурсов, которые поддерживают такую возможность, отправьте [запрос в службу поддержки Azure][azure-support] (в качестве **типа проблемы** укажите "Квота").

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
