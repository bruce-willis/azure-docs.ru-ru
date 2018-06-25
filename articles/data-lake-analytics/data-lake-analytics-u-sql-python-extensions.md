---
title: Расширение возможностей сценариев U-SQL c Python в Azure Data Lake Analytics
description: Сведения о том, как выполнить код Python в сценариях U-SQL в Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 3e895a6ea9bccc0d210f43748edb3eea80ddc6ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625086"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Расширение возможностей сценариев U-SQL c кодом Python в Azure Data Lake Analytics

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем начать, убедитесь, что в вашей учетной записи Azure Data Lake Analytics установлены расширения Python:

* выберите свою учетную запись Data Lake Analytics на портале Azure;
* в меню слева в разделе **Приступая к работе** щелкните **Примеры сценариев**;
* выберите **Установка расширений U-SQL** и нажмите кнопку **ОК**.

## <a name="overview"></a>Обзор 

Расширения Python для U-SQL позволяют разработчикам выполнять код Python с массовым параллелизмом. В следующих примерах представлены основные шаги:

* Используйте инструкцию `REFERENCE ASSEMBLY`, чтобы включить расширения Python для сценария U-SQL
* Использование операции `REDUCE` для разделения входных данных в разделе
* Расширения Python для U-SQL включают встроенный инструмент редукции (`Extension.Python.Reducer`), который выполняет код Python в каждой вершине, назначенный инструменту редукции.
* Сценарий U-SQL содержит встроенный код Python с функцией `usqlml_main`, которая принимает кадр данных Pandas в качестве входных данных и возвращает его в качестве выходных данных.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Интеграция Python c U-SQL

### <a name="datatypes"></a>Типы данных

* Строковые и числовые столбцы из U-SQL преобразовываются без изменений между Pandas и U-SQL.
* Пустые значения U-SQL преобразовываются в значения Pandas `NA` и из них.

### <a name="schemas"></a>Схемы

* Векторы индексов в Pandas не поддерживаются в U-SQL. Все входные кадры данных в функции Python будут всегда содержать 64-разрядный числовой индекс от 0 до соответствующего количества строк минус 1. 
* Наборы данных U-SQL не могут содержать повторяемые имена столбцов.
* Имена столбцов в наборах данных не являются строками. 

### <a name="python-versions"></a>Версии Python
Поддерживается только версия Python 3.5.1 (скомпилированная для Windows). 

### <a name="standard-python-modules"></a>Стандартные модули Python
Включены все стандартные модули Python.

### <a name="additional-python-modules"></a>Дополнительные модули Python
Кроме стандартных библиотек Python, включены несколько часто используемых библиотек Python:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Сообщения об исключении
В настоящее время исключение в коде Python отображается в качестве общего сбоя вершины. В будущем в сообщениях об ошибках заданий U-SQL будут отображаться сообщения об исключении Python.

### <a name="input-and-output-size-limitations"></a>Ограничения размера входных и выходных данных
Каждой вершине назначен ограниченный объем памяти. В настоящее время он составляет 6 ГБ на единицу аналитики. Так как входящие и исходящие кадры данных должны существовать в памяти кода Python, общий размер входных и выходных данных должен находиться в пределах 6 ГБ.

## <a name="see-also"></a>См. также
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка сценариев U-SQL с помощью средств озера данных для Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)
* [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
