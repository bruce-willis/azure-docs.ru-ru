---
title: 'Действие скрипта: установка пакетов Python с Jupyter в Azure HDInsight'
description: Пошаговые инструкции по использованию действия скрипта для настройки записных книжек Jupyter с кластерами Spark HDInsight для использования внешних пакетов Python.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: c8d0b172682654c858a97b4ca2df99ec5079adaa
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041155"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Использование действия сценария для установки внешних пакетов Python для записных книжек Jupyter в кластерах Apache Spark в HDInsight
> [!div class="op_single_selector"]
> * [Использование волшебных команд](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Использование действия сценария](apache-spark-python-package-installation.md)
>
>

Узнайте, как с помощью действий сценария настроить кластер Apache Spark в HDInsight (Linux) для использования внешних, предоставленных сообществом пакетов **Python**, которые не включены в готовую версию кластера.

> [!NOTE]
> Можно также настроить записную книжку Jupyter с помощью волшебной команды `%%configure`, чтобы использовать внешние пакеты. Дополнительные сведения см. в статье [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Полный список доступных пакетов можно найти в [указателе пакетов](https://pypi.python.org/pypi). Его также можно получить из других источников. Например, можно установить пакеты, предоставляемые посредством [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) или [conda-forge](https://conda-forge.org/feedstocks/).

В этой статье описано, как установить в кластере пакет [TensorFlow](https://www.tensorflow.org/) с помощью действия сценария и использовать его с помощью записной книжки Jupyter.

## <a name="prerequisites"></a>Предварительные требования
Необходимо следующее:

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Если у вас еще нет кластера Spark в HDInsight на платформе Linux, можно выполнить действия сценария во время его создания. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Использование внешних пакетов с записными книжками Jupyter

1. На начальной панели [портала Azure](https://portal.azure.com/)щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, последовательно щелкнув **Просмотреть все** > **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Действия скрипта** в области слева. Выполните настраиваемое действие, устанавливающее TensorFlow на головные узлы и рабочие узлы. Сценарий bash может быть основан на https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh. Обратитесь к документации по [использованию настраиваемых действий сценария](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > В кластере имеются два установленных компонента Python. Spark будет использовать компонент Python Anaconda, расположенный в `/usr/bin/anaconda/bin`. Укажите его в настраиваемых действиях с помощью `/usr/bin/anaconda/bin/pip` и `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Открытие Jupyter Notebook PySpark

    ![Создание записной книжки Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Создание записной книжки Jupyter")

4. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

    ![Указание имени для записной книжки](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Указание имени для записной книжки")

5. Теперь будет выполнена команда `import tensorflow` и запущен пример hello world. 

    Следует скопировать приведенный ниже код.

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Результат должен выглядеть следующим образом:
    
    ![Выполнение кода TensorFlow](./media/apache-spark-python-package-installation/execution.png "Выполнение кода TensorFlow")

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
