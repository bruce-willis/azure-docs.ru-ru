---
title: Руководство. Добавление автозаполнения в поле поиска с помощью Поиска Azure | Документы Майкрософт
description: Примеры того, как улучшить опыт взаимодействия пользователя с дата-центрированными приложениями с помощью функции автозаполнения Поиска Azure и вариантов API.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mcarter
ms.openlocfilehash: 90e99e7d44183d70f4e348c7b9070001fa3c6329
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101057"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Руководство. Добавление автозаполнения в поле поиска с помощью Поиска Azure

В этом руководстве вы узнаете, как использовать [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions), [автозаполнение](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete) и [аспекты](search-faceted-navigation.md) в [REST API Поиска Azure](https://docs.microsoft.com/rest/api/searchservice/) и [пакете SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet), чтобы создать эффективное поле поиска. *Предложения* дают рекомендации по результатам по мере ввода поискового запроса. *Автоматическое заполнение*, [новая функция предварительного просмотра](search-api-preview.md) в Поиске Azure, предоставляет термины из индекса, чтобы завершить текст, который вводит пользователь. Мы сравним несколько способов повысить производительность и легко и быстро находить желаемое, применяя широкие возможности функций поиска в процессе набора текста.

В этом руководстве мы рассмотрим приложение на основе ASP.NET MVC, в котором используется C# для вызова [клиентских библиотек .NET Поиска Azure](https://aka.ms/search-sdk) и JavaScript для прямого вызова REST API Поиска Azure. Приложение в этом руководстве нацелено на индекс, заполняющий демонстрационные данные [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Можно использовать индекс, который уже настроен в демонстрации NYCJobs, или заполнить свой индекс с помощью загрузчика данных в примере решения NYCJobs. В примере используются [пользовательский интерфейс jQuery](https://jqueryui.com/autocomplete/) и библиотеки JavaScript [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) для создания поля поиска, которое поддерживает автозаполнение. Используя эти компоненты, а также Поиск Azure, вы увидите несколько примеров того, как поддержать автозаполнение с упреждением ввода в поле поиска.

Вы выполните следующие действия:

> [!div class="checklist"]
> * скачивание и настройка решения;
> * добавление сведений о службе поиска в параметры приложения;
> * реализация поискового поля ввода;
> * добавление поддержки для списка автозаполнения, который получает данные из удаленного источника; 
> * получение предложений и автозаполнения с помощью пакета SDK для .NET и REST API;
> * поддержка кэширования на стороне клиента для повышения производительности. 

## <a name="prerequisites"></a>предварительным требованиям

* Visual Studio 2017. Можно использовать бесплатную версию [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Загрузите пример [исходного кода](https://github.com/azure-samples/search-dotnet-getting-started) для этого руководства.

* Активная учетная запись Azure и служба поиска Azure (необязательно). Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить [бесплатную пробную версию](https://azure.microsoft.com/free/). Инструкции по подготовке службы см. в статье [Создание службы поиска](search-create-service-portal.md). Учетная запись и служба не обязательны, так как для руководства достаточно будет размещенного индекса NYCJobs, уже существующего для другой демонстрации.

* Загрузите пример кода [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) для импорта данных NYCJobs в индекс службы поиска Azure (необязательно).

> [!Note]
> В бесплатной версии службы поиска Azure вы можете использовать не более трех индексов. Загрузчик данных NYCJobs создает два индекса. Убедитесь, что у вас достаточно места, чтобы служба могла принять новые индексы.

### <a name="set-up-azure-search-optional"></a>Настройка службы поиска Azure (необязательно)

Выполните инструкции в этом разделе, чтобы импортировать данные для примера приложения NYCJobs в собственный индекс. Этот шаг не является обязательным.  Если вы хотите использовать предоставленный пример индекса, перейдите к следующему разделу, где выполняется пример.

1. В папке DataLoader в примере кода NYCJobs откройте файл решения DataLoader.sln в Visual Studio.

1. Обновите сведения о подключении для службы поиска Azure.  Откройте файл App.config в проекте DataLoader и измените параметры appSettings TargetSearchServiceName и TargetSearchServiceApiKey, чтобы они соответствовали службе поиска Azure и ключу API службы поиска Azure.  Они находятся на портале Azure.

1. Нажмите F5 для запуска приложения.  Будет создано 2 индекса и импортированы данные примера NYCJob.

1. В примере кода из руководства откройте файл решения AutocompleteTutorial.sln в Visual Studio.  Откройте файл Web.config в проекте AutocompleteTutorial и измените значения SearchServiceName и SearchServiceApiKey на указанные выше.

### <a name="running-the-sample"></a>Выполнение примера

Теперь все готово к запуску примера приложения из руководства.  Откройте файл решения AutocompleteTutorial.sln в Visual Studio, чтобы выполнить пример.  Решение содержит проект ASP.NET MVC.  Нажмите клавишу F5, чтобы запустить проект и загрузить страницу в браузере по выбору.  В верхней части вы увидите параметр для выбора C# или JavaScript.  Параметр C# вызывает HomeController из браузера и использует пакет SDK для .NET службы поиска Azure для получения результатов.  Параметр JavaScript вызывает REST API службы поиска Azure непосредственно из браузера.  Этот параметр обычно гораздо производительнее, так как контроллер не участвует в процессе.  Можно выбрать параметр, который подходит для ваших потребностей и языковых параметров.  На странице есть несколько примеров автозаполнения с инструкциями для каждого.  Для каждого примера есть рекомендуемый пример текста.  Попробуйте ввести несколько букв в каждом поле поиска и посмотрите, что произойдет.

## <a name="how-this-works-in-code"></a>Как это работает в коде

Теперь, когда вы увидели примеры в браузере, давайте подробно рассмотрим первый пример, все компоненты и как они работают.

### <a name="search-box"></a>Поле поиска

Поле поиска одинаково для любого языка.  Откройте файл Index.cshtml в папке \Views\Home. Самое по себе поле поиска простое:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Это простое текстовое поле ввода с классом для стиля, идентификатором для ссылки в JavaScript и замещающим текстом.  Главное здесь — JavaScript.

### <a name="javascript-code-c"></a>Код JavaScript (C#)

В примере на языке C# используется JavaScript в Index.cshtml для применения библиотеки автозаполнения пользовательского интерфейса jQuery.  Эта библиотека расширяет возможности автозаполнения в поле поиска, совершая асинхронные вызовы котроллера MVC для получения рекомендаций.  Давайте взглянем на код JavaScript в первом примере:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Этот код выполняется в браузере при загрузке страницы для настройки автозаполнения в поле ввода "example1a".  `minLength: 3` гарантирует, что рекомендации будут отображаться, только когда пользователь введет в поле поиска хотя бы три символа.  Исходное значение является важным:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Эта строка указывает API автозаполнения, где можно получить список элементов для отображения под полем поиска.  Так как это проект MVC, он вызывает функцию предложения в HomeController.cs.  Мы рассмотрим это подробнее в следующем разделе.  Он также передает ряд параметров для управления выделением, нечетким соответствием и термином.  API JavaScript автозаполнения добавляет параметр термина.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Расширение примера для поддержки нечеткого соответствия

Поиск нечетких соответствий позволяет получить результаты, даже если пользователь неверно напишет слово в поле поиска.  Давайте испытаем это, изменив исходную строку, чтобы использовать нечеткое соответствие.

Измените такой вариант строки:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

на такой:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Запустите приложение, нажав клавишу F5.

Попробуйте ввести "деректор" и обратите внимание, что возвращаются результаты для запроса "директор", хотя это не точное совпадение.

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Теперь, когда мы изучили код JavaScript для примера, давайте взглянем на код контроллера C#, фактически извлекающий рекомендации с помощью пакета SDK для .NET для Поиска Azure.

1. Откройте файл HomeController.cs в каталоге контроллеров. 

1. В первую очередь вы заметите метод в верхней части класса InitSearch.  Он создает прошедший проверку подлинности клиент индекса HTTP для службы поиска Azure.  Если вы хотите узнать больше о том, как это работает, обратитесь к следующему руководству: [Использование Поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Перейдите к функции предложений.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Функция предложений использует два параметра, которые определяют, возвращается ли четкое совпадение или используется нечеткое соответствие в дополнение к вводу слова для поиска.  Метод создает объект SuggestParameters, который затем передается API функции предложений. Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.
Добавьте точку останова в начале функции предложений и выполните пошаговую отладку кода (необязательно).  Обратите внимание на ответ, возвращаемый пакетом SDK, а также на то, каким образом он преобразуется в результат, возвращаемый методом.

Другие примеры на странице следуют той же схеме, добавляя выделение совпадений, упреждающий ввод для автозаполнения и аспекты для поддержки кэширования результатов автозаполнения на стороне клиента.  Просмотрите каждый из них и разберитесь, как они работают и как использовать их при поиске.

### <a name="javascript-language-example"></a>Пример на языке JavaScript

В примере на языке JavaScript код JavaScript на странице IndexJavaScript.cshtml использует автозаполнение пользовательского интерфейса jQuery.  Это библиотека, которая выполняет большую часть тяжелой работы, обеспечивая удобство поля поиска, и упрощает процесс выполнения асинхронных вызовов к службе поиска Azure для получения рекомендаций.  Давайте взглянем на код JavaScript в первом примере:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Если сравнить это с примером выше, который вызывает контроллер Home, вы заметите несколько сходств.  Конфигурации автозаполнения для `minLength` и `position` абсолютно одинаковы.  А вот источник значительно отличается.  Вместо вызова метода Suggest в контроллере Home запрос REST создается в функции JavaScript и выполняется с помощью ajax.  Затем ответ обрабатываются в success и используется в качестве источника.

## <a name="takeaways"></a>Общие выводы

В этом руководстве показаны основные шаги по созданию поля поиска, которое поддерживает автозаполнение и предложения.  Вы узнали, как можно создать приложение ASP.NET MVC и использовать REST API или пакет SDK для .NET службы поиска Azure для получения предложений.

## <a name="next-steps"></a>Дальнейшие действия

Интегрируйте возможности предложений и автозаполнения в функцию поиска.  Подумайте, как использовать широкие возможности поиска Azure через пакет SDK для .NET или REST API напрямую, чтобы повысить эффективность ввода поисковых запросов.

> [!div class="nextstepaction"]
> [REST API автозаполнения](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)
> [REST API предложений](https://docs.microsoft.com/en-us/rest/api/searchservice/suggestions)
> [Атрибут индекса аспектов в REST API создания индекса](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index)

