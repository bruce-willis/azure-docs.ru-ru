---
title: Запрос в Azure Log Analytics для мониторинга кластеров Azure HDInsight
description: Узнайте, как выполнять запросы в Azure Log Analytics для мониторинга заданий, выполняемых в кластере HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 2d2de3c2e2b291ec1f5ad170350f19e9e0582eaa
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609315"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Запрос в Azure Log Analytics для мониторинга кластеров HDInsight

Ознакомьтесь с несколькими базовыми сценариями использования Azure Log Analytics для мониторинга кластеров Azure HDInsight:

* [Анализ метрик кластера HDInsight](#analyze-hdinsight-cluster-metrics).
* [Поиск определенных сообщений журнала](#search-for-specific-log-messages).
* [Создание оповещений о событиях](#create-alerts-for-tracking-events).

## <a name="prerequisites"></a>Предварительные требования

* Вы должны настроить кластер HDInsight для использования Azure Log Analytics и добавить в рабочую область решения для управления Log Analytics, связанные с кластером HDInsight. Инструкции см. в статье [Use Azure Log Analytics to monitor HDInsight clusters (Preview)](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)).

## <a name="analyze-hdinsight-cluster-metrics"></a>Анализ метрик кластера HDInsight

Узнайте, как выполнить поиск определенных метрик для кластера HDInsight.

1. Откройте рабочую область OMS, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. В поле поиска всех метрик введите следующий запрос для поиска всех доступных метрик для всех кластеров HDInsight, настроенных для использования Azure Log Analytics, а затем нажмите кнопку **ЗАПУСК**.

        search *

    ![Поиск всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск всех метрик")

    Выходные данные должны выглядеть так:

    ![Выходные данные поиска всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Выходные данные поиска всех метрик")

5. В левой области в категории **Тип** выберите метрику, которую необходимо изучить подробнее, и нажмите кнопку **Применить**. На следующем снимке экрана показано, что выбран тип `metrics_resourcemanager_queue_root_default_CL`.

    > [!NOTE]
    > Чтобы найти необходимую метрику, может потребоваться нажать кнопку **[+] Дополнительно**. Кроме того, кнопка **Применить** находится в нижней части списка, поэтому нужно прокрутить вниз, чтобы увидеть ее.

    Обратите внимание, что запрос в текстовом поле изменяется на тот, который показан в выделенном поле на следующем снимке экрана:

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

6. Можно получить более подробную информацию о конкретной метрике. Например, можно уточнить существующие выходные данные по среднему количеству ресурсов, использованных за 10-минутный интервал, классифицировав их по имени кластера с помощью следующего запроса:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального использования ресурсов в 10-минутном интервале (а также 90-го и 95-го процентиля):

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Поиск определенных сообщений журнала

Узнайте, как искать сообщения об ошибках за определенный интервал времени. Эти шаги являются всего лишь одним примером того, как можно получить сообщение об ошибке, которое вас интересует. Для поиска нужных ошибок можно использовать любое доступное свойство.

1. Откройте рабочую область OMS, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Введите следующий запрос для поиска всех сообщений об ошибках для всех кластеров HDInsight, настроенных для использования Azure Log Analytics, а затем нажмите клавишу **ЗАПУСК**. 

         search "Error"

    Должны отобразиться выходные данные, аналогичные указанным ниже.

    ![Выходные данные поиска всех ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Выходные данные поиска всех ошибок")

4. В левой области в категории **Тип** найдите тип ошибки, который необходимо изучить подробнее, и нажмите кнопку **Применить**.  Обратите внимание, что результаты уточнены и показывают только ошибки выбранного типа.
5. Можно получить более подробную информацию о конкретном списке ошибок, используя параметры, доступные на панели слева. Например: 

    - чтобы просмотреть сообщения об ошибках из определенного рабочего узла, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Выходные данные поиска конкретных ошибок")

    - Чтобы просмотреть ошибку, которая возникла в определенное время, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Выходные данные поиска конкретных ошибок")

6. Чтобы просмотреть определенную ошибку, сделайте следующее. Щелкните **[+]show more** ([+] больше), чтобы просмотреть фактическое сообщение об ошибке.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Выходные данные поиска конкретных ошибок")

## <a name="create-alerts-for-tracking-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для создания оповещения можно использовать любой запрос.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Выполните следующий запрос, на основе которого требуется создать оповещение, а затем нажмите клавишу **ЗАПУСК**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Запрос вернет список запущенных на кластерах HDInsight приложений, в которых возник сбой.

4. Выберите **Новое правило генерации оповещений** в верхней части страницы.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ввод запроса для создания оповещения")

5. В окне **Создать правило** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Создать правило генерации оповещений**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ввод запроса для создания оповещения")

Чтобы изменить или удалить оповещение:

1. Откройте рабочую область Log Analytics на портале Azure.
2. В меню слева выберите **Оповещение**.
3. Выберите оповещение, которое требуется изменить или удалить.
4. Доступны следующие варианты: **Сохранить**, **Отменить**, **Отключить** и **Удалить**.

    ![Удаление и изменение оповещения HDInsight в OMS Log Analytics](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Дополнительные сведения см. в статье [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>См. также

* [Работа с Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
