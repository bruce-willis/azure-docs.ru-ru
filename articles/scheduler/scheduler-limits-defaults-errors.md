---
title: Границы, квоты и пороговые значения в планировщике Azure
description: Сведения о границах, квотах, значениях по умолчанию и пороговых значениях регулирования для планировщика Azure.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 0c1e704a3bdec239c87d879ae1ef95e6e76d27fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966922"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Границы, квоты и пороговые значения регулирования в планировщике Azure

> [!IMPORTANT]
> Служба [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из эксплуатации. Для планирования заданий [попробуйте использовать Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Границы, квоты и пороговые значения

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Заголовок x-ms-request-id

Каждый запрос к службе планировщика возвращает заголовок ответа с именем **x-ms-request-id**. Этот заголовок содержит непрозрачное значение, которое однозначно определяет запрос. Таким образом, если запрос постоянно завершается ошибкой и вы убедились, что запрос в правильном формате, вы можете сообщить об ошибке в Майкрософт, указав значение заголовка ответа **x-ms-request-id** и включив следующие данные: 

* значение **x-ms-request-id**;
* приблизительное время, когда был выполнен запрос; 
* идентификаторы подписки Azure, коллекции задания и задания; 
* тип операции, которую пытался выполнить запрос.

## <a name="see-also"></a>См. также

* [Что такое планировщик Azure?](scheduler-intro.md)
* [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)
