---
title: Как интегрировать журналы Azure Active Directory со Splunk с помощью Azure Monitor (предварительная версия) | Документация Майкрософт
description: Узнайте, как интегрировать журналы Azure Active Directory со Splunk с помощью Azure Monitor (предварительная версия).
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503848"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Интеграция журналов Azure AD со Splunk с помощью Azure Monitor (предварительная версия)

В этой статье вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) со Splunk с помощью Azure Monitor. Сначала вы направите журналы в концентратор событий Azure, а затем интегрируете этот концентратор событий со Splunk.

## <a name="prerequisites"></a>Предварительные требования

Для использования этой функции необходимо иметь следующее.
* Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* Надстройка Azure Monitor для Splunk. [Скачайте и настройте свой экземпляр Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Учебник 

1. Откройте свой экземпляр Splunk и выберите **Data Summary** (Сводка данных).

    ![Кнопка "Data Summary" (Сводка данных)](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Выберите вкладку **Sourcetypes** (Типы источников), а затем — **amal: aadal:audit**.

    ![Вкладка "Sourcetypes" (Типы источников) в диалоговом окне сводки данных](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Отобразятся журналы действий Azure AD, как показано на рисунке ниже.

    ![Журналы действий](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Если вам не удается установить надстройку в экземпляре Splunk (например, если вы используете прокси-сервер или работаете со Splunk Cloud), можно передать эти события в сборщик событий HTTP Splunk с помощью этой [функции Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), активируемой при поступлении новых сообщений в концентратор событий. 
>

## <a name="next-steps"></a>Дополнительная информация

* [Интерпретация схемы журналов аудита в Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpret the Azure AD sign-in logs schema in Azure Monitor (preview)](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) (Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия))
* [Часто задаваемые вопросы и известные проблемы](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
