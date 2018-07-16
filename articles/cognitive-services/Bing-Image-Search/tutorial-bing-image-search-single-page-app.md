---
title: Одностраничное веб-приложение для службы "Поиск изображений Bing" | Документация Майкрософт
description: В этой статье показано, как использовать API Bing для поиска изображений в одностраничном веб-приложении.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382157"
---
# <a name="tutorial-single-page-web-app"></a>Руководство по одностраничным веб-приложениям

API Bing для поиска изображений позволяет выполнять поиск в Интернете и получать информацию об изображениях, имеющим отношение к поисковому запросу. В этом руководстве объясняется, как создать одностраничное веб-приложение, использующее API Bing для поиска изображений для отображения результатов поиска прямо на странице. Приложение включает в себя компоненты HTML, CSS и JavaScript.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> При нажатии заголовков JSON и HTTP в нижней части страницы отображаются сведения об ответе JSON и HTTP-запросе. Эти данные позволяют лучше изучить службу.

На примере учебного приложения показано, как выполнить такие задачи:

> [!div class="checklist"]
> * Вызов API Bing для поиска изображений из JavaScript.
> * Передача параметров поиска в API Bing для поиска изображений.
> * Отображение результатов поиска.
> * Переход по страницам результатов поиска.
> * Обработка идентификатора клиента Bing и ключа подписки API.
> * Обработка ошибок, которые могут возникнуть.

Страница руководства является полностью автономной. На ней не используются внешние платформы, таблицы стилей и даже файлы изображений. Она использует только широко распространенные функции языка JavaScript и работает с текущими версиями всех основных браузеров.

В этом руководстве затрагиваются только отдельные части исходного кода. Полный исходный код доступен на [отдельной странице](tutorial-bing-image-search-single-page-app-source.md). Скопируйте и вставьте этот код в текстовый редактор и сохраните его как файл `bing.html`.

> [!NOTE]
> Это руководство в большей части схоже с [руководством по одностраничному веб-приложению для API Bing для поиска в Интернете](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md). Но здесь описаны только результаты поиска изображений.

## <a name="app-components"></a>Компоненты приложения

Как и любое другое одностраничное веб-приложение, это учебное приложение состоит из трех частей:

> [!div class="checklist"]
> * HTML — определяет структуру и содержимое страницы;
> * CSS — определяет внешний вид страницы;
> * JavaScript — определяет поведение страницы.

В этом руководстве большинство аспектов работы с HTML и CSS не описываются подробно, так как они довольно просты.

HTML-код содержит форму поиска, в которой пользователь вводит запрос и выбирает параметры поиска. Эта форма связана с языком JavaScript, на котором фактически выполняется поиск по атрибуту `onsubmit` тега `<form>`:

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Обработчик `onsubmit` возвращает значение `false`, что препятствует отправке формы на сервер. Код JavaScript собирает необходимую информацию из формы и выполняет поиск.

HTML также содержит разделы (HTML-теги `<div>`), где отображаются результаты поиска.

## <a name="managing-subscription-key"></a>Управление ключом подписки

Чтобы не включать ключ подписки для API поиска Bing прямо в код, мы используем для его хранения постоянное хранилище браузера. Если ключа нет в хранилище, мы запрашиваем ключ пользователя и сохраняем его для последующего использования. Если на дальнейшем этапе API отклоняет ключ, мы аннулируем сохраненный ключ и снова выводим запрос пользователю.

Мы определяем функции `storeValue` и `retrieveValue`, которые используют объект `localStorage` (если браузер поддерживает его) или файл cookie. Наша функция `getSubscriptionKey()` использует эти функции для хранения и извлечения ключа пользователя.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
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

В теге HTML `<form>` атрибут `onsubmit` вызывает функцию `bingWebSearch`, чтобы возвращать результаты поиска. Функция `bingWebSearch` использует `getSubscriptionKey` для аутентификации каждого запроса. Как показано в предыдущем определении, `getSubscriptionKey` запрашивает у пользователя ключ, если он еще не был введен. Затем ключ сохраняется для последующего использования приложением.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Выбор параметров поиска

![[Форма Bing для поиска изображений]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

HTML-форма включает в себя следующие элементы управления:

| | |
|-|-|
|`where`|Раскрывающееся меню для выбора рынка (расположения и языка), который используется для поиска.|
|`query`|Текстовое поле для ввода условий поиска.|
|`aspect`|Переключатели для выбора пропорций найденных изображений: примерно квадратные, вертикальные или горизонтальны.|
|`color`|Выбор цветности изображений: цветные, черно-белые или с определенным преобладающим цветом.
|`when`|Раскрывающееся меню для ограничения поиска результатами за последний день, неделю или месяц (необязательный параметр).|
|`safe`|Флажок, указывающий, следует ли использовать функцию безопасного поиска Bing для фильтрации результатов с материалами для взрослых.|
|`count`|Скрытое поле. Количество результатов поиска, которые должны возвращаться при каждом запросе. Измените значение, чтобы на странице отображалось меньше или больше результатов.|
|`offset`|Скрытое поле. Смещение первого результата поиска в запросе. Используется для разбиения по страницам. Сбрасывается на `0` при новом запросе.|
|`nextoffset`|Скрытое поле. При получении результатов поиска для этого поля устанавливается значение `nextOffset` из ответа. Это поле позволяет избежать перекрывающихся результатов на соседних страницах.|
|`stack`|Скрытое поле. Список смещений для предыдущих страниц результатов поиска в формате JSON. Используется для перехода на предыдущие страницы.|

> [!NOTE]
> Служба "Поиск изображений Bing" предоставляет много дополнительных параметров запроса. Здесь мы используем только некоторые из них.

Наша функция JavaScript `bingSearchOptions()` преобразует эти поля в частичную строку запроса такого формата, который требуется для API поиска Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Например, функция SafeSearch может иметь значения `strict`, `moderate` или `off`, где `moderate` используется как значение по умолчанию. Но в нашей форме он представлен флажком, который имеет только два состояния. Код JavaScript преобразует это значение в `strict` или `off` (`moderate` не используется).

## <a name="performing-the-request"></a>Выполнение запроса

Получив на вход запрос, строку параметров и ключ API, функция `BingImageSearch` применяет объект `XMLHttpRequest` для выполнения запроса к конечной точке Поиска изображений Bing.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
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
    request.addEventListener("load", handleBingResponse);
    
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

После успешного выполнения HTTP-запроса JavaScript вызывает обработчик событий `load`, функцию `handleBingResponse()`, чтобы передать успешный запрос HTTP GET в API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> Успешный HTTP-запрос *не* обязательно означает, что сам поиск был успешным. Если при выполнении поиска возникает ошибка, то API Bing для поиска изображений возвращает код состояния, отличный от "HTTP: 200", и включает сведения об ошибке в ответ JSON. Кроме того, если запрос имел ограничение по скорости, то API возвращает пустой ответ.

Большая часть кода в обеих описанных выше функциях предназначена для обработки ошибок. Ошибки могут возникать на следующих этапах:

|Этап|Возможные ошибки|Чем обрабатывается|
|-|-|-|
|Создание объекта запроса JavaScript|Недопустимый URL-адрес|Блок `try`/`catch`|
|Выполнение запроса|Ошибки сети, прерванные соединения|Обработчики событий `error` и `abort`|
|Выполнение поиска|Недопустимый запрос, недопустимый JSON-файл, ограничения скорости|Тесты в обработчике событий `load`|

Ошибки обрабатываются путем вызова функции `renderErrorMessage()` со всеми имеющимися сведениями об ошибке. Если ответ передает полный набор тестов ошибок, то мы вызываем функцию `renderSearchResults()` для отображения результатов поиска на странице.

## <a name="displaying-search-results"></a>Отображение результатов поиска

Основная функция отображения результатов поиска — `renderSearchResults()`. Эта функция принимает код JSON, возвращенный службой поиска изображений Bing, а также отображает изображения и связанные с ними поисковые запросы, если они есть.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Основные результаты поиска изображений возвращаются как объекты верхнего уровня `value` в ответе JSON. Мы передаем их в нашу функцию `renderImageResults()`, которая поочередно вызывает для них функцию отображения в формате HTML. Полученный HTML-код возвращается в `renderSearchResults()`, где он вставляется в раздел `results` на странице.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

API Bing для поиска изображений возвращает до четырех различных видов связанных результатов, каждый в своем собственном объекте верхнего уровня. К ним относятся:

|||
|-|-|
|`pivotSuggestions`|Запросы, которые заменяют сводное слово в исходном поиске другим. Например, если вы ищете "красные цветы", сводным словом может быть "красные", а сводным предложением может быть "желтые цветы".|
|`queryExpansions`|Запросы, которые сужают исходный поиск, добавляя больше условий. Например, если вы ищете "Microsoft Surface", расширением запроса может быть "Microsoft Surface Pro".|
|`relatedSearches`|Запросы, которые вводились другими пользователями, использовавшими ранее исходный поиск. Например, если вы ищете "гора Рейнир", может применяться связанный поиск "гора Святой Елены".|
|`similarTerms`|Запросы, похожие по смыслу на исходный запрос. Например, если вы ищете "котята", аналогичным термином может быть "милота".|

Как ранее было показано в `renderSearchResults()`, мы преобразовываем для просмотра только предложения `relatedItems` и размещаем результирующие ссылки на боковой панели страницы.

## <a name="rendering-result-items"></a>Отображение элементов результата

В нашем коде JavaScript есть объект `searchItemRenderers`, который содержит *отрисовщики* — функции, создающие HTML-код для каждого типа результатов поиска.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Функция-отрисовщик может принимать следующие параметры:

| | |
|-|-|
|`item`|Объект JavaScript, содержащий свойства элемента, такие как URL-адрес и его описание.|
|`index`|Индекс элемента результата в коллекции.|
|`count`|Число элементов в коллекции результатов поиска.|

Параметры `index` и `count` можно использовать для нумерации результатов, создания специального HTML-кода для начала или конца коллекции, вставки разрывов строки после определенного числа элементов и т. д. Если от отрисовщика не требуется выполнять эту функцию, то ему не нужно принимать эти два параметра.

Давайте рассмотрим отрисовщик `images` подробнее:

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Наша функция-отрисовщик изображений:

> [!div class="checklist"]
> * вычисляет размер эскиза изображения (переменная ширина — не менее 120 пикселей, и фиксированная высота — 90 пикселей);
> * создает HTML-тег `<img>` для отображения эскиза изображения; 
> * создает HTML-теги `<a>` со ссылкой на изображение и страницей, которая его содержит;
> * создает описание со сведениями об этом изображении и о сайте, на котором оно размещено.

Мы проверяем переменную `index`, чтобы вставить тег `<p>` перед первым изображением из результата. В противном случае эскизы располагаются впритык и переносятся по мере необходимости в окне браузера.

Размер эскиза указывается в теге `<img>` и в полях `h` и `w` URL-адреса эскиза. Затем [служба эскизов Bing](resize-and-crop-thumbnails.md) предоставляет эскиз точно такого размера.

## <a name="persisting-client-id"></a>Сохранение идентификатора клиента

Ответы от интерфейсов API поиска Bing могут содержать заголовок `X-MSEdge-ClientID`, который необходимо отправить обратно в API с последующими запросами. Если используется несколько API-интерфейсов поиска Bing, то желательно использовать для всех интерфейсов один идентификатор клиента.

Наличие заголовка `X-MSEdge-ClientID` позволяет API-интерфейсам Bing связывать все поисковые запросы пользователя, а это дает два важных преимущества.

Во-первых, поисковая система Bing может применять к операциям поиска прошлый контекст и находить результаты, которые лучше соответствуют требованиям пользователя. Если пользователь ранее уже вводил поисковые запросы, например, связанные с мореплаванием под парусом, то при последующем поиске по слову "морские узлы" в приоритетном порядке могут возвращаться сведения об узлах, которые используются в мореплавании.

Во-вторых, Bing может случайным образом выбирать пользователей для опробования новых возможностей, прежде чем делать их общедоступными. Предоставление одного и того же идентификатора клиента при каждом запросе гарантирует, что пользователи, которым был открыт доступ к определенному компоненту, всегда будут иметь к нему доступ. Без идентификатора клиента такой компонент может появляться и исчезать в результатах поиска пользователя как бы случайным образом.

Из-за политик безопасности браузера (CORS) заголовок `X-MSEdge-ClientID` может быть недоступным для кода JavaScript. Это ограничение возникает, когда ответ поиска и страница, его запросившая, имеют разные источники. В рабочей среде такая проблема с политикой решается путем размещения серверного скрипта, который выполняет вызов API, на одном домене с веб-страницей. Так как скрипт будет иметь тот же источник, что и веб-страница, заголовок `X-MSEdge-ClientID` станет доступным для JavaScript.

> [!NOTE]
> В рабочем веб-приложении запрос следует всегда выполнять на стороне сервера. В противном случае вам придется включать в веб-страницу ключ API поиска Bing, где он будет доступен для всех, кто просматривает исходный код. С вас будет взиматься плата за любое использование ресурсов в рамках вашего ключа подписки API, включая запросы, выполненные неавторизованными сторонами, поэтому важно не предоставлять доступ к своему ключу.

В целях разработки запрос к API Bing для поиска в Интернете можно выполнить через прокси-сервер CORS. Ответ от прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит нашему учебному приложению иметь доступ к заголовку идентификатора клиента. Для начала [установите платформу Node.js](https://nodejs.org/en/download/), если она еще не установлена. Затем введите следующую команду в командном окне:

    npm install -g cors-proxy-server

После этого в HTML-файле измените конечную точку службы "Поиск в Интернете Bing" на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Это приведет к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по API Bing для поиска изображений](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

