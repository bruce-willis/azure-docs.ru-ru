---
title: Создание классических оповещений для служб Azure с помощью кроссплатформенного интерфейса командной строки Azure | Документация Майкрософт
description: Узнайте, как активировать сообщения электронной почты, уведомления или вызовы URL-адресов веб-сайтов (веб-перехватчик) или автоматизированные операции при выполнении заданных условий.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287115"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Создание классических оповещений для служб Azure с помощью кроссплатформенного интерфейса командной строки Azure 

> [!div class="op_single_selector"]
> * [Портал](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> В этой статье объясняется, как создавать классические оповещения метрик прежней версии. Azure Monitor теперь поддерживает [новые улучшенные оповещения на основе метрик](monitoring-near-real-time-metric-alerts.md). Эти оповещения поддерживают мониторинг нескольких метрик и охватывают метрики размерностей. Поддержка новых оповещений Azure CLI на основе метрик ожидается в ближайшее время.
>
>

В этой статье показано, как настроить классические оповещения метрик Azure с помощью кроссплатформенного интерфейса командной строки (Azure CLI).

> [!NOTE]
> Azure Monitor — это новое название для Azure Insights, актуальное с 25 сентября 2016 г. При этом пространства имен и соответствующие команды будут по-прежнему содержать слово insights.

Вы можете получать оповещения на основе метрик для служб Azure или на основе событий, которые происходят в Azure.

* **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.    

* **События журнала действий**. Оповещение может активироваться как при *каждом*, так и при определенном событии. Дополнительные сведения о журналах действий см. в статье [Создание оповещений журнала действий (классических)](monitoring-activity-log-alerts.md). 

Для классического оповещения метрики можно настроить действие, выполняемое при его активации:

* отправка уведомлений по электронной почте администратору службы и соадминистраторам; 
* Отправка сообщений электронной почты на указанные адреса.
* вызов веб-перехватчика.
* Запуск выполнения Runbook Azure (в настоящее время только на портале Microsoft Azure).

Для настройки правил генерации классических оповещений метрик и получения сведений о них можно использовать: 

* [Портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [интерфейс командной строки Azure](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Справку по командам всегда можно получить, введя команду с параметром **-help** в конце. Ниже приведен пример: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Создание правил генерации оповещений с помощью интерфейса командной строки Azure
1. После установки необходимых компонентов войдите в Azure. Команды, необходимые для начала работы, см. в статье с [примерами CLI для Azure Monitor](insights-cli-samples.md). Эти команды помогут вам войти в систему, показать, какая подписка используется, и подготовиться к выполнению команд Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду: 

   **azure insights alerts rule list** *[параметры] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Чтобы создать правило, необходимо сначала получить определенные важные сведения.
    * **Идентификатор ресурса**, для которого необходимо установить оповещение.
    * **Доступные определения метрик** для этого ресурса.

     Получить идентификатор ресурса можно на портале Microsoft Azure. Если ресурс уже создан, выберите его на портале Azure. В следующей колонке в разделе **Параметры** выберите **Свойства**. В следующей колонке отображается поле **Идентификатор ресурса**. 
     
     Идентификатор ресурса можно также получить с помощью [обозревателя ресурсов Azure](https://resources.azure.com/).

     Ниже приведен пример идентификатора ресурса для веб-приложения:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Чтобы получить список доступных метрик и их единиц измерения в предыдущем примере ресурсов, введите следующую команду в интерфейсе командной строки Azure:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* представляет собой детализацию доступного измерения (интервалы в 1 минуту). Выбор различных уровней детализации позволяет использовать разные параметры метрик.
     
4. Чтобы создать правило генерации оповещений на основе метрики, используйте команду следующего вида:

    **azure insights alerts rule metric set** *[параметры] &lt;имя_правила&gt; &lt;расположение&gt; &lt;группа_ресурсов&gt; &lt;размер_окна&gt; &lt;оператор&gt; &lt;пороговое_значение&gt; &lt;ИД_целевого_ресурса&gt; &lt;имя_метрики&gt; &lt;оператор_агрегата_времени&gt;*

    В следующем примере настраивается оповещение о ресурсе веб-сайта. Оповещение активируется, когда любой трафик непрерывно поступает в течении 5 минут, и затем снова активируется, если в течение 5 минут трафик не поступает.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Чтобы при срабатывании классического оповещения метрики создавался веб-перехватчик или отправлялось письмо, необходимо сначала создать это письмо и (или) веб-перехватчики. Сразу после этого следует создать правило. Нельзя связать веб-перехватчики или сообщения электронной почты с созданными правилами.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Можно проверить, правильно ли созданы оповещения, просмотрев отдельное правило.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Чтобы удалить правила, используйте команду в следующем формате:

    **insights alerts rule delete** [параметры] &lt;группа_ресурсов&gt; &lt;имя_правила&gt;

    Эти команды удаляют правила, созданные ранее в этой статье.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с общими сведениями о мониторинге Azure](monitoring-overview.md), включая типы информации, которую можно собирать и отслеживать.
* Узнайте больше о [настройке веб-перехватчиков webhook в оповещениях](insights-webhooks-alerts.md).
* Узнайте больше о [настройке оповещений о событиях журнала действий](monitoring-activity-log-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](../automation/automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](monitoring-overview-of-diagnostic-logs.md), чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.
