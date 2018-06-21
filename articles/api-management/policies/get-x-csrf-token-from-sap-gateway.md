---
title: Пример политики службы управления API Azure. Реализация шаблона X-CSRF | Документация Майкрософт
description: Пример политики службы управления API Azure. Реализация шаблона X-CSRF, который используется во многих интерфейсах API. Этот пример относится только к шлюзу SAP.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 2e9f51cc2afadf70c02a23a85a4c2a866f3fd149
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285476"
---
# <a name="implement-x-csrf-pattern"></a>Реализация шаблона X-CSRF

В этой статье на примере политики службы управления API Azure показано, как реализовать шаблон X-CSRF, который используется во многих интерфейсах API. Этот пример относится только к шлюзу SAP. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

