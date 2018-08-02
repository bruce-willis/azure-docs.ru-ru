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
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240573"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Интеграция журналов Azure Active Directory со Splunk с помощью Azure Monitor (предварительная версия)

В этой статье вы узнаете, как интегрировать журналы Azure Active Directory со Splunk с помощью Azure Monitor. Сначала необходимо направить журналы в концентратор событий Azure, а затем интегрировать его со Splunk.

## <a name="prerequisites"></a>Предварительные требования

1. Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Используйте следующие [инструкции](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md), чтобы скачать надстройку Azure Monitor для Splunk и настроить в свой экземпляр Splunk.

## <a name="tutorial"></a>Учебник 

1. Откройте свой экземпляр Splunk и щелкните **Data Summary** (Сводка данных).
    ![Сводка данных](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "Data summary")

2. Перейдите на вкладку **Sourcetypes** (Исходные типы) и выберите **amal: aadal:audit**. ![Вкладка "Sourcetypes" (Исходные типы)](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes tab")

3. Отобразятся журналы действий Azure AD, как показано на рисунке ниже.
    ![Журналы действий](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "Activity Logs")

> [!NOTE]
> Если вам не удается установить надстройку в экземпляр Splunk (например, если вы используете прокси-сервер или работаете со Splunk Cloud), можно передать эти события в сборщик событий HTTP Splunk с помощью [этой функции Azure, активируемой при поступлении новых сообщений в концентратор событий](https://github.com/Microsoft/AzureFunctionforSplunkVS). 
>

## <a name="next-steps"></a>Дополнительная информация

* [Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия)](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия)](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Часто задаваемые вопросы и известные проблемы](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)