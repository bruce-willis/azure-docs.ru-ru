---
title: Пример политики службы управления API Azure. Отправка ошибок в Stackify для записи в журнал | Документация Майкрософт
description: Пример политики службы управления API Azure. Добавление политики регистрации ошибок для отправки ошибок в Stackify и их записи в журнал.
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
ms.openlocfilehash: 5daf21cb55289c874d56910b1240e1433f3d55d0
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945961"
---
# <a name="send-errors-to-stackify-for-logging"></a>Отправка ошибок Stackify для записи в журнал

В этой статье на примере политики службы управления API Azure показано, как добавить политику регистрации ошибок для отправки ошибок в Stackify и их записи в журнал. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

