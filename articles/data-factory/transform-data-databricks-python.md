---
title: Преобразование данных с помощью Databricks Python (Azure) | Документы Майкрософт
description: Сведения об обработке или преобразовании данных с помощью Databricks Python.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: fa19ff6e6293e1d5d082ee40d55b456500fcb1ad
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075560"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Преобразование данных с помощью выполнения действий Python в Azure Databricks

Действие Python в Azure Databricks в [конвейере фабрики данных](concepts-pipelines-activities.md) позволяет запустить файл Python в кластере Azure Databricks. Данная статья основана на материалах статьи о [действиях преобразования данных](transform-data.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования. Azure Databricks — это управляемая платформа для запуска Apache Spark.

## <a name="databricks-python-activity-definition"></a>Определение действия Python в Databricks

Пример определения JSON для действия Python в Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Свойства действия Python в Databricks

В следующей таблице приведено описание свойств, используемых в определении JSON.

|Свойство|ОПИСАНИЕ|Обязательно|
|---|---|---|
|name|Имя действия в конвейере.|Yes|
|description|Описание действия.|Нет |
|Тип|Тип действия Python в Databricks — DatabricksSparkPython.|Yes|
|linkedServiceName|Имя связанной службы Databricks, в которой выполняется действие Python. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md).|Yes|
|pythonFile|URI файла Python, который будет выполнен. Поддерживаются только пути DBFS.|Yes|
|parameters|Параметры командной строки, которые будут переданы в файл Python. Массив строк.|Нет |
|libraries|Список библиотек, которые должны быть установлены на кластере, на котором будет выполнено задание. Массив объектов <строка, объект>|Нет |

## <a name="supported-libraries-for-databricks-activities"></a>Поддерживаемые библиотеки для действий Databricks

В приведенном выше определении действия Databricks необходимо указать следующие типы библиотек: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Дополнительные сведения см. в [документации Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) по типам библиотек.

## <a name="how-to-upload-a-library-in-databricks"></a>Отправка библиотеки в Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[С помощью пользовательского интерфейса рабочей области Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Чтобы получить путь к dbfs библиотеке, добавленной с помощью пользовательского интерфейса, можно использовать [интерфейс командной строки Databricks (установка)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Обычно библиотеки Jar, добавленные с помощью пользовательского интерфейса, хранятся в каталоге dbfs:/FileStore/jars. Вы можете получить список всех библиотек, выполнив следующую команду в интерфейсе командной строки: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Копирование библиотеки с помощью интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Пример: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*