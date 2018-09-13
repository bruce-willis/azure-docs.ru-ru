---
title: Как интегрировать журналы Azure Active Directory со SumoLogic с помощью Azure Monitor (предварительная версия) | Документация Майкрософт
description: Узнайте, как интегрировать журналы Azure Active Directory со SumoLogic с помощью Azure Monitor (предварительная версия).
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c805416b71e7cdb7ce3cdef84baf1167694eda12
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703491"
---
# <a name="integrate-azure-ad-logs-with-sumologic-by-using-azure-monitor-preview"></a>Интеграция журналов Azure AD с SumoLogic с помощью Azure Monitor (предварительная версия)

В этой статье вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) со SumoLogic с помощью Azure Monitor. Сначала вы направите журналы в концентратор событий Azure, а затем интегрируете этот концентратор событий со SumoLogic.

## <a name="prerequisites"></a>Предварительные требования

Для использования этой функции необходимо иметь следующее.
* Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Подписка с поддержкой единого входа SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Шаги по интеграции журналов Azure AD с SumoLogic 

1. Сначала [выполните потоковую передачу журналов Azure AD в концентратор событий Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Настройте имеющийся экземпляр SumoLogic, чтобы [собирать журналы для Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Установите приложение Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards), чтобы использовать предварительно настроенные панели мониторинга, которые предоставляют анализ среды в реальном времени.

 ![панель мониторинга](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Дополнительная информация

* [Интерпретация схемы журналов аудита в Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret the Azure AD sign-in logs schema in Azure Monitor (preview)](reference-azure-monitor-sign-ins-log-schema.md) (Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия))
* [Часто задаваемые вопросы и известные проблемы](overview-activity-logs-in-azure-monitor.md#frequently-asked-questions)
