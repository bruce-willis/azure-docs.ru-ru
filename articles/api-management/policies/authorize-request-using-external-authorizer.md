---
title: Пример политики службы управления API Azure. Авторизации запроса с помощью внешнего авторизатора | Документация Майкрософт
description: Пример политики службы управления API демонстрирует, как авторизация запросов выполняется с использованием внешнего авторизатора, инкапсулирующего пользовательскую или устаревшую логику аутентификации или авторизации.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: cce2376c440c233d81198d903709867c5fdca7ec
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34840693"
---
# <a name="authorize-requests-using-external-authorizer"></a>Авторизация запросов с помощью внешнего авторизатора

В этой статье показан пример политики службы управления API Azure, который демонстрирует, как защитить доступ к API с помощью внешнего авторизатора, инкапсулирующего пользовательскую логику аутентификации или авторизации. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики ограничения доступа в службе управления API](../api-management-access-restriction-policies.md)
+ [Примеры политик](../policy-samples.md).