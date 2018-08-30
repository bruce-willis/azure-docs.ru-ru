---
title: Доступ к данным предварительной версии Azure Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark | Документация Майкрософт
description: Сведения о выполнении запросов Spark на кластере Azure Databricks для получения доступа к данным в учетной записи хранения Azure Data Lake Storage 2-го поколения.
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 83e6b6817f33cb19c3839a94a8919f3516576d68
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886584"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Руководство. Получение доступа к данным предварительной версии Azure Data Lake Storage 2-го поколения с помощью Azure Databricks и Spark

В этом руководстве описано, как выполнять запросы Spark на кластере Azure Databricks, чтобы запрашивать данные в учетной записи с поддержкой предварительной версии Azure Data Lake Storage 2-го поколения.

> [!div class="checklist"]
> * Создание кластера Databricks
> * Прием неструктурированных данных в учетной записи хранения
> * Активация обработки данных в службе "Функции Azure"
> * Выполнение анализа данных в хранилище BLOB-объектов

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве демонстрируется, как использовать данные о рейсах авиакомпаний и запрашивать эти данные, доступные в [Министерстве транспорта США](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Скачайте данные авиакомпании по крайней мере за два года (выбрав все поля) и сохраните результаты на компьютере. Обязательно запишите имя файла и путь скачивания. Они понадобятся позже.

> [!NOTE]
> Установите флажок **Prezipped file** (Предварительно сжатый файл), чтобы выбрать все поля данных. Размер загрузки будет составлять несколько гигабайт, но такой объем данных необходим для анализа.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Создание учетной записи Azure Data Lake Storage Gen2

Чтобы приступить к работе, создайте [учетную запись Azure Data Lake Storage Gen2](quickstart-create-account.md) и присвойте ей уникальное имя. Перейдите к учетной записи хранения, чтобы получить параметры конфигурации.

> [!IMPORTANT]
> При использовании предварительной версии служба "Функции Azure" работает только с учетными записями Azure Data Lake Storage Gen2, созданными с использованием неструктурированного пространства имен.

1. В разделе **Settings** (Параметры) щелкните **Ключи доступа**.
3. Нажмите кнопку **Копировать** рядом с **key1**, чтобы скопировать значение ключа.

В рамках этого руководства ключ и имя учетной записи необходимы для выполнения следующих действий. Откройте текстовый редактор и скопируйте в него имя учетной записи и ключ для дальнейшего использования.

## <a name="create-a-databricks-cluster"></a>Создание кластера Databricks

Затем нужно создать [кластер Databricks](https://docs.azuredatabricks.net/) для создания рабочей области данных.

1. Создайте [службу Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) и назовите ее **myFlightDataService**. При этом установите флажок *Закрепить на панели мониторинга*.
2. Щелкните **Launch Workspace** (Запустить рабочее пространство), чтобы открыть рабочее пространство в новом окне браузера.
3. Щелкните **Кластеры** на панели навигации слева.
4. Щелкните **Create cluster** (Создать кластер).
5. Введите **myFlightDataCluster** в поле *Имя кластера*.
6. Выберите **Standard_D8s_v3** в поле *Тип рабочего процесса*.
7. Измените значение параметра **Min Workers** (Минимальное количество рабочих процессов) на *4*.
8. Щелкните **Create Cluster** (Создать кластер) в верхней части страницы (этот процесс может занять около 5 минут).
9. После завершения процесса выберите **Azure Databricks** в верхней левой части панели навигации.
10. Выберите **Блокнот** в разделе **New** (Новый) в нижней части страницы.
11. Введите имя по своему усмотрению в поле **Имя** и выберите **Python** в качестве языка.
12. Во всех остальных полях можно оставить значения по умолчанию.
13. Нажмите кнопку **Создать**.
14. Вставьте следующий код в ячейку **Cmd 1**, замените имеющиеся значения на сохраненные значения учетной записи хранения.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfss://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Прием данных

### <a name="copy-source-data-into-the-storage-account"></a>Копирование исходных данных в учетную запись хранения

Далее нужно использовать средство AzCopy для копирования данных из *CSV-файла* в хранилище Azure. Откройте окно командной строки и введите следующие команды: Обязательно замените заполнители `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` и `<ACCOUNT_KEY>` соответствующими значениями, сохраненными при выполнении предыдущего шага.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Использование Databricks Notebook для преобразования CSV-файла в файл Parquet

Повторно откройте Databricks в браузере и сделайте следующее:

1. Выберите **Azure Databricks** в верхней левой части панели навигации.
2. Выберите **Блокнот** в разделе **New** (Новый) в нижней части страницы.
3. Введите **CSV2Parquet** в поле **Имя**.
4. Во всех остальных полях можно оставить значения по умолчанию.
5. Нажмите кнопку **Создать**.
6. Вставьте следующий код в ячейку **Cmd 1** (этот код автоматически сохраняется в текстовом редакторе).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Просмотр данных с использованием распределенной файловой системы Hadoop

Вернитесь в рабочее пространство Databricks и щелкните значок **Последние** на панели навигации слева.

1. Щелкните блокнот **Flight Data Analytics** (Аналитика данных рейсов).
2. Одновременно нажмите клавиши **Ctrl + Alt + N**, чтобы создать ячейку.

Введите каждый из приведенных ниже блоков кода в **Cmd 1** и одновременно нажмите клавиши **Cmd + Ввод**, чтобы выполнить сценарий Python.

Чтобы получить список отправленных через средство AzCopy CSV-файлов, выполните следующий сценарий:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Чтобы создать файл и составить перечень файлов в папке *parquet/flights*, выполните следующий сценарий:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
С помощью этих примеров кода вы изучили иерархическую сущность HDFS, используя данные, хранящиеся в учетной записи с поддержкой Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Запрос данных

Теперь вы можете запрашивать данные, отправленные в хранилище Azure Data Lake. Введите каждый из приведенных ниже блоков кода в **Cmd 1** и одновременно нажмите клавиши **Cmd + Ввод**, чтобы выполнить сценарий Python.

### <a name="simple-queries"></a>Простые запросы

Чтобы создать кадры данных для источников данных, выполните следующий сценарий:

> [!IMPORTANT]
> Обязательно замените заполнитель **<YOUR_CSV_FILE_NAME>** именем файла, скаченного на начальном этапе этого руководства.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

Для выполнения запросов анализа данных выполните следующий сценарий:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>Сложные запросы

Чтобы выполнить следующие более сложные запросы, одновременно запустите каждый сегмент в блокноте и проверьте результаты.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>Дополнительная информация

* [Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](tutorial-extract-transform-load-hive.md)
