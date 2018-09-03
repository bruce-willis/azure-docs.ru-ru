---
title: Использование службы "Анализ текста" с Power BI в Azure Cognitive Services | Документация Майкрософт
description: Узнайте, как использовать службу "Анализ текста" для извлечения ключевых фраз из текста, хранимого в Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889318"
---
# <a name="text-analytics-with-power-bi"></a>Текстовая аналитика с использованием Power BI

Microsoft Power BI извлекает корпоративные данные в удобные для восприятия отчеты, которые можно предоставить всем сотрудникам для более быстрого и глубокого анализа. Как часть Cognitive Services в Microsoft Azure функция "Аналитика текста" извлекает наиболее важные фразы из текста с помощью API ключевых фраз. Оба инструмента помогают быстро узнать, о чем говорят ваши пользователи и что они при этом испытывают.

В этом руководстве описано, как интегрировать Power BI Desktop и API ключевых фраз, чтобы извлекать наиболее важные фразы из отзывов клиентов с помощью настраиваемой функции Power Query, а затем собрать эти фразы в облако Word.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

> [!div class="checklist"]
> * Microsoft Power BI Desktop ([скачать бесплатно](https://powerbi.microsoft.com/get-started/)).
> * Учетная запись Microsoft Azure. ([начните работу в бесплатной пробной версии](https://azure.microsoft.com/free/) или [войдите в существующую](https://portal.azure.com/)).
> * Ключ доступа к функции "Аналитика текста" ([зарегистрироваться](../../cognitive-services-apis-create-account.md) и [получить ключ](../how-tos/text-analytics-how-to-access-key.md)).
> * Отзывы пользователей. ([получите пример](https://aka.ms/cogsvc/ta) или используйте свой).

## <a name="loading-customer-data"></a>Загрузка данных пользователей

Чтобы начать работу, откройте Power BI Desktop и загрузите файл данных с разделителями-запятыми (CSV) **FabrikamComments.csv**. В этом файле представлены возможные действия пользователей за сутки на форуме поддержки небольшой вымышленной компании.

> [!NOTE]
> Power BI использует данные из разнообразных источников, например из Facebook или базы данных SQL. См. дополнительные сведения об [интеграции Facebook с Power BI](https://powerbi.microsoft.com/integrations/facebook/) и [интеграции SQL Server с Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

В главном окне Power BI Desktop на ленте "Главная" найдите группу "Внешние данные". В раскрывающемся меню **Получить данные** для этой группы выберите **Текст/CSV**.

![[Кнопка "Получить данные"]](../media/tutorials/power-bi/get-data-button.png)

Появится диалоговое окно "Открытие". Перейдите в папку "Загрузки" или любую другую папку, содержащую пример файла данных. Щелкните `FabrikamComments.csv` и нажмите кнопку **Открыть**. Появится диалоговое окно "Импорт CSV-файла".

![[Диалоговое окно "Импорт CSV-файла"]](../media/tutorials/power-bi/csv-import.png)

В диалоговом окне "Импорт CSV-файла" можно проверить кодировку, разделитель, строки заголовка и типы столбцов, определенные в Power BI Desktop. Если все определено правильно, щелкните **Загрузить**.

Чтобы просмотреть загруженные данные, перейдите к представлению данных, нажав кнопку "Просмотр данных" на панели рабочей области Power BI слева. Откроется таблица с данными, как в документе Microsoft Excel.

![[Исходное представление импортированных данных]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Подготовка данных

Может потребоваться преобразовать данные в Power BI Desktop перед их обработкой службой ключевых фраз.

Например, в наших данных содержатся поля `subject` и `comment`, поэтому при извлечении ключевых фраз мы учтем текст в обоих этих полях, а не только в поле `comment`. Для этого в Power BI Desktop есть функция "Объединить столбцы".

В главном окне Power BI Desktop на вкладке "Главная" в группе "Внешние данные" щелкните **Редактировать запросы**, чтобы вызвать редактор запросов. 

![[Группа "Внешние данные" на ленте "Главная"]](../media/tutorials/power-bi/edit-queries.png)

В списке "Запросы" слева выберите FabrikamComments, если это еще не сделано.

Затем выделите столбцы `subject` и `comment` в таблице. Может потребоваться горизонтально прокрутить документ, чтобы увидеть эти столбцы. Сначала щелкните заголовок столбца `subject`, а затем, удерживая клавишу CTRL, щелкните заголовок столбца `comment`.

![[Выбор полей для объединения]](../media/tutorials/power-bi/select-columns.png)

На ленте "Преобразование" в группе "Текстовые столбцы" щелкните **Объединить столбцы**. Появится диалоговое окно "Объединение столбцов".

![[Объединение полей в диалоговом окне "Объединение столбцов"]](../media/tutorials/power-bi/merge-columns.png)

В диалоговом окне "Объединение столбцов" выберите "Табуляция" в качестве разделителя и щелкните **ОК**. Готово!

Кроме того, можно отфильтровать пустые сообщения с помощью фильтра "Удалить пустые", а также удалить непечатаемые знаки с помощью очистки. Если в ваших данных содержится столбец `spamscore`, как в нашем примере, с помощью фильтра "Число" можно игнорировать комментарии, содержащие спам.

## <a name="understanding-the-api"></a>Основные сведения об API

API ключевых фраз обрабатывает до тысячи текстовых документов за один HTTP-запрос. В свою очередь, Power BI обрабатывает одну запись за раз, поэтому вызовы API будут содержать только один документ. В каждом обрабатываемом документе для [API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) нужны следующие поля:

| | |
| - | - |
| `id`  | Уникальный идентификатор документа в пределах запроса. Это поле также содержится в ответе, так что, если вы обрабатываете несколько документов, извлеченные ключевые фразы можно легко сопоставить с исходным документом. За один запрос выполняется обработка одного документа, поэтому вы сразу узнаете, с каким документом связан ответ. При этом значение `id` будет одинаковым в каждом запросе.|
| `text`  | Текст для обработки. Расположен в созданном нами столбце `Merged`, содержащем строку темы и текст комментария. API ключевых фраз поддерживает данные длиной максимум 5000 символов.|
| `language` | Код языка документа. Все наши сообщения на английском языке, поэтому в запросе мы задаем значение `en`.|

Нужно объединить эти поля в документ JSON (нотация объектов JavaScript), чтобы отправить в API ключевых фраз. Ответом также будет JSON-документ, который нужно проанализировать и выделить ключевые фразы.

## <a name="creating-a-custom-function"></a>Создание настраиваемой функции

Теперь мы можем создать настраиваемую функцию, чтобы интегрировать Power BI и функция "Аналитика текста". Power BI Desktop вызывает функцию для каждой строки таблицы и создает столбец с результатами.

Этот текст служит параметром функции. Она преобразует данные необходимого JSON и выполняет HTTP-запрос в конечную точку API ключевых фраз. Проанализировав ответ, функция возвращает строку со списком извлеченных ключевых фраз с разделителями-запятыми.

> [!NOTE]
> Настраиваемые функции Power BI Desktop написаны на [языке формул Power Query M](https://msdn.microsoft.com/library/mt211003.aspx) (М для краткости). М — язык функционального программирования на основе [F#](https://docs.microsoft.com/dotnet/fsharp/). Чтобы завершить работу с этим руководством, не нужно быть программистом, — весь необходимый код приведен ниже.

Оставайтесь в окне редактора запросов. На ленте "Главная" щелкните **Создать источник** (в группе "Создать запрос") и в раскрывающемся меню выберите **Пустой запрос**. 

В списке "Запросы" появится новый запрос с именем Query1 по умолчанию. Дважды щелкните эту запись и переименуйте ее в `KeyPhrases`.

Откройте окно Расширенного редактора, щелкнув кнопку **Расширенный редактор** в группе "Запрос" на ленте "Главная". Удалите находящийся в окне код и вставьте следующий. 

> [!NOTE]
> В примерах ниже подразумевается, что конечная точка находится в https://westus.api.cognitive.microsoft.com.  Служба "Анализ текста" поддерживает создание подписки в 13 разных регионах. Если вы зарегистрированы в другом регионе, необходимо использовать конечную точку для выбранного региона. Регион отображается на странице "Общие сведения" портала Azure при выборе подписки службы "Анализ текста".

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Вставьте ключ API службы "Анализ текста", полученный на панели мониторинга Microsoft Azure, в строку `apikey`. (Не удаляйте кавычки вокруг ключа.) Нажмите **Готово**.

## <a name="using-the-function"></a>Использование функции

Теперь можно использовать настраиваемую функцию, чтобы извлекать ключевые фразы из комментариев пользователей и хранить их в новом столбце таблицы. 

Вернитесь к запросу FabrikamComments в редакторе запросов, откройте ленту "Добавление столбца" и в группе "Общие" нажмите кнопку **Вызвать настраиваемую функцию**.

![[Кнопка "Вызвать настраиваемую функцию"]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

В диалоговом окне "Вызов настраиваемой функции" задайте имя `keyphrases` для нового столбца. В запросе функции задайте настраиваемую функцию `KeyPhrases`. 

Появится новое поле с выбором поля для параметра `text` функции. В раскрывающемся меню выберите `Merged` (столбец, полученный после объединения полей темы и сообщения).

![[Вызов настраиваемой функции]](../media/tutorials/power-bi/invoke-custom-function.png)

В конце нажмите кнопку **ОК**.

Когда все будет готово, Power BI вызовет функцию один раз для каждой строки в таблице, выполняя запросы ключевых фраз и добавляя новый столбец. Но перед этим может потребоваться указать параметры проверки подлинности и конфиденциальности.

## <a name="authentication-and-privacy"></a>Проверка подлинности и конфиденциальность

После закрытия диалогового окна "Вызов настраиваемой функции" появится баннер с предложением указать способ подключения к конечной точке ключевых фраз.

![[Баннер учетных данных]](../media/tutorials/power-bi/credentials-banner.png)

Нажмите кнопку **Редактировать учетные данные**, убедитесь, что выбран анонимный доступ, затем выберите **Подключить**. 

> [!NOTE]
> Зачем выбирать анонимный доступ? Служба текстовой аналитики выполнит проверку подлинности с помощью ключа API, поэтому Power BI не нужно предоставлять учетные данные для самого HTTP-запроса.

![[Выбор анонимного доступа для проверки подлинности]](../media/tutorials/power-bi/access-web-content.png)

Если баннер "Редактировать учетные данные" все еще активен после выбора анонимного доступа, вероятно, не указан ключ API. Проверьте настраиваемую функцию `KeyPhrases` в Расширенном редакторе.

Затем появится баннер с предложением указать сведения об уровне конфиденциальности источника данных. 

![[Баннер с запросом конфиденциальных сведений]](../media/tutorials/power-bi/privacy-banner.png)

Нажмите **Продолжить** и в диалоговом окне выберите "Общедоступный" для всех источников данных в диалоговом окне. Нажмите **Сохранить**.

![[Настройка уровня конфиденциальности источника данных]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Создание облака Word

После завершения работы с баннерами на ленте "Главная" щелкните **Закрыть и применить**, чтобы закрыть редактор запросов.

Power BI Desktop требуется несколько секунд, чтобы выполнить необходимые HTTP-запросы. В новом столбце `keyphrases` будут ключевые фразы, обнаруженные API ключевых фраз в тексте каждой строки. 

С помощью этого столбца можно создать облако Word. Сначала нажмите кнопку "Отчет" в главном окне Power BI Desktop слева.

> [!NOTE]
> Зачем использовать извлеченные ключевые фразы вместо текста всего комментария? Ключевые фразы содержат *важные* слова из комментариев пользователей, а не только *самые распространенные*. Кроме того, размер слова в полученном облаке не изменяется в соответствии с частотой его использования в относительно небольшом числе комментариев.

Если у вас нет настраиваемого визуального элемента облака Word, установите его. На панели "Визуализации" справа щелкните многоточие (**...**) и выберите **Import From Store** (Импорт из Store). Введите "облако" в строке поиска и нажмите кнопку **Добавить** рядом с облаком Word. Power BI установит визуальный элемент облака Word и сообщит об этом.

![[Добавление настраиваемого визуального элемента]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Теперь мы создадим облако Word.

![[Значок облака Word на панели визуализаций]](../media/tutorials/power-bi/visualizations-panel.png)

Сначала щелкните значок облака Word на панели "Визуализации". В рабочей области появится новый отчет. Перетащите поле `keyphrases` с панели "Поля" в поле "Категория" на панели "Визуализации". Облако Word появится внутри отчета.

Перейдите на страницу "Формат" на панели "Визуализации". В категории "Исключения" включите **Стоп-слова по умолчанию**, чтобы не добавлять в облако короткие распространенные слова, например "of". 

![[Активация стоп-слов по умолчанию]](../media/tutorials/power-bi/default-stop-words.png)

Далее отключите **Поворот текста** и **Заголовок**.

![[Активация режима фокусировки]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Нажмите кнопку "Режим фокусировки", чтобы улучшить отображение облака. Инструмент развернет облако на всю рабочую область, как показано ниже.

![[Облако Word]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Другие возможности службы "Анализ текста"

Служба "Анализ текста", как одна из служб Cognitive Services от Microsoft Azure, также поддерживает анализ тональности и распознавание языка. Распознавание языка можно использовать, когда комментарий пользователя содержит не только английские слова.

Оба этих API схожи с API ключевых фраз, поэтому можно использовать практически идентичные настраиваемые функции для интеграции с Power BI Desktop. Создайте пустой запрос и вставьте в Расширенный редактор код ниже, как вы делали раньше. (Не забудьте про ключ доступа!) Затем аналогичным способом используйте функцию, чтобы добавить новый столбец в таблицу.

Функция анализа тональности ниже возвращает оценку с указанием степени положительности тона текста.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Ниже приведены две версии функции распознавания языка. Первая версия возвращает код языка по ISO (например, `en` для английского), а вторая — его более привычное имя (например, `English`). Обе версии отличаются лишь последней строкой.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Ниже представлен вариант функции ключевых фраз, который возвращает фразы в виде списка, а не отдельной строки с разделителями-запятыми. 

> [!NOTE]
> Возврат отдельной строки упрощает наш пример облака Word. В свою очередь список — более гибкий формат работы с фразами в Power BI. Power BI Desktop позволяет управлять объектами списка с помощью группы "Структурированный столбец" в редакторе запросов на ленте "Преобразование".

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе текстовой аналитики, языке формул Power Query M и Power BI см. в следующих статьях.

> [!div class="nextstepaction"]
> [Справочник по API анализа текста](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Справочник по Power Query M](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Документация по Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
