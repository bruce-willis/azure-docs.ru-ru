---
title: Пример политики для службы управления API Azure. Отправка контекстной информации запроса во внутреннюю службу | Документация Майкрософт
description: Пример политики для службы управления API Azure. Отправка контекстной информации запроса во внутреннюю службу.
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
ms.openlocfilehash: d6cfd6e63dbc8a56179197b2942c52d15539ae74
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285553"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Отправка контекстной информации запроса во внутреннюю службу

В этой статье приведен пример политики для службы управления API Azure. Он демонстрирует отправку контекстной информации запроса во внутреннюю службу. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

