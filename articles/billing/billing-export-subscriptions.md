---
title: Экспорт данных верхнего уровня из подписки Azure | Документация Майкрософт
description: В этой статье описано, как просмотреть все идентификаторы подписки Azure, связанной с учетной записью.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659902"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Экспорт и просмотр данных верхнего уровня из подписки
Чтобы просмотреть набор идентификаторов подписок, связанных с учетными данными пользователя, [загрузите файл JSON из Центра управления учетной записью Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Загруженный JSON-файл содержит следующие сведения:
- адрес электронной почты, связанный с учетной записью (параметр Email);
- глобальный уникальный идентификатор, связанный с учетной записью выставления счетов (параметр Puid);
- список подписок в учетной записи, перечисленных за идентификаторами (параметр SubscriptionIds).

### <a name="subscriptionsjson-sample"></a>Пример файла subscriptions.json
~~~~
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
~~~~
