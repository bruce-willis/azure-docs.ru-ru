---
title: включение файла
description: включение файла
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7843410043b726526380b2a916d96f414a2decda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740598"
---
После применения тегов к ресурсам можно просмотреть затраты на ресурсы с этими тегами. Однако сведения о последнем использовании отобразятся в анализе затрат только через некоторое время. Если сведения о затратах на ресурсы доступны, их можно просмотреть в группах ресурсов в подписке. Чтобы пользователи увидели сведения о затратах, им необходимы права [доступа на уровне подписки к сведениям о выставлении счетов](../articles/billing/billing-manage-access.md).

Чтобы просмотреть сведения о затратах по тегам на портале, выберите подписку и щелкните **Анализ затрат**.

![Анализ затрат](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Затем отфильтруйте результаты по значению тега и нажмите кнопку **Применить**.

![Просмотр данных о затратах по тегу](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Кроме того, можно использовать [API-интерфейсы выставления счетов Azure](../articles/billing/billing-usage-rate-card-overview.md), чтобы получить сведения о затратах программным образом.
