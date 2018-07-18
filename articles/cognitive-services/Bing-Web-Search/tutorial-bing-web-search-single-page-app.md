---
title: Одностраничное веб-приложение для службы "Поиск в Интернете Bing" | Документация Майкрософт
description: В этой статье показано, как использовать API Bing для поиска в Интернете в одностраничном веб-приложении.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382160"
---
# <a name="tutorial-single-page-web-app"></a>Руководство по одностраничным веб-приложениям

API Bing для поиска в Интернете позволяет выполнять поиск в Интернете и получать результаты различных типов, релевантных поисковому запросу. В этом руководстве мы создаем одностраничное веб-приложение, использующее API Bing для поиска в Интернете для отображения результатов поиска прямо на странице. Приложение включает в себя компоненты HTML, CSS и JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> При нажатии заголовков JSON и HTTP в нижней части страницы отображаются сведения об ответе JSON и HTTP-запросе. Эти данные позволяют лучше изучить службу.

Учебное приложение иллюстрирует приведенные ниже задачи.

> [!div class="checklist"]
> * Вызов API Bing для поиска в Интернете в JavaScript.
> * Передача параметров поиска в API Bing для поиска в Интернете.
> * Отображение результатов поиска в Интернете, новостях, изображениях и видео.
> * Переход по страницам результатов поиска.
> * Обработка идентификатора клиента Bing и ключа подписки API.
> * Обработка ошибок, которые могут возникнуть.

Страница руководства является полностью автономной; на ней не используется никаких внешних платформ, таблиц стилей и даже файлов изображений. Она использует только широко распространенные функции языка JavaScript и работает с текущими версиями всех основных браузеров.

В этом руководстве затрагиваются только отдельные части исходного кода. Полный исходный код доступен на [отдельной странице](tutorial-bing-web-search-single-page-app-source.md). Скопируйте и вставьте этот код в текстовый редактор и сохраните его как файл `bing.html`.

## <a name="app-components"></a>Компоненты приложения

Как и любое другое одностраничное веб-приложение, это учебное приложение состоит из трех частей:

> [!div class="checklist"]
> * HTML — определяет структуру и содержимое страницы;
> * CSS — определяет внешний вид страницы;
> * JavaScript — определяет поведение страницы.

В этом руководстве большинство аспектов работы с HTML и CSS не описывается подробно, так как они довольно просты.

HTML-код содержит форму поиска, в которой пользователь вводит запрос и выбирает параметры поиска. Эта форма связана с языком JavaScript, который фактически и выполняет поиск по атрибуту `onsubmit` тега `<form>`:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

Обработчик `onsubmit` возвращает значение `false`, которое препятствует отправке формы на сервер. Код JavaScript фактически осуществляет сбор необходимой информации из формы и выполняет поиск.

HTML также содержит разделы (теги `<div>` HTML), где отображаются результаты поиска.

## <a name="managing-subscription-key"></a>Управление ключом подписки

Чтобы не включать в код ключ подписки для API поиска Bing, мы используем для его хранения постоянное хранилище браузера. Если ключа нет в хранилище, мы запрашиваем ключ пользователя и сохраняем его для последующего использования. Если на дальнейшем этапе ключ отклоняется API, мы аннулируем сохраненный ключ. В результате пользователю предлагается запросить ключ еще раз.

Мы определяем функции `storeValue` и `retrieveValue`, которые используют объект `localStorage` (если браузер поддерживает его) или файл cookie. Наша функция `getSubscriptionKey()` использует эти функции для хранения и извлечения ключа пользователя.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

HTML-тег `form` `onsubmit` вызывает функцию `bingWebSearch`, чтобы возвращать результаты поиска. `bingWebSearch` использует `getSubscriptionKey` для проверки подлинности каждого запроса. Как показано в предыдущем определении, `getSubscriptionKey` запрашивает у пользователя ключ, если он не был введен. Затем ключ сохраняется для последующего использования приложением.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Выбор параметров поиска

![[Форма API Bing для поиска в Интернете]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

HTML-форма включает элементы со следующими именами:

| | |
|-|-|
| `where` | Раскрывающееся меню для выбора рынка (расположения и языка), который используется для поиска. |
| `query` | Текстовое поле для ввода условий поиска. |
| `what` | Флажки для повышения уровня определенных видов результатов. Например, повышение уровня изображений увеличивает их ранжирование. |
| `when` | Раскрывающееся меню для необязательного ограничения поиска результатами за последний день, неделю или месяц. |
| `safe` | Флажок, указывающий, следует ли использовать функцию безопасного поиска Bing для фильтрации результатов с материалами для взрослых. |
| `count` | Скрытое поле. Количество результатов поиска, которые должны возвращаться при каждом запросе. Измените, чтобы на странице отображалось меньше или больше результатов. |
| `offset` | Скрытое поле. Смещение первого результата поиска в запросе; используется для разбиения по страницам. Сбрасывается к `0` при новом запросе. |

> [!NOTE]
> Поиск в Интернете Bing предоставляет много дополнительных параметров запроса. Здесь мы используем только некоторые из них.

Функция JavaScript `bingSearchOptions()` преобразует эти поля в формат, требуемый API поиска Bing.

```javascript
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
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Например, параметр `SafeSearch` в действительном вызове API может иметь значение `strict`, `moderate` или `off`, при этом `moderate` является значением по умолчанию. Тем не менее наша форма использует флажок, который имеет только два состояния. Код JavaScript преобразует это значение в `strict` или `off` (`moderate` не используется).

Если отмечены какие-либо флажки **Promote** (Повышение уровня), мы также добавим к запросу параметр `answerCount`. `answerCount` необходим при использовании параметра `promote`. Мы просто задаем для него значение `9` (количество типов результатов, поддерживаемых API Bing для поиска в Интернете), чтобы убедиться, что мы получаем максимально возможное количество типов результатов.

> [!NOTE]
> Повышение уровня результата *не гарантирует*, что в результатах поиска будет результат такого вида. Оно, скорее всего, повышает оценку этого типа результатов по сравнению со стандартной. Чтобы ограничить поиск конкретными видами результатов, используйте параметр запроса `responseFilter` или вызовите более конкретную конечную точку, например API Bing для поиска изображений или API Bing для поиска новостей.

Мы также отправляем параметры запроса`textDecoration` и `textFormat`, чтобы поисковой запрос выделялся жирным шрифтом в результатах поиска. Эти значения жестко заданы в сценарии.

## <a name="performing-the-request"></a>Выполнение запроса

Учитывая запрос, строку параметров и ключ API, функция `BingWebSearch` использует объект `XMLHttpRequest`, чтобы сделать запрос к конечной точке Поиска в Интернете Bing.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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

После успешного выполнения HTTP-запроса JavaScript вызывает обработчик событий `load`, функцию `handleBingResponse()`, чтобы предать успешный запрос HTTP GET в API. 

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> Успешный HTTP-запрос *не* обязательно означает, что сам поиск был успешным. Если во время выполнения операции поиска возникает ошибка, то API Bing для поиска в Интернете возвращает код состояния, отличный от "HTTP: 200", и включает сведения об ошибке в ответ JSON. Кроме того, если запрос имел ограничение по скорости, то API возвращает пустой ответ.

Большая часть кода в обоих описанных выше функциях предназначена для обработки ошибок. Ошибки могут возникать на следующих этапах:

|Этап|Возможные ошибки|Чем обрабатывается|
|-|-|-|
|Создание объекта запроса JavaScript|Недопустимый URL-адрес|Блок `try`/`catch`|
|Выполнение запроса|Ошибки сети, прерванные соединения|Обработчики событий `error` и `abort`|
|Выполнение поиска|Недопустимый запрос, недопустимый JSON-файл, ограничения скорости|Тесты в обработчике событий `load`|

Ошибки обрабатываются путем вызова функции `renderErrorMessage()` со всеми имеющимися сведениями об ошибке. Если ответ передает полный набор тестов ошибок, то мы вызываем функцию `renderSearchResults()` для отображения результатов поиска на странице.

## <a name="displaying-search-results"></a>Отображение результатов поиска

Для API Bing для поиска в Интернете [требуется, чтобы результаты отображались в указанном порядке](useanddisplayrequirements.md). Так как API может возвращать различные типы ответа, недостаточно выполнить итерацию через коллекцию `WebPages` верхнего уровня в ответе JSON и отобразить полученные результаты. (Если вы хотите получить только один вид результатов, используйте параметр запроса `responseFilter` или другую конечную точку Поиска Bing).

Вместо этого мы используем `rankingResponse` в результатах поиска, чтобы упорядочить результаты для отображения. Этот объект ссылается на элементы в коллекциях `WebPages` `News`, `Images` и/или `Videos`, или в других коллекциях ответов верхнего уровня в ответе JSON.

`rankingResponse` может содержать до трех коллекций результатов поиска, назначенных как `pole`, `mainline` и `sidebar`. 

`pole` (если есть) — это самый релевантный результат поиска, и его необходимо отображать в приоритетном порядке. `mainline` ссылается на основную часть результатов поиска. Результаты mainline необходимо отображать сразу после результатов `pole` (или первыми, если `pole` отсутствуют). 

И наконец. `sidebar` ссылается на вспомогательные результаты поиска. Часто это связанные поисковые запросы и изображения. Если возможно, эти результаты должны отображаться на фактической боковой панели. Если из-за ограничений экрана использование боковой панели становится нецелесообразным (например, на мобильном устройстве), они должны располагаться после результатов `mainline`.

Каждый элемент в коллекции `rankingResponse` ссылается на фактические элементы результатов поиска двумя разными, но похожими способами.

| | |
|-|-|
|`id`|Элемент `id` выглядит как URL-адрес, но он не должен использоваться для ссылок. Тип `id` результата ранжирования совпадает с `id` элемента результатов поиска в коллекции ответов *или* со всей коллекцией ответов (например `Images`).
|`answerType`, `resultIndex`|`answerType` ссылается на коллекцию ответов верхнего уровня, содержащую результат (например `WebPages`). `resultIndex` ссылается на индекс результата в этой коллекции. Если `resultIndex` отсутствует, то результат ранжирования ссылается на всю коллекцию.

> [!NOTE]
> Дополнительные сведения об этой части ответа поиска см. в статье [Using ranking to display results](rank-results.md) (Использование рейтинга для отображения результатов).

Вы можете использовать любой метод расстановки элемента в результатах поиска, который наилучшим образом подходит для вашего приложения. В примере кода в этом руководстве мы используем `answerType` и `resultIndex` для расстановки каждого результата поиска.

Вот и пришло время взглянуть на нашу функцию `renderSearchResults()`. Эта функция выполняет итерацию по трем коллекциям `rankingResponse`, которые предоставляют три раздела результатов поиска. Для каждого раздела мы вызываем функцию `renderResultsItems()`, чтобы преобразовать результаты для просмотра для этого раздела.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()`, в свою очередь, выполняет перебор элементов в каждом разделе `rankingResponse`, сопоставляет каждый результат ранжирования с результатом поиска с использованием полей `answerType` и `resultIndex` и вызывает соответствующую функцию рендеринга для генерации HTML-кода результата. 

Если для данного элемента ранжирования `resultIndex` не указан, `renderResultsItems()` выполняет итерацию по всем результатам этого типа и вызывает функцию рендеринга для каждого элемента. 

В любом случае полученный HTML вставляется в соответствующий элемент `<div>` на странице.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Рендеринг элементов результатов

В нашем коде JavaScript есть объект `searchItemRenderers`, который содержит *отрисовщики*: функции, создающие HTML-код для каждого типа результатов поиска.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> В нашем учебном приложении есть отрисовщики для веб-страниц, новостей, изображений, видео и связанных с ними поисковых запросов. Вашему собственному приложению нужны отрисовщики для любого вида полученных результатов, которые могут включать вычисления, предложения орфографии, сущности, часовые пояса и определения.

Некоторые из наших функций рендеринга принимают только параметр `item`. Это объект JavaScript, который представляет собой единственный результат поиска. Другие принимают дополнительные параметры, которые могут использоваться для преобразования элементов для просмотра по-разному в разных контекстах. (Отрисовщик, который не использует эту информацию, не должен принимать эти параметры).

Контекстные аргументы:

| | |
|-|-|
|`section`|Раздел результатов (`pole`, `mainline` или `sidebar`), в котором содержится элемент.
|`index`<br>`count`|Доступен, когда элемент `rankingResponse` указывает, что все результаты в данной коллекции должны отображаться. В противном случае `undefined`. Эти параметры получают индекс элемента и общее число элементов коллекции. Вы можете использовать эту информацию для подсчета результатов, для генерации разных HTML для первого или последнего результата и т. д.|

В нашем учебном приложении отрисовщики `images` и `relatedSearches` используют контекстные аргументы для настройки создаваемого HTML-кода. Давайте рассмотрим отрисовщик `images` подробнее:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Наша функция-отрисовщик изображений:

> [!div class="checklist"]
> * Вычисляет размер эскиза изображения (ширина изменяется, а высота фиксируется на 60 пикселях).
> * Вставляет HTML, предшествующий результату изображения в зависимости от контекста.
> * Создает HTML-тег `<a>`, позволяющий перейти к странице, содержащей это изображение.
> * Создает HTML-тег `<img>` для отображения эскиза изображения. 

Отрисовщик изображений использует переменные `section` и `index`, чтобы отобразить результаты по-разному, в зависимости от того, где они отображаются. Разрыв строки (тег `<br>`) вставляется между результатами изображения на боковой панели, чтобы в боковой панели изображения отображались в столбце. В других разделах первый результат изображения `(index === 0)` предшествует тегу `<p>`. В противном случае эскизы располагаются впритык и переносятся по мере необходимости в окне браузера.

Размер эскиза используется как в тегах `<img>`, так и в полях `h` и `w` в URL-адресе эскиза. Затем [служба эскизов Bing](resize-and-crop-thumbnails.md) предоставляет эскиз точно такого размера. Атрибуты `title` и `alt` (текстовое описание изображения) формируются на основе имени изображения и имени узла в URL-адресе.

Изображения появляются, как показано ниже, в результатах поиска основного поля.

![[Результаты изображения Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>Сохранение идентификатора клиента

Ответы от интерфейсов API поиска Bing могут содержать заголовок `X-MSEdge-ClientID`, который необходимо отправить обратно в API с последующими запросами. Если используется несколько API-интерфейсов поиска Bing, то по возможности со всеми ими необходимо использовать один идентификатор клиента.

Наличие заголовка `X-MSEdge-ClientID` позволяет интерфейсам API Bing связывать все поисковые запросы пользователя, а это дает два важных преимущества.

Во-первых, поисковая система Bing может применять к операциям поиска прошлый контекст и находить результаты, которые лучше соответствуют потребностям пользователя. Если пользователь ранее уже вводил поисковые запросы, например, связанные с мореплаванием под парусом, то при последующем поиске по слову "knots" в приоритетном порядке могут возвращаться сведения об узлах, которые используются в мореплавании.

Во-вторых, Bing может случайным образом выбирать пользователей для опробования новых возможностей, прежде чем делать их общедоступными. Предоставление одного и того же идентификатора клиента при каждом запросе гарантирует, что пользователи, которым был открыт доступ к определенному компоненту, всегда будут иметь к нему доступ. Без идентификатора клиента такой компонент может появляться и исчезать в результатах поиска пользователя как бы случайным образом.

Из-за политик безопасности браузера (CORS) заголовок `X-MSEdge-ClientID` может быть недоступным для кода JavaScript. Это ограничение возникает, когда ответ поиска и страница, его запросившая, имеют разные источники. В рабочей среде такая проблема с политикой решается путем размещения серверного сценария, который выполняет вызов API, на одном домене с веб-страницей. Так как такой сценарий будет иметь тот же источник, что и веб-страница, заголовок `X-MSEdge-ClientID` станет доступным для JavaScript.

> [!NOTE]
> В рабочем веб-приложении все равно необходимо выполнить запрос на стороне сервера. В противном случае ваш ключ API-интерфейса поиска Bing должен быть включен в веб-страницу, где он будет доступен для всех, кто просматривает источник. С вас будет взиматься плата за любое использование ресурсов в рамках вашего ключа подписки API, включая запросы, выполненные неавторизованными сторонами, поэтому важно не предоставлять доступ к своему ключу.

В целях разработки запрос API Bing для поиска в Интернете можно выполнить через прокси-сервер CORS. Ответ с такого прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит нашему учебному приложению иметь доступ к заголовку идентификатора клиента. Для начала [установите платформу Node.js](https://nodejs.org/en/download/), если она еще не установлена. Затем введите следующую команду в командном окне:

    npm install -g cors-proxy-server

Затем в HTML-файле измените конечную точку службы "Поиск в Интернете Bing" на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Это приведет к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство для мобильного приложения для визуального поиска](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Web Search API v7 reference](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) (Справка по API Bing для поиска в Интернете версии 7)
