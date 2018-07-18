---
title: Пример политики службы управления API Azure. Добавление функций в серверную службу | Документация Майкрософт
description: Пример политики службы управления API Azure. Способы добавления функций в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды.
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
ms.openlocfilehash: a019eb4556dc7cde34d51af6858f576e8ea9abcf
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284537"
---
# <a name="add-capabilities-to-a-backend-service"></a>Добавление функций в серверную службу

В этой статье на примере политики службы управления API Azure показано, как добавить функции в серверную службу. В статье показано, как принять имя вместо широты и долготы в API прогнозирования погоды. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

