---
title: Руководство по передаче изображения в Bing Visual Search | Документация Майкрософт
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Описание процесса передачи изображения в службу Bing для получения полезных сведений о нем, а также для его анализа и отображения ответа.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 07/10/2018
ms.author: scottwhi
ms.openlocfilehash: 1352ccbcda35c693c5ac0b36156af199ae46bee9
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068674"
---
# <a name="tutorial-breaking-down-bing-visual-search-upload"></a>Пошаговое руководство по передаче изображения в Bing Visual Search

В этом руководстве подробно описывается передача изображения в службу Bing и получение полезных сведений о нем. В нем также показано, как получить доступ полезным сведениям в ответе JSON и отобразить их. Полный пример HTML и JavaScript приведен в разделе [Полный код](#complete-code).

Это руководство предназначено для разработчика, который хочет изучить содержимое ответа Bing Visual Search. Оно не охватывает все варианты использования и требования к отображению (например, в нем нет ссылки на политику конфиденциальности корпорации Майкрософт). Все требования к использованию приведены в разделе [Требования к использованию и отображению API поиска Bing](./use-and-display-requirements.md).


## <a name="where-to-start"></a>Как начать работу?

Давайте начнем с HTML-страницы, которая отправляет изображение в службу Bing, получает полезные сведения и отображает их. В привычном редакторе создайте файл uploaddemo.html. Добавьте в него приведенную ниже базовую структуру HTML.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>      
```

Сначала давайте разделим страницу на раздел запроса, где пользователь предоставляет все сведения, необходимые для выполнения запроса, и раздел ответа, где отображаются полезные сведения. Добавьте следующие теги \<div\> в раздел \<body\>. Тег \<hr\> визуально отделяет раздел запроса от раздела ответа.

```html
        <div id="requestSection"></div>

        <hr />
        
        <div id="responseSection"></div>
```

## <a name="get-the-file-to-upload"></a>Получение файла для передачи

Чтобы пользователь мог выбрать изображение для передачи, в демоверсии используется тег \<input\> с атрибутом типа file. Пользовательский интерфейс должен дать понять, что демоверсия использует Bing для получения результатов поиска. 

Добавьте следующий тег \<div\> в тег div requestSection. Элемент ввода файлов принимает один файл любого типа изображения (например, JPG, GIF, PNG). Событие `onchange` указывает обработчик, который вызывается, когда пользователь выбирает файл.

Тег \<output\> используется для отображения эскиза выбранного изображения.


```html
            <div>
                <p>Select image to get insights from Bing:
                    <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
                </p>

                <output id="thumbnail"></output>
            </div>
```

Прежде чем добавить обработчик, добавьте тег \<script\> в тег \<head\>.

```html
        <script>
        <\script>
```

Ниже показан обработчик, который записывает выбранное изображение. Он содержит логику, которая проверяет, является ли выбранный файл изображением, размер которого не превышает 1 МБ. Можно разрешить пользователю выбирать файлы большего размера, но тогда потребуется уменьшать размер файлов, чтобы он не превышал 1 МБ, прежде чем отправить файлы в Bing. Последнее, что делает обработчик, — отображает эскиз изображения, чтобы у пользователя было визуальное напоминание о файле, который он выбрал.

```javascript
        function handleFileSelect(selector) {
            var files = document.getElementById(selector).files; // FileList object

            // The list will contain only one file.
            for (var i = 0, f; f = files[i]; i++) {
      
                // Only process image files.
                if (!f.type.match('image.*')) {
                    alert("Selected file must be an image file.");
                    document.getElementById("uploadImage").value = null;
                    continue;
                }
      
                // Image must be <= 1 MB and should be about 1500px.
                if (f.size > 1000000) {
                    alert("Image must be less than 1 MB.");
                    document.getElementById("uploadImage").value = null;
                    continue;
                }


                var reader = new FileReader();
      
                // Capture the file information.
                reader.onload = (function(theFile) {
                    return function(e) {
                        var fileOutput = document.getElementById('thumbnail');

                        if (fileOutput.childElementCount > 0) {
                            fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                        }

                        // Render thumbnail.
                        var span = document.createElement('span');
                        span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                          '" title="', escape(theFile.name), '"/>'].join('');
                        fileOutput.insertBefore(span, null);
                    };
                })(f);
      
                // Read in the image file as a data URL.
                reader.readAsDataURL(f);
            }
        }
```


## <a name="what-else-is-needed-before-making-the-call-to-bing"></a>Что еще необходимо сделать перед отправкой вызова к Bing?

Демоверсии по-прежнему требуется ключ подписки. На практике вы, скорее всего, получите ключ подписки из защищенного хранилища, но ввиду простоты этой демоверсии его потребуется указать в пользовательском интерфейсе. Добавьте приведенный ниже тег \<input\> (с атрибутом типа text) в раздел \<body\> сразу же после тега \<output\>.

```html
        <div>
            <p>Subscription key: 
                <input type="text" id="key" name="subscription" size=40 maxlength="32" />
            </p>
        </div>
```

С помощью изображения и ключа подписки можно отправить вызов к Bing Visual Search, чтобы получить полезные сведения об этом изображении. Для вызова будут использованы значения рынка и безопасного поиска по умолчанию (en-us и moderate соответственно).

В этой демоверсии пользователю предоставляется возможность изменить эти значения. Добавьте следующий тег \<div\> под тегом div ключа подписки. Демоверсия использует тег \<select\>, чтобы отобразить раскрывающиеся списки для выбора значений рынка и безопасного поиска. Оба списка отображают значение по умолчанию Bing.

 
```html
        <div>
            <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

            <div id="options" style="display:none">
                <p style="margin-left: 20px">Market: 
                    <select id="mkt">
                        <option value="es-AR">Argentina (Spanish)</option>
                        <option value="en-AU">Australia (English)</option>
                        <option value="de-AT">Austria (German)</option>
                        <option value="nl-BE">Belgium (Dutch)</option>
                        <option value="fr-BE">Belgium (French)</option>
                        <option value="pt-BR">Brazil (Portuguese)</option>
                        <option value="en-CA">Canada (English)</option>
                        <option value="fr-CA">Canada (French)</option>
                        <option value="es-CL">Chile (Spanish)</option>
                        <option value="da-DK">Denmark (Danish)</option>
                        <option value="fi-FI">Finland (Finnish)</option>
                        <option value="fr-FR">France (French)</option>
                        <option value="de-DE">Germany (German)</option>
                        <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
                        <option value="en-IN">India (English)</option>
                        <option value="en-ID">Indonesia (English)</option>
                        <option value="it-IT">Italy (Italian)</option>
                        <option value="ja-JP">Japan (Japanese)</option>
                        <option value="ko-KR">Korea (Korean)</option>
                        <option value="en-MY">Malaysia (English)</option>
                        <option value="es-MX">Mexico (Spanish)</option>
                        <option value="nl-NL">Netherlands (Dutch)</option>
                        <option value="en-NZ">New Zealand (English)</option>
                        <option value="no-NO">Norway (Norwegian)</option>
                        <option value="zh-CN">People's Republic of China (Chinese)</option>
                        <option value="pl-PL">Poland (Polish)</option>
                        <option value="pt-PT">Portugal (Portuguese)</option>
                        <option value="en-PH">Philippines (English)</option>
                        <option value="ru-RU">Russia (Russian)</option>
                        <option value="ar-SA">Saudi Arabia (Arabic)</option>
                        <option value="en-ZA">South Africa (English)</option>
                        <option value="es-ES">Spain (Spanish)</option>
                        <option value="sv-SE">Sweden (Swedish)</option>
                        <option value="fr-CH">Switzerland (French)</option>
                        <option value="de-CH">Switzerland (German)</option>
                        <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                        <option value="tr-TR">Turkey (Turkish)</option>
                        <option value="en-GB">United Kingdom (English)</option>
                        <option value="en-US" selected>United States (English)</option>
                        <option value="es-US">United States (Spanish)</option>
                    </select>
                </p>
                <p style="margin-left: 20px">Safe search: 
                    <select id="safesearch">
                        <option value="moderate" selected>Moderate</option>
                        <option value="strict">Strict</option>
                        <option value="off">off</option>
                    </select>
                </p>
            </div>
        </div>
```

Демоверсия скрывает эти списки в свертываемом элементе div, которым можно управлять с помощью ссылки "Query options" (Параметры запроса). Если щелкнуть ссылку "Query options" (Параметры запроса), элемент div развернется и вы можете просмотреть и изменить параметры запроса. Если еще раз щелкнуть ссылку "Query options" (Параметры запроса), элемент div свернется, скрыв параметры. Ниже показан обработчик onclick ссылки "Query options" (Параметры запроса). Этот обработчик управляет сворачиванием и разворачиванием элемента div. Добавьте этот обработчик в раздел \<script\>. Обработчик использует все свертываемые элементы div в демоверсии.

```javascript
        // Contains the toggle state of divs.
        var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


        // Toggles between showing and hiding the specified div.
        function expandCollapse(divToToggle) {
            var div = document.getElementById(divToToggle);

            if (divToggleMap[divToToggle] == 1) {   // if div is expanded
                div.style.display = "none";
                divToggleMap[divToToggle] = 0;
            }
            else {                                  // if div is collapsed
                div.style.display = "inline-block";
                divToggleMap[divToToggle] = 1;
            }
        }
```


## <a name="making-the-call"></a>Отправка вызова

Добавьте в раздел body под элементом div приведенную ниже кнопку "Get insights" (Получить сведения). С ее помощью пользователь сможет инициировать вызов. Когда пользователь нажимает эту кнопку, курсор изменяется на вращающийся курсор ожидания и вызывается обработчик onclick.

```html
        <p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Добавьте обработчик onclick кнопки в тег \<script\>. Этот обработчик гарантирует наличие ключа подписки, содержащего 32 знака, а также выбранного изображения. Он также очищает полезные сведения из предыдущего запроса. Если ошибки отсутствуют, он вызывает функцию sendRequest для отправки вызова.

```javascript
        function handleQuery() {
            var subscriptionKey = document.getElementById('key').value;

            // Make sure user provided a subscription key and image.
            // For this demo, the user provides the key but typically you'd 
            // get it from secured storage.
            if (subscriptionKey.length !== 32) {
                alert("Subscription key length is not valid. Enter a valid key.");
                document.getElementById('key').focus();
                return;
            }

            var imagePath = document.getElementById('uploadImage').value;

            if (imagePath.length === 0)
            {
                alert("Please select an image to upload.");
                document.getElementById('uploadImage').focus();
                return;
            }

            var responseDiv = document.getElementById('responseSection');

            // Clear out the response from the last query.
            while (responseDiv.childElementCount > 0) {
                responseDiv.removeChild(responseDiv.lastChild);
            }

            // Send the request to Bing to get insights about the image.
            var f = document.getElementById('uploadImage').files[0];
            sendRequest(f, subscriptionKey);
        }
```

Функция sendRequest форматирует URL-адрес конечной точки, задает заголовок Ocp-Apim-Subscription-Key для ключа подписки, добавляет двоичный файл изображения для передачи, задает обработчик ответа и выполняет вызов. 

```javascript
        function sendRequest(file, key) {
            var market = document.getElementById('mkt').value;
            var safeSearch = document.getElementById('safesearch').value;
            var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

            var form = new FormData();
            form.append("image", file);

            var request = new XMLHttpRequest();

            request.open("POST", baseUri);
            request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
            request.addEventListener('load', handleResponse);
            request.send(form);
        }
```

## <a name="handling-the-response"></a>Обработка ответа

Функция handleResponse обрабатывает ответ на вызов к Bing Visual Search. Если вызов завершен успешно, она анализирует ответ JSON, выделяя в нем отдельные теги, которые содержат полезные сведения. Затем она добавляет строку (результаты поиска в Интернете Bing) на страницу, чтобы сообщить пользователю, что данные поступили Bing.

Демоверсия может создать дамп всех полезных данных на странице, но некоторые изображения возвращают много данных, которые трудно использовать. Вместо этого демоверсия создает свертываемые элементы div для каждого тега, чтобы пользователь мог управлять отображаемым объемом данных.

Добавьте обработчик в раздел \<script\>.

```javascript
        function handleResponse() {
            if(this.status !== 200){
                alert("Error calling Bing Visual Search. See console log for details.");
                console.log(this.responseText);
                return;
            }

            var tags = parseResponse(JSON.parse(this.responseText));
            var h4 = document.createElement('h4');
            h4.textContent = 'Bing internet search results';
            document.getElementById('responseSection').appendChild(h4);
            buildTagSections(tags);

            document.body.style.cursor = 'default'; // reset the wait curor set by query insights button
        }

        function parseResponse(json) {
            var dict = {};

            for (var i =0; i < json.tags.length; i++) {
                var tag = json.tags[i];
             
                if (tag.displayName === '') {
                    dict['Default'] = JSON.stringify(tag);
                }
                else {
                    dict[tag.displayName] = JSON.stringify(tag);
                }
            }

            return(dict);
        }
```

Функция buildTagSections итеративно обрабатывает проанализированные теги JSON и вызывает функцию buildDiv для создания элемента div для каждого тега. Как и "Query options" (Параметры запроса), каждый тег отображается как ссылка. Когда пользователь щелкает ссылку, тег разворачивается, отображая связанные полезные сведения. Если пользователь щелкнет ссылку еще раз, раздел свернется, скрыв полезные данные от пользователя.

```javascript
        function buildTagSections(tags) {
            for (var tag in tags) {
                if (tags.hasOwnProperty(tag)) {
                    var tagSection = buildDiv(tags, tag);
                    document.getElementById('responseSection').appendChild(tagSection);
                }
            }  
        }


        function buildDiv(tags, tag) {
            var tagSection = document.createElement('div');
            tagSection.setAttribute('class', 'subSection');

            var link = document.createElement('a');
            link.setAttribute('href', '#');
            link.setAttribute('style', 'float: left;')
            link.text = tag;
            tagSection.appendChild(link);

            var contentDiv = document.createElement('div');
            contentDiv.setAttribute('id', tag);
            contentDiv.setAttribute('style', 'clear: left;')
            contentDiv.setAttribute('class', 'section');
            tagSection.appendChild(contentDiv);

            link.setAttribute('onclick', `expandCollapse("${tag}")`);
            divToggleMap[tag] = 0;  // 1 = show, 0 = hide

            addDivContent(contentDiv, tag, tags[tag]);

            return tagSection;
        }
```

Функция buildDiv вызывает функцию addDivContent для создания содержимого свертываемого элемента div для каждого тега.

Содержимое тега включает в себя код JSON из ответа. Демоверсия содержит JSON для разработчиков, которые хотят просмотреть код JSON из ответа. Первоначально отображаются только первые 100 знаков JSON, но можно щелкнуть строку JSON, чтобы отобразить весь код. Если щелкнуть ее еще раз, строка JSON свернется до 100 знаков.

Добавьте типы действий, найденные в теге. Для каждого типа действия следует вызывать различные функции, чтобы добавить его полезные сведения.

```javascript
        function addDivContent(div, tag, json) {

            // Adds the first 100 characters of the json that contains
            // the tag's data. The user can click the text to show the
            // full json. They can click it again to collapse the json.
            var para = document.createElement('p');
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
            para.setAttribute('style', 'cursor: pointer;')
            para.setAttribute('data-json', json);
            para.addEventListener('click', function(e) {
                var json = e.target.getAttribute('data-json');

                if (e.target.textContent.length <= 103) {  // 100 + '...'
                    e.target.textContent = json;
                    para.setAttribute('title', 'click to collapse');
                }
                else {
                    para.textContent = String(json).substr(0, 100) + '...';
                    para.setAttribute('title', 'click to expand');
                }
            });
            div.appendChild(para); 

            var parsedJson = JSON.parse(json);

            // Loop through all the actions in the tag and display them.
            for (var j = 0; j < parsedJson.actions.length; j++) {
                var action = parsedJson.actions[j];

                var subSectionDiv = document.createElement('div');
                subSectionDiv.setAttribute('class', 'subSection');
                div.appendChild(subSectionDiv);

                var h4 = document.createElement('h4');
                h4.innerHTML = action.actionType;
                subSectionDiv.appendChild(h4);

                if (action.actionType === 'ImageResults') {
                    addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
                }
                else if (action.actionType === 'DocumentLevelSuggestions') {
                    addRelatedSearches(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'RelatedSearches') {
                    addRelatedSearches(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'PagesIncluding') {
                    addPagesIncluding(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'VisualSearch') {
                    addRelatedImages(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'Recipes') {
                    addRecipes(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'ShoppingSources') {
                    addShopping(subSectionDiv, action.data.offers);
                }
                else if (action.actionType === 'ProductVisualSearch') {
                    addProducts(subSectionDiv, action.data.value);
                }
                else if (action.actionType === 'TextResults') {
                    addTextResult(subSectionDiv, action);
                }
                else if (action.actionType === 'Entity') {
                    addEntity(subSectionDiv, action);
                }
            }
        }
```

Ниже приведены все функции, которые отображают полезные сведения для различных действий. Большая часть этих функций проста в использовании. Они предоставляют изображение или ссылку, которую можно щелкнуть, чтобы перейти на веб-страницу, где можно получить дополнительные сведения об этом изображении (Bing.com или веб-страница узла изображения). В этом руководства не отображаются все данные, связанные с полезными сведениями. Чтобы просмотреть все доступные поля полезных сведений, ознакомьтесь со [справочником по Bing Visual Search](https://aka.ms/bingvisualsearchreferencedoc).

Помните, что следует отображать минимальный объем данных, а решение об отображении оставшихся данных принимает пользователь. Чтобы убедиться, что требования к соответствию соблюдены, ознакомьтесь с разделом [Требования к использованию и отображению API поиска Bing](./use-and-display-requirements.md).


```javascript
        function addRelatedImages(div, images) {
            var length = (images.length > 10) ? 10 : images.length;

            // Set the title to the website that hosts the image. The title displays 
            // when the user hovers over the image. 

            // Make the image clickable. If the user clicks the image, they're taken
            // to the image in Bing.com.

            for (var j = 0; j < length; j++) {
                var img = document.createElement('img');
                img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
                img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
                img.setAttribute('title', images[j].hostPageDisplayUrl);
                img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

                img.addEventListener('click', function(e) {
                    var url = e.target.getAttribute('data-webSearchUrl');
                    window.open(url, 'foo');
                })

                div.appendChild(img);
            }
        }


        // Display links to the first 5 webpages that include the image.
        // TODO: Add 'more' link in case the user wants to see all of them.
        function addPagesIncluding(div, pages) {
            var length = (pages.length > 5) ? 5 : pages.length;

            for (var j = 0; j < length; j++) {
                var page = document.createElement('a');
                page.text = pages[j].name;
                page.setAttribute('href', pages[j].hostPageUrl);
                page.setAttribute('style', 'margin: 20px 20px 0 0');
                page.setAttribute('target', '_blank')
                div.appendChild(page);

                div.appendChild(document.createElement('br'));
            }
        }


        // Display the first 10 related searches. Include a link with the image
        // that when clicked, takes the user to Bing.com and displays the 
        // related search results.
        // TODO: Add 'more' link in case the user wants to see all of them.
        function addRelatedSearches(div, relatedSearches) {
            var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

            for (var j = 0; j < length; j++) {
                var childDiv = document.createElement('div');
                childDiv.setAttribute('class', 'stackLink');
                div.appendChild(childDiv);

                var img = document.createElement('img');
                img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
                img.setAttribute('style', 'margin: 20px 20px 0 0;');
                childDiv.appendChild(img);

                var relatedSearch = document.createElement('a');
                relatedSearch.text = relatedSearches[j].displayText;
                relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
                relatedSearch.setAttribute('target', '_blank');
                childDiv.appendChild(relatedSearch);

            }
        }


        // Display links to the first 10 recipes. Include the recipe's rating,
        // if available. 
        // TODO: Add 'more' link in case the user wants to see all of them.
        function addRecipes(div, recipes) {
            var length = (recipes.length > 10) ? 10 : recipes.length;

            for (var j = 0; j < length; j++) {
                var para = document.createElement('p');

                var recipe = document.createElement('a');
                recipe.text = recipes[j].name;
                recipe.setAttribute('href', recipes[j].url);
                recipe.setAttribute('style', 'margin: 20px 20px 0 0');
                recipe.setAttribute('target', '_blank')
                para.appendChild(recipe);

                if (recipes[j].hasOwnProperty('aggregateRating')) {
                    var span = document.createElement('span');
                    span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                    para.appendChild(span);
                }

                div.appendChild(para);
            }
        }


        // Display links for the first 10 shopping offers.
        // TODO: Add 'more' link in case the user wants to see all of them.
        function addShopping(div, offers) {
            var length = (offers.length > 10) ? 10 : offers.length;

            for (var j = 0; j < length; j++) {
                var para = document.createElement('p');

                var offer = document.createElement('a');
                offer.text = offers[j].name;
                offer.setAttribute('href', offers[j].url);
                offer.setAttribute('style', 'margin: 20px 20px 0 0');
                offer.setAttribute('target', '_blank')
                para.appendChild(offer);

                var span = document.createElement('span');
                span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
                para.appendChild(span);

                div.appendChild(para);
            }
        }


        // Display the first 10 related products. Display a clickable image of the 
        // product that takes the user to Bing.com search results for the product.
        // If there are any offers associated with the product, provide links to the offers.
        // TODO: Add 'more' link in case the user wants to see all of them.
        function addProducts(div, products) {
            var length = (products.length > 10) ? 10 : products.length;

            for (var j = 0; j < length; j++) {
                var childDiv = document.createElement('div');
                childDiv.setAttribute('class', 'stackLink');
                div.appendChild(childDiv);

                var img = document.createElement('img');
                img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
                img.setAttribute('title', products[j].name);
                img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
                img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
                img.addEventListener('click', function(e) {
                    var url = e.target.getAttribute('data-webSearchUrl');
                    window.open(url, 'foo');
                })
                childDiv.appendChild(img);

                if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                    if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                        var offers = products[j].insightsMetadata.aggregateOffer.offers;

                        // Show all the offers. Not all markets provide links to offers.
                        for (var i = 0; i < offers.length; i++) {  
                            var para = document.createElement('p');

                            var offer = document.createElement('a');
                            offer.text = offers[i].name;
                            offer.setAttribute('href', offers[i].url);
                            offer.setAttribute('style', 'margin: 20px 20px 0 0');
                            offer.setAttribute('target', '_blank')
                            para.appendChild(offer);

                            var span = document.createElement('span');
                            span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                            para.appendChild(span);

                            childDiv.appendChild(para);
                        }
                    }
                    else {  // Otherwise, just show the lowest price that Bing found.
                        var offer = products[j].insightsMetadata.aggregateOffer;

                        var para = document.createElement('p');
                        para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                        childDiv.appendChild(para);
                    }
                }
            }
        }


        // If Bing recognized any text in the image, display the text.
        function addTextResult(div, action) {
            var text = document.createElement('p');
            text.textContent = action.displayName;
            div.appendChild(text);
        }


        // If the image is of a person, the tag might include an entity
        // action type. Display a link that takes the user to Bing.com
        // where they can get details about the entity.
        function addEntity(div, action) {
            var entity = document.createElement('a');
            entity.text = action.displayName;
            entity.setAttribute('href', action.webSearchUrl);
            entity.setAttribute('style', 'margin: 20px 20px 0 0');
            entity.setAttribute('target', '_blank');
            div.appendChild(entity);
        }


        // Adds a clickable image to the div that takes the user to
        // Bing.com search results.
        function addImageWithWebSearchUrl(div, image, action) {
            var img = document.createElement('img');
            img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', action.webSearchUrl);
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            div.appendChild(img);
        }

```



## <a name="adding-styles-to-make-the-page-display-correctly"></a>Добавление стилей для правильного отображения страницы

Добавьте следующий раздел \<style\> в тег \<head\>.

```html
        <style>
            
            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```



## <a name="complete-code"></a>Полный код

Ниже приведен полный пример HTML и JavaScript.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Bing Visual Search Demo</title>
        
        <style>
            
            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>

        <script>

            // Handles the user's file selection. The file input button specifies this handler.
            function handleFileSelect(selector) {
                var files = document.getElementById(selector).files; // FileList object

                // The list will contain only one file.
                for (var i = 0, f; f = files[i]; i++) {
        
                    // Only process image files.
                    if (!f.type.match('image.*')) {
                        alert("Selected file must be an image file.");
                        document.getElementById("uploadImage").value = null;
                        continue;
                    }
        
                    // Image must be <= 1 MB and should be about 1500px.
                    if (f.size > 1000000) {
                        alert("Image must be less than 1 MB.");
                        document.getElementById("uploadImage").value = null;
                        continue;
                    }


                    var reader = new FileReader();
        
                    // Capture the file information.
                    reader.onload = (function(theFile) {
                        return function(e) {
                            var fileOutput = document.getElementById('thumbnail');

                            if (fileOutput.childElementCount > 0) {
                                fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                            }

                            // Render thumbnail.
                            var span = document.createElement('span');
                            span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                            '" title="', escape(theFile.name), '"/>'].join('');
                            fileOutput.insertBefore(span, null);
                        };
                    })(f);
        
                    // Read in the image file as a data URL.
                    reader.readAsDataURL(f);
                }
            }


            // Contains the toggle state of divs.
            var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


            // Toggles between showing and hiding the specified div.
            function expandCollapse(divToToggle) {
                var div = document.getElementById(divToToggle);

                if (divToggleMap[divToToggle] == 1) {   // if div is expanded
                    div.style.display = "none";
                    divToggleMap[divToToggle] = 0;
                }
                else {                                  // if div is collapsed
                    div.style.display = "inline-block";
                    divToggleMap[divToToggle] = 1;
                }
            }


            // Called when the user clicks the Query insights button.
            function handleQuery() {
                var subscriptionKey = document.getElementById('key').value;

                // Make sure user provided a subscription key and image.
                // For this demo, the user provides the key but typically you'd 
                // get it from secured storage.
                if (subscriptionKey.length !== 32) {
                    alert("Subscription key length is not valid. Enter a valid key.");
                    document.getElementById('key').focus();
                    return;
                }

                var imagePath = document.getElementById('uploadImage').value;

                if (imagePath.length === 0)
                {
                    alert("Please select an image to upload.");
                    document.getElementById('uploadImage').focus();
                    return;
                }

                var responseDiv = document.getElementById('responseSection');

                // Clear out the response from the last query.
                while (responseDiv.childElementCount > 0) {
                    responseDiv.removeChild(responseDiv.lastChild);
                }

                // Send the request to Bing to get insights about the image.
                var f = document.getElementById('uploadImage').files[0];
                sendRequest(f, subscriptionKey);
            }


            // Format the request and send it.
            function sendRequest(file, key) {
                var market = document.getElementById('mkt').value;
                var safeSearch = document.getElementById('safesearch').value;
                var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

                var form = new FormData();
                form.append("image", file);

                var request = new XMLHttpRequest();

                request.open("POST", baseUri);
                request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
                request.addEventListener('load', handleResponse);
                request.send(form);
            }


            // Handles the response from Bing. Parses the response and 
            // the tag divs.
            function handleResponse() {
                if(this.status !== 200){
                    alert("Error calling Bing Visual Search. See console log for details.");
                    console.log(this.responseText);
                    return;
                }

                var tags = parseResponse(JSON.parse(this.responseText));
                var h4 = document.createElement('h4');
                h4.textContent = 'Bing internet search results';
                document.getElementById('responseSection').appendChild(h4);
                buildTagSections(tags);

                document.body.style.cursor = 'default'; // reset the wait curor set by query insights button
            }


            // Parses the json response by tags.
            function parseResponse(json) {
                var dict = {};

                for (var i =0; i < json.tags.length; i++) {
                    var tag = json.tags[i];
                
                    if (tag.displayName === '') {
                        dict['Default'] = JSON.stringify(tag);
                    }
                    else {
                        dict[tag.displayName] = JSON.stringify(tag);
                    }
                }

                return(dict);
            }


            // Builds divs fro each tag in the response.
            function buildTagSections(tags) {
                for (var tag in tags) {
                    if (tags.hasOwnProperty(tag)) {
                        var tagSection = buildDiv(tags, tag);
                        document.getElementById('responseSection').appendChild(tagSection);
                    }
                }  
            }


            // Builds the div for the specified tag. The div is shown as a
            // link that when clicked, expands or collapses. The divs are 
            // initially collapsed.
            function buildDiv(tags, tag) {
                var tagSection = document.createElement('div');
                tagSection.setAttribute('class', 'subSection');

                var link = document.createElement('a');
                link.setAttribute('href', '#');
                link.setAttribute('style', 'float: left;')
                link.text = tag;
                tagSection.appendChild(link);

                var contentDiv = document.createElement('div');
                contentDiv.setAttribute('id', tag);
                contentDiv.setAttribute('style', 'clear: left;')
                contentDiv.setAttribute('class', 'section');
                tagSection.appendChild(contentDiv);

                link.setAttribute('onclick', `expandCollapse("${tag}")`);
                divToggleMap[tag] = 0;  // 1 = show, 0 = hide

                addDivContent(contentDiv, tag, tags[tag]);

                return tagSection;
            }


            // Adds the tag's action types to the div.
            function addDivContent(div, tag, json) {

                // Adds the first 100 characters of the json that contains
                // the tag's data. The user can click the text to show the
                // full json. They can click it again to collapse the json.
                var para = document.createElement('p');
                para.textContent = String(json).substr(0, 100) + '...';
                para.setAttribute('title', 'click to expand');
                para.setAttribute('style', 'cursor: pointer;')
                para.setAttribute('data-json', json);
                para.addEventListener('click', function(e) {
                    var json = e.target.getAttribute('data-json');

                    if (e.target.textContent.length <= 103) {  // 100 + '...'
                        e.target.textContent = json;
                        para.setAttribute('title', 'click to collapse');
                    }
                    else {
                        para.textContent = String(json).substr(0, 100) + '...';
                        para.setAttribute('title', 'click to expand');
                    }
                });
                div.appendChild(para); 

                var parsedJson = JSON.parse(json);

                // Loop through all the actions in the tag and display them.
                for (var j = 0; j < parsedJson.actions.length; j++) {
                    var action = parsedJson.actions[j];

                    var subSectionDiv = document.createElement('div');
                    subSectionDiv.setAttribute('class', 'subSection');
                    div.appendChild(subSectionDiv);

                    var h4 = document.createElement('h4');
                    h4.innerHTML = action.actionType;
                    subSectionDiv.appendChild(h4);

                    if (action.actionType === 'ImageResults') {
                        addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
                    }
                    else if (action.actionType === 'DocumentLevelSuggestions') {
                        addRelatedSearches(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'RelatedSearches') {
                        addRelatedSearches(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'PagesIncluding') {
                        addPagesIncluding(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'VisualSearch') {
                        addRelatedImages(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'Recipes') {
                        addRecipes(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'ShoppingSources') {
                        addShopping(subSectionDiv, action.data.offers);
                    }
                    else if (action.actionType === 'ProductVisualSearch') {
                        addProducts(subSectionDiv, action.data.value);
                    }
                    else if (action.actionType === 'TextResults') {
                        addTextResult(subSectionDiv, action);
                    }
                    else if (action.actionType === 'Entity') {
                        addEntity(subSectionDiv, action);
                    }
                }
            }


            // Display the first 10 related images.
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addRelatedImages(div, images) {
                var length = (images.length > 10) ? 10 : images.length;

                // Set the title to the website that hosts the image. The title displays 
                // when the user hovers over the image. 

                // Make the image clickable. If the user clicks the image, they're taken
                // to the image in Bing.com.

                for (var j = 0; j < length; j++) {
                    var img = document.createElement('img');
                    img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
                    img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
                    img.setAttribute('title', images[j].hostPageDisplayUrl);
                    img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

                    img.addEventListener('click', function(e) {
                        var url = e.target.getAttribute('data-webSearchUrl');
                        window.open(url, 'foo');
                    })

                    div.appendChild(img);
                }
            }


            // Display links to the first 5 webpages that include the image.
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addPagesIncluding(div, pages) {
                var length = (pages.length > 5) ? 5 : pages.length;

                for (var j = 0; j < length; j++) {
                    var page = document.createElement('a');
                    page.text = pages[j].name;
                    page.setAttribute('href', pages[j].hostPageUrl);
                    page.setAttribute('style', 'margin: 20px 20px 0 0');
                    page.setAttribute('target', '_blank')
                    div.appendChild(page);

                    div.appendChild(document.createElement('br'));
                }
            }


            // Display the first 10 related searches. Include a link with the image
            // that when clicked, takes the user to Bing.com and displays the 
            // related search results.
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addRelatedSearches(div, relatedSearches) {
                var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

                for (var j = 0; j < length; j++) {
                    var childDiv = document.createElement('div');
                    childDiv.setAttribute('class', 'stackLink');
                    div.appendChild(childDiv);

                    var img = document.createElement('img');
                    img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
                    img.setAttribute('style', 'margin: 20px 20px 0 0;');
                    childDiv.appendChild(img);

                    var relatedSearch = document.createElement('a');
                    relatedSearch.text = relatedSearches[j].displayText;
                    relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
                    relatedSearch.setAttribute('target', '_blank');
                    childDiv.appendChild(relatedSearch);

                }
            }


            // Display links to the first 10 recipes. Include the recipe's rating,
            // if available. 
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addRecipes(div, recipes) {
                var length = (recipes.length > 10) ? 10 : recipes.length;

                for (var j = 0; j < length; j++) {
                    var para = document.createElement('p');

                    var recipe = document.createElement('a');
                    recipe.text = recipes[j].name;
                    recipe.setAttribute('href', recipes[j].url);
                    recipe.setAttribute('style', 'margin: 20px 20px 0 0');
                    recipe.setAttribute('target', '_blank')
                    para.appendChild(recipe);

                    if (recipes[j].hasOwnProperty('aggregateRating')) {
                        var span = document.createElement('span');
                        span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                        para.appendChild(span);
                    }

                    div.appendChild(para);
                }
            }


            // Display links for the first 10 shopping offers.
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addShopping(div, offers) {
                var length = (offers.length > 10) ? 10 : offers.length;

                for (var j = 0; j < length; j++) {
                    var para = document.createElement('p');

                    var offer = document.createElement('a');
                    offer.text = offers[j].name;
                    offer.setAttribute('href', offers[j].url);
                    offer.setAttribute('style', 'margin: 20px 20px 0 0');
                    offer.setAttribute('target', '_blank')
                    para.appendChild(offer);

                    var span = document.createElement('span');
                    span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
                    para.appendChild(span);

                    div.appendChild(para);
                }
            }


            // Display the first 10 related products. Display a clickable image of the 
            // product that takes the user to Bing.com search results for the product.
            // If there are any offers associated with the product, provide links to the offers.
            // TODO: Add 'more' link in case the user wants to see all of them.
            function addProducts(div, products) {
                var length = (products.length > 10) ? 10 : products.length;

                for (var j = 0; j < length; j++) {
                    var childDiv = document.createElement('div');
                    childDiv.setAttribute('class', 'stackLink');
                    div.appendChild(childDiv);

                    var img = document.createElement('img');
                    img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
                    img.setAttribute('title', products[j].name);
                    img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
                    img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
                    img.addEventListener('click', function(e) {
                        var url = e.target.getAttribute('data-webSearchUrl');
                        window.open(url, 'foo');
                    })
                    childDiv.appendChild(img);

                    if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                        if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                            var offers = products[j].insightsMetadata.aggregateOffer.offers;

                            // Show all the offers. Not all markets provide links to offers.
                            for (var i = 0; i < offers.length; i++) {  
                                var para = document.createElement('p');

                                var offer = document.createElement('a');
                                offer.text = offers[i].name;
                                offer.setAttribute('href', offers[i].url);
                                offer.setAttribute('style', 'margin: 20px 20px 0 0');
                                offer.setAttribute('target', '_blank')
                                para.appendChild(offer);

                                var span = document.createElement('span');
                                span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                                para.appendChild(span);

                                childDiv.appendChild(para);
                            }
                        }
                        else {  // Otherwise, just show the lowest price that Bing found.
                            var offer = products[j].insightsMetadata.aggregateOffer;

                            var para = document.createElement('p');
                            para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                            childDiv.appendChild(para);
                        }
                    }
                }
            }


            // If Bing recognized any text in the image, display the text.
            function addTextResult(div, action) {
                var text = document.createElement('p');
                text.textContent = action.displayName;
                div.appendChild(text);
            }


            // If the image is of a person, the tag might include an entity
            // action type. Display a link that takes the user to Bing.com
            // where they can get details about the entity.
            function addEntity(div, action) {
                var entity = document.createElement('a');
                entity.text = action.displayName;
                entity.setAttribute('href', action.webSearchUrl);
                entity.setAttribute('style', 'margin: 20px 20px 0 0');
                entity.setAttribute('target', '_blank');
                div.appendChild(entity);
            }


            // Adds a clickable image to the div that takes the user to
            // Bing.com search results.
            function addImageWithWebSearchUrl(div, image, action) {
                var img = document.createElement('img');
                img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
                img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
                img.setAttribute('data-webSearchUrl', action.webSearchUrl);
                img.addEventListener('click', function(e) {
                    var url = e.target.getAttribute('data-webSearchUrl');
                    window.open(url, 'foo');
                })
                div.appendChild(img);
            }

        </script>

    </head>

    <body>

        <div id="requestSection">
            <div>
                <p>Select image to get insights from Bing:
                    <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
                </p>

                <output id="thumbnail"></output>
            </div>

            <div>
                <p>Subscription key: 
                    <input type="text" id="key" name="subscription" size=40 maxlength="32" />
                </p>
            </div>

            <div>
                <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

                <div id="options" style="display:none">
                    <p style="margin-left: 20px">Market: 
                        <select id="mkt">
                            <option value="es-AR">Argentina (Spanish)</option>
                            <option value="en-AU">Australia (English)</option>
                            <option value="de-AT">Austria (German)</option>
                            <option value="nl-BE">Belgium (Dutch)</option>
                            <option value="fr-BE">Belgium (French)</option>
                            <option value="pt-BR">Brazil (Portuguese)</option>
                            <option value="en-CA">Canada (English)</option>
                            <option value="fr-CA">Canada (French)</option>
                            <option value="es-CL">Chile (Spanish)</option>
                            <option value="da-DK">Denmark (Danish)</option>
                            <option value="fi-FI">Finland (Finnish)</option>
                            <option value="fr-FR">France (French)</option>
                            <option value="de-DE">Germany (German)</option>
                            <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
                            <option value="en-IN">India (English)</option>
                            <option value="en-ID">Indonesia (English)</option>
                            <option value="it-IT">Italy (Italian)</option>
                            <option value="ja-JP">Japan (Japanese)</option>
                            <option value="ko-KR">Korea (Korean)</option>
                            <option value="en-MY">Malaysia (English)</option>
                            <option value="es-MX">Mexico (Spanish)</option>
                            <option value="nl-NL">Netherlands (Dutch)</option>
                            <option value="en-NZ">New Zealand (English)</option>
                            <option value="no-NO">Norway (Norwegian)</option>
                            <option value="zh-CN">People's Republic of China (Chinese)</option>
                            <option value="pl-PL">Poland (Polish)</option>
                            <option value="pt-PT">Portugal (Portuguese)</option>
                            <option value="en-PH">Philippines (English)</option>
                            <option value="ru-RU">Russia (Russian)</option>
                            <option value="ar-SA">Saudi Arabia (Arabic)</option>
                            <option value="en-ZA">South Africa (English)</option>
                            <option value="es-ES">Spain (Spanish)</option>
                            <option value="sv-SE">Sweden (Swedish)</option>
                            <option value="fr-CH">Switzerland (French)</option>
                            <option value="de-CH">Switzerland (German)</option>
                            <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                            <option value="tr-TR">Turkey (Turkish)</option>
                            <option value="en-GB">United Kingdom (English)</option>
                            <option value="en-US" selected>United States (English)</option>
                            <option value="es-US">United States (Spanish)</option>
                        </select>
                    </p>
                    <p style="margin-left: 20px">Safe search: 
                        <select id="safesearch">
                            <option value="moderate" selected>Moderate</option>
                            <option value="strict">Strict</option>
                            <option value="off">off</option>
                        </select>
                    </p>
                </div>
            </div>
        </div>
        
        <p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>

        <hr />
        
        <div id="responseSection"></div>
    </body>
</html>      
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как получать полезные сведения с помощью и маркера аналитических сведений, ознакомьтесь с [руководством по использованию ImageInsightsToken в пакете SDK для Bing Visual Search](.\tutorial-visual-search-insights-token.md).