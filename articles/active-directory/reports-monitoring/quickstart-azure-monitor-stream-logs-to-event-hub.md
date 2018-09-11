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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1a7f91a0d15dd67d86f83485b4aad01a3bae37b3
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699075"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>Руководство: потоковая передача журналов Azure AD в концентратор событий Azure (предварительная версия)

В этом руководстве описано, как настроить параметры диагностики Azure Monitor для потоковой передачи журналов Azure Active Directory (Azure AD) в концентратор событий Azure. Используйте этот механизм для интеграции своих журналов со сторонними инструментами управления информационной безопасностью и событиями безопасности (SIEM), такими как Splunk и QRadar.

## <a name="prerequisites"></a>Предварительные требования 

Для использования этой функции необходимо иметь следующее.

* Подписка Azure. Если у вас нет подписки Azure, можно [зарегистрироваться и получить бесплатную пробную версию](https://azure.microsoft.com/free/).
* Клиент Azure AD.
* Пользователь, который является *глобальным администратором* или *администратором безопасности* для этого клиента Azure AD.
* Пространство имен Центров событий и концентратор событий в подписке Azure. Узнайте, как [создать концентратор событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="archive-logs-to-an-event-hub"></a>Архивация журналов в концентратор событий

1. Войдите на [портале Azure](https://portal.azure.com). 

2. Щелкните **Azure Active Directory** > **Действие** > **Журналы аудита**. 

3. Выберите **Параметры экспорта**.  
    
4. В области **Параметры диагностики** выполните одно из действий ниже:
    * Чтобы изменить имеющиеся параметры, выберите **Настройка параметра**.
    * Чтобы добавить новые параметры, выберите **Add diagnostics setting** (Добавить параметр диагностики).  
      Можно задать не более трех параметров.

      ![Параметры экспорта](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Установите флажок **Передать в концентратор событий**,а затем щелкните **Концентратор событий и Настройка**.

6. Выберите подписку Azure и пространство имен Центров событий для маршрутизации журналов.  
    Подписка и пространство имен Центров событий должны быть связаны с клиентом Azure AD, из которого поступают журналы. В пространстве имен Центров событий можно также указать концентратор событий, на который будут отправляться журналы. Если концентратор событий не указан, он будет создан в пространстве имен с именем по умолчанию **insights-logs-audit**.

7. Нажмите кнопку **ОК**, чтобы выйти из конфигурации концентратора событий.

8. Выполните одно из приведенных ниже действий или оба:
    * Установите флажок **AuditLogs**, чтобы отправить журналы аудита в учетную запись хранения. 
    * Установите флажок **SignInLogs**, чтобы отправить журналы входа в учетную запись хранения.

9. Нажмите кнопку **Сохранить**, чтобы сохранить параметры.

    ![Параметры диагностики](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Примерно через 15 минут проверьте, что события отображаются в концентраторе событий. Для этого зайдите в концентратор событий с портала и убедитесь, что количество **входящих сообщений** больше нуля. 

    ![Журналы аудита](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Доступ к данным из концентратора событий

После того, как данные появятся в концентраторе событий, вы можете получить доступ к ним и считать их двумя способами:

* **Настройте поддерживаемый инструмент SIEM**. Для большинства инструментов требуется строка подключения к концентратору событий и определенные разрешения подписки Azure для чтения данных из концентратора событий. К сторонним инструментам, поддерживающим интеграцию с Azure Monitor, помимо прочего, относятся:
    * **Splunk**. Дополнительные сведения об интеграции журналов Azure AD со Splunk с помощью Azure Monitor см. в [этой](tutorial-integrate-activity-logs-with-splunk.md) статье.
    
    * **IBM QRadar**. DSM и протокол концентратора событий Azure доступны для скачивания на [веб-сайте поддержки IBM](http://www.ibm.com/support). Дополнительные сведения об интеграции с Azure см. на веб-сайте [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic.** Чтобы настроить Sumo Logic для получения данных из концентратора событий, см. статью об [установке приложения Azure AD и просмотре панелей мониторинга](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Настройте специальные инструменты**. Если текущий инструмент SIEM пока не поддерживается в системе диагностики Azure Monitor, можно настроить пользовательские инструменты с помощью API Центров событий. Дополнительные сведения см. в статье [Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Дополнительная информация

* [Integrate Azure AD logs with Splunk by using Azure Monitor (preview)](tutorial-integrate-activity-logs-with-splunk.md) (Интеграция журналов Azure AD со Splunk с помощью Azure Monitor (предварительная версия))
* [Интеграция журналов Azure AD с SumoLogic с помощью Azure Monitor](howto-integrate-activity-logs-with-sumologic.md)
* [Интерпретация схемы журналов аудита в Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret the Azure AD sign-in logs schema in Azure Monitor (preview)](reference-azure-monitor-sign-ins-log-schema.md) (Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия))
