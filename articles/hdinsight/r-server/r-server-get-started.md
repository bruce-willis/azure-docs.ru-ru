---
title: Начало работы со службами машинного обучения в HDInsight — Azure
description: Узнайте, как создать Apache Spark в кластере HDInsight, который содержит службы машинного обучения, и отправить скрипт R в кластер.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: get-started-article
ms.date: 06/27/2018
ms.openlocfilehash: 7965a91efe58102268f4d54275e7fa3fc4ff74d3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617791"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Начало работы со службами машинного обучения в Azure HDInsight

Azure HDInsight позволяет создавать кластеры служб машинного обучения. Эта поддержка позволяет скриптам R использовать MapReduce и Spark для выполнения распределенных вычислений. В этой статьи описано, как создать кластер служб машинного обучения в HDInsight и запустить скрипт R, демонстрирующий использование Spark для распределенных вычислений в среде R.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Этот процесс описан в видеоролике [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Как получить бесплатную пробную версию Azure для тестирования Hadoop в HDInsight).
* **Клиент Secure Shell (SSH)**. Клиент SSH используется для удаленного подключения к кластеру HDInsight и выполнения команд непосредственно в кластере. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Создание кластера с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **Создать ресурс** > **Данные и аналитика** > **HDInsight**.

3. В колонке **Основные сведения** задайте следующие параметры:

    * **Имя кластера.** Имя кластера HDInsight.
    * **Подписка.** Выберите нужную подписку.
    * **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Имя для входа в кластер по протоколу HTTPS. Эти учетные данные используются для доступа к таким службам, как веб-интерфейс Ambari или REST API.
    * **Secure Shell (SSH) username** (Имя пользователя Secure Shell (SSH)). Имя для доступа к кластеру по протоколу SSH. По умолчанию пароль совпадает с паролем для входа в кластер.
    * **Группа ресурсов.** Группа ресурсов, в которой будет создан кластер.
    * **Расположение.** Регион Azure, в котором будет создан кластер.

        ![Основные сведения о кластере](./media/r-server-get-started/clustername.png)

4. Выберите **тип кластера** и в разделе **Конфигурация кластера** задайте следующие значения:

    * **Тип кластера**: службы машинного обучения

    * **Операционная система**: Linux.

    * **Версия**: ML Server 9.3 (HDI 3.6). Заметки о выпуске для версии ML Server 9.3 доступны на сайте [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **Выпуск R Studio Community для ML Server**: эта среда IDE на основе браузера устанавливается по умолчанию на граничном узле. Если вы не хотите ее устанавливать, снимите соответствующий флажок. Если вы решили установить среду, на портале в колонке приложения для созданного кластера вы найдете URL-адрес для входа в RStudio Server.

        ![Основные сведения о кластере](./media/r-server-get-started/clustertypeconfig.png)

4. Выбрав тип кластера, нажмите кнопку __Выбрать__, чтобы установить выбранный тип. Затем нажмите кнопку __Далее__, чтобы завершить настройку основных параметров.

5. В разделе **Хранилище** выберите или создайте учетную запись хранения. Для выполнения действий, описанных в этом документе, в других полях этого раздела оставьте значения по умолчанию. Нажмите кнопку __Далее__, чтобы сохранить конфигурацию хранилища.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/cluster-storage.png)

6. В разделе **Сводка** просмотрите конфигурацию для кластера. Используйте ссылки __Изменить__, чтобы изменить неправильные параметры. Наконец, нажмите кнопку __Создать__ для создания кластера.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Подключение к RStudio Server

Если вы решили установить HDInsight RStudio Server Community Edition как часть кластера, вы можете войти в RStudio двумя способами.

* **Вариант 1.** Откройте в браузере следующий URL-адрес (где **CLUSTERNAME** — это имя созданного кластера служб машинного обучения):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Вариант 2.** Откройте кластер служб машинного обучения на портале Azure. В разделе **Быстрые ссылки** щелкните **Панели мониторинга служб машинного обучения**.

     ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    В разделе **Панели мониторинга кластера** выберите **R Studio Server**.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > В любом случае при первом входе понадобится выполнить аутентификацию дважды.  При первом запросе на аутентификацию укажите *имя пользователя* и *пароль* администратора кластера. При втором запросе на аутентификацию укажите *идентификатор пользователя SSH* и *пароль*. При последующих операциях входа вам нужно будет указывать только учетные данные SSH.

После подключения ваш экран будет похож на этот снимок экрана:

![Подключение к RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Запуск примера задания

Вы можете отправлять задания с помощью функций scaleR. Ниже приведен пример команды для выполнения задания:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Подключение к пограничному узлу кластера

В этом разделе описано, как подключиться к граничному узлу кластера служб машинного обучения в HDInsight с помощью SSH. Сведения об использовании SSH см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Команда SSH для подключения к граничному узлу кластера служб машинного обучения выглядит так:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Чтобы найти команду SSH для кластера, на портале Azure щелкните имя кластера, выберите **SSH + Cluster login** (SSH + вход в кластер), а затем для параметра **Имя узла** выберите граничный узел. Так можно отобразить информацию о конечной точке SSH для пограничного узла.

![Изображение конечной точки SSH для пограничного узла](./media/r-server-get-started/sshendpoint.png)

Если для защиты учетной записи SSH используется пароль, то предлагается ввести его. Если используется открытый ключ, может потребоваться использовать параметр `-i`, чтобы указать соответствующий закрытый ключ. Например: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Когда подключение будет установлено, отобразится окно запроса, как показано ниже:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Использование консоли R

1. В сеансе SSH используйте следующую команду, чтобы запустить консоль R:  

        R

2. Должны отобразиться выходные данные и версия ML Server, а также другие сведения.
    
3. В строке ввода `>` можно ввести код R. Службы машинного обучения в HDInsight содержат пакеты, которые позволяют легко взаимодействовать с Hadoop и выполнять распределенные вычисления. Например, используйте следующую команду, чтобы просмотреть корень файловой системы по умолчанию для кластера HDInsight.

        rxHadoopListFiles("/")

4. Также можно использовать WASB-адресацию.

        rxHadoopListFiles("wasb:///")

5. Закройте консоль R с помощью следующей команды:

        quit()

## <a name="automated-cluster-creation"></a>Автоматизированное создание кластера

Вы можете автоматизировать создание кластера служб машинного обучения в HDInsight с помощью пакета SDK или PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Сведения о создании кластера служб машинного обучения с помощью пакета SDK для .NET см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Чтобы создать кластер служб машинного обучения с помощью PowerShell, см. статью [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как создать кластер служб машинного обучения в Azure HDInsight и как использовать консоль R в сеансе SSH. В следующих статьях описаны другие способы использования и администрирования служб машинного обучения в HDInsight:

* [Отправка заданий из расширения "Инструменты R для Visual Studio"](r-server-submit-jobs-r-tools-vs.md)
* [Управление кластером служб машинного обучения в HDInsight](r-server-hdinsight-manage.md)
* [Ввод в эксплуатацию кластера R Server в Azure HDInsight](r-server-operationalize.md)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)
* [Решения службы хранилища Azure для служб машинного обучения в Azure HDInsight](r-server-storage.md)
