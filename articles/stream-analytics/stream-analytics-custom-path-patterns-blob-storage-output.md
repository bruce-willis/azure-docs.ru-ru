---
title: Пользовательские шаблоны даты и времени в пути для выходных данных хранилища BLOB-объектов Azure Stream Analytics (предварительная версия)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: da29c6bd8ddc1e2f62a78fb683df5e1784141722
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452570"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Пользовательские шаблоны даты и времени в пути для выходных данных хранилища BLOB-объектов Azure Stream Analytics (предварительная версия)

Azure Stream Analytics поддерживает пользовательские спецификаторы формата даты и времени в пути к файлу для выходных данных хранилища BLOB-объектов. Пользовательские шаблоны даты и времени в пути позволяют указать формат выходных данных, который соответствует соглашениям о потоковой передаче Hive, предоставляя Azure Stream Analytics возможность отправлять данные в Azure HDInsight и Azure Databricks для последующей обработки. Пользовательские шаблоны даты и времени в пути легко реализуются, если ввести в поле "Префикс пути" выходных данных хранилища BLOB-объектов ключевое слово `datetime` вместе со спецификатором формата. Например, `{datetime:yyyy}`.

[Перейдите на портал Azure](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true), чтобы включить предварительную версию функции, которая позволяет настраивать шаблоны даты и времени в пути для выходных данных хранилища BLOB-объектов. Эта функция скоро будет включена на главном портале.

## <a name="supported-tokens"></a>Поддерживаемые токены

Следующие токены спецификатора формата могут использоваться отдельно или в комбинациях для настройки пользовательских форматов даты и времени:

|Спецификатор формата   |ОПИСАНИЕ   |Результаты для примера 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Год как четырехзначное число|2018|
|{datetime:MM}|Месяц от 01 до 12|01|
|{datetime:M}|Месяц от 1 до 12|1|
|{datetime:dd}|День от 01 до 31|02|
|{datetime:d}|День от 1 до 12|2|
|{datetime:HH}|Часы в 24-часовом формате, от 00 до 23|10|
|{datetime:mm}|Минуты от 00 до 24|06|
|{datetime:m}|Минуты от 0 до 24|6|
|{datetime:ss}|Секунды от 00 до 60|08|

Если вы не хотите использовать пользовательские шаблоны даты и времени, можно добавить токен {date} и (или) {time} в префикс пути, чтобы создать раскрывающийся список со встроенными форматами даты и времени.

![Старые форматы даты и времени Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Расширяемость и ограничения

Вы можете использовать столько токенов `{datetime:<specifier>}` в шаблоне пути, сколько нужно, пока не достигнете предельного числа знаков в префиксе пути. Спецификаторы формата не могут быть объединены в один токен, за исключением комбинаций, уже перечисленных в раскрывающемся списке даты и времени. 

Для раздела пути `logs/MM/dd`:

|Допустимое выражение   |Недопустимое выражение   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Вы можете использовать один и тот же спецификатор формата несколько раз в префиксе пути. Токен должен повторяться каждый раз.

## <a name="hive-streaming-conventions"></a>Соглашения для потоковой передачи Hive

Пользовательские шаблоны пути для хранилища BLOB-объектов могут использоваться с соглашением для потоковой передачи Hive, в которой ожидается, что в имени папки будет пометка `column=`.

Например, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Пользовательский тип выходных данных устраняет проблему изменения таблиц и добавления разделов для передачи данных между Azure Stream Analytics и Hive вручную. Вместо этого многие папки можно добавлять автоматически, используя:

```
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Пример

Создайте учетную запись хранения, группу ресурсов, задание Stream Analytics и источник входных данных, следуя инструкциям в статье [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md). Используйте те же примеры данных, которые используются в кратком руководстве, также доступном на сайте [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Создайте приемник выходных данных больших двоичных объектов со следующей конфигурацией:

![Создание приемника выходных данных больших двоичных объектов Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Шаблон полного пути выглядит следующим образом:

```
year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}
```

Когда вы запускаете задание, в контейнере больших двоичных объектов создается структура папок, основанная на шаблоне пути. Вы можете детализировать до уровня дня.

![Выходные данные больших двоичных объектов Stream Analytics с пользовательским шаблоном пути](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Дополнительная информация

* [Описание выходных данных из Azure Stream Analytics](stream-analytics-define-outputs.md)
