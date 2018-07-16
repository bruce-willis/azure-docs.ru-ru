---
title: Одностраничное приложение Bing для поиска видео | Документация Майкрософт
description: В этой статье показано, как использовать API Bing для поиска видео в одностраничном веб-приложении.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 11/01/2017
ms.author: v-gedod
ms.openlocfilehash: 55f662721e007e03c8f43f19d8b905e755cfe1d8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382169"
---
# <a name="tutorial-single-page-video-search-app"></a>Руководство по одностраничным приложениям для поиска видео
API Bing для поиска видео позволяет выполнять поиск в Интернете и получать результаты видео, относящиеся к поисковому запросу. В этом руководстве мы создаем одностраничное веб-приложение, использующее API Bing для поиска в Интернете для отображения результатов поиска прямо на странице. Приложение включает в себя компоненты HTML, CSS и JavaScript.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> При нажатии заголовков JSON и HTTP в нижней части страницы отображаются сведения об ответе JSON и HTTP-запросе. Эти данные позволяют лучше изучить службу.

![JSON, HTTP raw results](./media/json-http-raw-results.png)

Учебное приложение иллюстрирует приведенные ниже задачи.
> [!div class="checklist"]
> * Вызов API Bing для поиска видео в JavaScript.
> * Передача параметров поиска в API-интерфейсах поиска Bing.
> * Отображение результатов поиска видео или необязательное включение веб-страниц, новостей или изображений.
> * Временные рамки поиска: 24 часа, прошедшая неделя, месяц или все время.
> * Отображение страниц через результаты поиска.
> * Обработка ключа идентификатора клиента Bing и подписки API.
> * Обработка ошибок, которые могут возникнуть.

Страница руководства является полностью автономной; на ней не используется никаких внешних платформ, таблиц стилей и даже файлов изображений. Она использует только широко распространенные функции языка JavaScript и работает с текущими версиями всех основных браузеров.

В этом руководстве затрагиваются отдельные части исходного кода. Доступен полный [исходный код](tutorial-bing-video-search-single-page-app-source.md). Чтобы запустить пример, скопируйте исходный код, вставьте его в текстовый редактор и сохраните файл как `bing.html`.

## <a name="app-components"></a>Компоненты приложения
Как и любое другое одностраничное веб-приложение, это учебное приложение состоит из трех частей:

> [!div class="checklist"]
> * HTML — определяет структуру и содержимое страницы;
> * CSS — определяет внешний вид страницы;
> * JavaScript — определяет поведение страницы.

Большинство компонентов HTML и CSS являются обычными, поэтому в руководстве это не обсуждается. HTML-код содержит форму поиска, в которой пользователь вводит запрос и выбирает параметры поиска. Форма связана с JavaScript, который выполняет поиск, используя атрибут `onsubmit` тега `<form>`:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
Обработчик `onsubmit` возвращает значение `false`, которое препятствует отправке формы на сервер. Код JavaScript фактически осуществляет сбор необходимой информации из формы и выполняет поиск.

HTML также содержит разделы (теги `<div>` HTML), где отображаются результаты поиска.

## <a name="managing-subscription-key"></a>Управление ключом подписки

Чтобы не включать в код ключ подписки для API поиска Bing, мы используем для его хранения постоянное хранилище браузера. Перед сохранением ключа мы запрашиваем ключ пользователя. Если на дальнейшем этапе ключ отклоняется API, мы аннулируем сохраненный ключ. В результате пользователю предлагается запросить ключ еще раз.

Мы определяем функции `storeValue` и `retrieveValue`, которые используют объект `localStorage` (если браузер поддерживает его) или файл cookie. Наша функция `getSubscriptionKey()` использует эти функции для хранения и извлечения ключа пользователя.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
Тег `<form>` HTML `onsubmit` вызывает функцию `bingWebSearch`, чтобы возвращать результаты поиска. `bingWebSearch` использует `getSubscriptionKey()` для проверки подлинности каждого запроса. Как показано в предыдущем определении, `getSubscriptionKey` запрашивает у пользователя ключ, если ключ не был введен. Затем ключ сохраняется для продолжения использования приложением.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Выбор параметров поиска
На следующем изображении показано текстовое поле запроса и параметры, которые определяют поиск.

![Параметры Bing для поиска новостей](media/video-search-options.png)

HTML-форма включает элементы со следующими именами:

|Элемент|ОПИСАНИЕ|
|-|-|
| `where` | Раскрывающееся меню для выбора рынка (расположения и языка), который используется для поиска. |
| `query` | Текстовое поле для ввода условий поиска. |
| `modules` | Флажки для продвижения определенных модулей результатов, всех результатов или связанных видео. |
| `when` | Раскрывающееся меню для необязательного ограничения поиска за последний день, неделю или месяц. |
| `safe` | Флажок, указывающий, следует ли использовать функцию безопасного поиска Bing для фильтрации результатов с материалами для взрослых. |
| `count` | Скрытое поле. Количество результатов поиска для каждого запроса. Измените, чтобы отобразить меньше или больше результатов на странице. |
| `offset`|  Скрытое поле. Смещение первого результата поиска в запросе для разбиения по страницам. Сбрасывается на `0` при новом запросе. |

> [!NOTE]
> Поиск в Интернете Bing предоставляет дополнительные параметры запроса. Мы используем только некоторые из них.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Например, параметр `SafeSearch` в действительном вызове API может быть `strict`, `moderate` или `off`, при этом `moderate` является значением по умолчанию. Тем не менее в форме используется флажок, который имеет только два состояния. Код JavaScript преобразует это значение в `strict` или `off` (`moderate` не используется).

## <a name="performing-the-request"></a>Выполнение запроса
Учитывая запрос, строку параметров и ключ API, функция `BingWebSearch` использует объект `XMLHttpRequest`, чтобы сделать запрос к конечной точке поиска Bing.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleOnLoad);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
После успешного выполнения HTTP-запроса JavaScript вызывает обработчик событий `load`, функцию `handleOnLoad()`, чтобы передать успешный запрос HTTP GET в API. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Если во время выполнения операции поиска возникает ошибка, то API Bing для поиска новостей возвращает код состояния, отличный от "HTTP: 200", и включает сведения об ошибке в ответ JSON. Кроме того, если запрос имел ограничение по скорости, то API возвращает пустой ответ.
Успешный HTTP-запрос *не* обязательно означает, что сам поиск был успешным. 

Большая часть кода в обеих описанных выше функциях предназначена для обработки ошибок. Ошибки могут возникать на следующих этапах:

|Этап|Возможные ошибки|Чем обрабатывается|
|-|-|-|
|Построение объекта запроса JavaScript|Недопустимый URL-адрес|Блок `try`/`catch`|
|Выполнение запроса|Ошибки сети, прерванные соединения|Обработчики событий `error` и `abort`|
|Выполнение поиска|Недопустимый запрос, недопустимый JSON-файл, ограничения скорости|Тесты в обработчике событий `load`|

Ошибки обрабатываются посредством вызова функции `renderErrorMessage()` со всеми имеющимися сведениями об ошибке. Если ответ передает полный набор тестов ошибок, то мы вызываем функцию `renderSearchResults()` для отображения результатов поиска на странице.

## <a name="displaying-search-results"></a>Отображение результатов поиска
Основная функция отображения результатов поиска — `renderSearchResults()`. Эта функция принимает JSON, возвращенный службой поиска новостей Bing, и отображает результаты новостей и связанные с ними поисковые запросы, если они есть.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the resuts to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Результаты поиска возвращаются как объекты верхнего уровня `value` в ответе JSON. Мы передаем их в нашу функцию `renderResultsItems()`, которая выполняет их итерацию и вызывает функцию для отображения каждого элемента в HTML. Полученный HTML возвращается в `renderSearchResults()`, где он вставляется в раздел `results` на странице.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

API Bing для поиска новостей возвращает до четырех различных видов связанных результатов, каждый в своем собственном объекте верхнего уровня. К ним относятся:

|Отношение|ОПИСАНИЕ|
|-|-|
|`pivotSuggestions`|Запросы, которые заменяют сводное слово в оригинальном поиске другим. Например, если вы ищете "красные цветы", сводное слово может быть "красные", а сводным предложением может быть "желтые цветы".|
|`queryExpansions`|Запросы, которые сужают исходный поиск, добавляя больше условий. Например, если вы ищете "Microsoft Surface", расширение запроса может быть "Microsoft Surface Pro".|
|`relatedSearches`|Запросы, которые также были введены другими пользователями, которые ввели первоначальный поиск. Например, если вы ищете "гора Рейнир", связанный поиск может быть "г. Святой Елены".|
|`similarTerms`|Запросы, похожие по смыслу с исходным запросом. Например, если вы ищете "школы", аналогичным термином может быть "образование".|

Как ранее было показано в `renderSearchResults()`, мы отображаем только предложения `relatedItems` и размещаем результирующие ссылки на боковой панели страницы.

## <a name="rendering-result-items"></a>Рендеринг элементов результатов

В коде JavaScript есть объект `searchItemRenderers`, который содержит *обработчики*: функции, создающие HTML-код для каждого типа результатов поиска. На странице поиска видео используется только `videos`. См. другие руководства о различных типах рендеринга.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Функция-обработчик может принимать следующие параметры:

|Параметр|ОПИСАНИЕ|
|-|-|
|`item`| Объект JavaScript, содержащий свойства элемента, такие как URL-адрес и его описание.|
|`index`| Индекс элемента результатов в коллекции.|
|`count`| Число элементов в коллекции элемента результатов поиска.|

Параметры `index` и `count` можно использовать для нумерации результатов, создания специального HTML-кода для начала или конца коллекции, вставки разрывов строки после определенного числа элементов и т. д. Если от обработчика не требуется выполнять эту функцию, то ему не нужно принимать эти два параметра.

Отрисовщик `video` показан в следующем фрагменте javascript. Используя конечную точку видео, все результаты имеют тип `Videos`. `searchItemRenderers` показаны в следующем сегменте кода.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

Функция отрисовщика:
> [!div class="checklist"]
> * Создает тег параграфа, назначает его классу `images` ​​и передает его в массив html.
> * Вычисляет размер эскиза изображения (с фиксированной шириной на 60 пикселей, высота рассчитывается пропорционально).
> * Создает HTML-тег `<img>` для отображения эскиза изображения. 
> * Создает HTML-тег `<a>`, который связывает со страницей, содержащей это изображение.
> * Создает описание, которое отображает сведения об этом изображении и о сайте, на котором оно размещено.

Размер эскиза используется как в тегах `<img>`, так и в `h` и `w` в URL-адресе эскиза. Затем [Bing thumbnail service](resize-and-crop-thumbnails.md) (Служба эскизов Bing) предоставляет эскиз точно такого размера.

## <a name="persisting-client-id"></a>Сохранение идентификатора клиента
Ответы от интерфейсов API поиска Bing могут содержать заголовок `X-MSEdge-ClientID`, который необходимо отправить обратно в API с последующими запросами. Если используется несколько API-интерфейсов поиска Bing, то по возможности со всеми ими необходимо использовать один идентификатор клиента.

Наличие заголовка `X-MSEdge-ClientID` позволяет интерфейсам API Bing связывать все поисковые запросы пользователя, а это дает два важных преимущества.

Во-первых, поисковая система Bing может применять к операциям поиска прошлый контекст и находить результаты, которые лучше соответствуют потребностям пользователя. Если пользователь ранее уже вводил поисковые запросы, например, связанные с мореплаванием под парусом, то при последующем поиске по слову "knots" в приоритетном порядке могут возвращаться сведения об узлах, которые используются в мореплавание.

Во-вторых, Bing может случайным образом выбирать пользователей для опробования новых возможностей, прежде чем делать их широко доступными. Предоставление одного и того же идентификатора клиента при каждом запросе гарантирует, что пользователи, которым был открыт доступ к определенному компоненту, всегда будут иметь к нему доступ. Без идентификатора клиента такой компонент может появляться и исчезать в результатах поиска пользователя, как бы случайным образом.

Из-за политик безопасности браузера (CORS) заголовок `X-MSEdge-ClientID` может быть недоступным для кода JavaScript. Это ограничение возникает, когда ответ поиска и страница, его запросившая, имеют разные источники. В рабочей среде такая проблема с политикой решается путем размещения серверного сценария, который выполняет вызов API, на одном домене с веб-страницей. Поскольку такой сценарий будет иметь тот же источник, что и веб-страница, заголовок `X-MSEdge-ClientID` станет доступным для JavaScript.

> [!NOTE]
> В рабочем веб-приложении все равно необходимо выполнить запрос на стороне сервера. В противном случае ваш ключ API-интерфейса поиска Bing должен быть включен в веб-страницу, где он будет доступен для всех, кто просматривает источник. С вас будет взиматься плата за любое использование в рамках вашего ключа подписки API, включая запросы, выполненные неавторизованными сторонами, поэтому важно не предоставлять доступ к своему ключу.

В целях разработки запрос API Bing для поиска в Интернете можно выполнить через прокси-сервер CORS. Ответ с такого прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит нашему учебному приложению иметь доступ к заголовку идентификатора клиента. Для начала, если у вас не установлен язык Node.js, [установите его](https://nodejs.org/en/download/). Затем введите следующую команду в командном окне:

    npm install -g cors-proxy-server

Затем в HTML-файле измените конечную точку службы "Поиск в Интернете Bing" на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Закрытие этого окна приводит к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Справочник по API поиска видео Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)