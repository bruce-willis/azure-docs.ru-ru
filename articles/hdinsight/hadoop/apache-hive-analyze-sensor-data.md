---
title: Анализ данных датчиков с помощью Hive и Hadoop в Azure HDInsight
description: Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.topic: conceptual
ms.date: 04/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 412942aa41e7884c6315d921b0b272b033386d17
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590251"
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Анализ данных датчика с помощью консоли запросов Hive с Hadoop в HDInsight

Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View.

> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows. Для версий ниже HDInsight 3.4 кластер HDInsight доступен только в Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


В этом примере используется Hive для обработки исторических данных и выявления проблем с системами отопления и кондиционирования воздуха. В частности, выявляются системы, неспособные надежно поддерживать заданную температуру. Для этого вы будете:

* создавать таблицы HIVE для формирования запросов к данным, хранящимся в файлах значений, разделенных запятыми (CSV-файлах);
* создавать запросы HIVE для анализа данных;
* использовать Microsoft Excel для подключения к HDInsight, чтобы извлекать проанализированные данные;
* использовать Power View для визуализации данных.

![Схема архитектуры решения](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Предварительные требования

* Кластер HDInsight (Hadoop). Сведения о создании кластера см. в статье о [создании кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel используется для визуализации данных с помощью [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Драйвер Microsoft Hive ODBC.](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Запуск образца

1. Откройте веб-браузер и перейдите по следующему URL-адресу: 

         https://<clustername>.azurehdinsight.net

    Замените `<clustername>` на имя вашего кластера HDInsight.

    В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.

2. На открывшейся веб-странице выберите вкладку **Getting Started Gallery** (Коллекция для начала работы), а затем в категории **Solutions with Sample Data** (Решения с примером данных) щелкните пример **Анализ данных датчиков**.

    ![Рисунок коллекции для начала работы](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.
