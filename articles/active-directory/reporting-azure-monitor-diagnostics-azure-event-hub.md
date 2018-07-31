---
title: Руководство. Потоковая передача журналов Azure Active Directory в концентратор событий Azure (предварительная версия) | Документация Майкрософт
description: Узнайте, как настроить систему диагностики Azure для передачи журналов Azure Active Directory в концентратор событий (предварительная версия)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240270"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Руководство. Потоковая передача журналов Azure Active Directory в концентратор событий Azure (предварительная версия)

В этом руководстве описано, как настроить параметры диагностики Azure Monitor для потоковой передачи журналов Azure Active Directory в концентратор событий Azure. Этот механизм можно использовать для интеграции журналов с помощью сторонних средств SIEM, таких как Splunk и QRadar.

## <a name="prerequisites"></a>Предварительные требования 

Вам необходимы:

* Подписка Azure. Если у вас нет подписки Azure, можно [зарегистрироваться и получить бесплатную пробную версию](https://azure.microsoft.com/free/).
* Клиент Azure Active Directory.
* Пользователь, который является глобальным администратором или администратором безопасности для этого клиента.
* Пространство имен концентраторов событий и концентратор событий в подписке Azure. Дополнительные сведения о его создании см. [здесь](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Архивация журналов в концентратор событий

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Щелкните вкладку **Azure Active Directory** -> **Действия** -> **Журналы аудита**. 
3. Щелкните **Экспортировать параметры**, чтобы открыть колонку "Параметры диагностики". Выберите **Настройка параметра**, чтобы изменить существующие параметры, или **Добавить параметр диагностики**, чтобы добавить новый. Можно задать не более трех параметров. 
    ![Экспорт параметров](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Экспорт параметров")

4. Установите флажок **Передать в концентратор событий**, затем щелкните **Концентратор событий и Настройка**.
5. Выберите подписку Azure и пространство имен концентраторов событий для маршрутизации журналов. Подписка и пространство имен концентраторов событий должны быть связаны с клиентом Active Directory, от которого поступают журналы. В пространстве имен можно также указать концентратор событий, на который будут отправляться журналы. Если концентратор событий не указан, в пространстве имен будет по умолчанию создан концентратор с именем **insights-logs-audit**.
6. Щелкните **ОК**, чтобы выйти из конфигурации концентратора событий.
7. Установите флажок **AuditLogs**, чтобы отправить журналы аудита в учетную запись хранения. 
8. Установите флажок **SignInLogs**, чтобы отправить журналы входа в учетную запись хранения.
9. Щелкните **Сохранить**, чтобы сохранить параметры.
    ![Параметры диагностики](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Параметры диагностики")

10. Примерно через 15 минут убедитесь, что события отображаются в концентраторе событий. Для этого зайдите в концентратор событий с портала и убедитесь, что количество **входящих сообщений** больше нуля. 
    ![Журналы аудита](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Журналы аудита")


## <a name="access-data-from-event-hubs"></a>Данные доступа из концентратора событий

Как только данные появляются в концентраторе событий, к ним можно получить доступ двумя способами.

* **Настроить поддерживаемые средства SIEM просмотра данных**. Для большинства средств требуется строка подключения к концентратору событий и определенные разрешения подписки Azure для просмотра данных из концентратора. Ниже приведен неполный список средств интеграции с Azure Monitor:
    1. **Splunk**. Дополнительные сведения об интеграции журналов Azure AD со Splunk см. в разделе [How to integrate Azure Active Directory logs with Splunk using Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md) (Интеграция журналов Azure Active Directory со Splunk с помощью Azure Monitor (предварительный просмотр)).
    
    2. **IBM QRadar**. Модуль устройства (DSM) Microsoft Azure и протокол концентратора событий Microsoft Azure доступны для загрузки на [веб-сайте поддержки IBM](http://www.ibm.com/support). [Дополнительные сведения об интеграции с Azure.](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)
    
    3. **SumoLogic**. Следуйте [этим инструкциям](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub), чтобы настроить SumoLogic для использования данных из концентратора событий. 

* **Настройка пользовательских средств для чтения данных**. Если текущее SIEM пока не поддерживается в системе диагностики Azure Monitor, можно настроить пользовательские средства с помощью интерфейсов API концентратора событий. Дополнительные сведения см. в разделе [Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Дополнительная информация

* [Integrate Azure Active Directory logs with Splunk using Azure Monitor Diagnostics](reporting-azure-monitor-diagnostics-splunk-integration.md) (Интеграция журналов Azure Active Directory со Splunk с помощью системы диагностики Azure Monitor (предварительный просмотр))
* [Interpret audit logs schema in Azure monitor diagnostics](reporting-azure-monitor-diagnostics-audit-log-schema.md) (Интерпретация схемы журналов аудита в системе диагностики Azure Monitor)
* [Interpret sign-in logs schema in Azure monitor diagnostics](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) (Интерпретация схемы журналов регистрации в системе диагностики Azure Monitor)