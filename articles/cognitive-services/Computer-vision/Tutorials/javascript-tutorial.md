---
title: Руководство по использованию API компьютерного зрения для JavaScript | Документация Майкрософт
description: Изучите базовое приложение JavaScript, в котором используется API компьютерного зрения в Microsoft Cognitive Services. Узнайте, как выполнять распознавание текста, создавать эскизы и работать с визуальными компонентами в изображении.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380365"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Руководство по использованию API компьютерного зрения для JavaScript

В этом руководстве демонстрируются функции REST API компьютерного зрения в Microsoft Cognitive Services.

Исследуйте приложение JavaScript, которое использует REST API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных компонентов изображения, включая лица. В этом примере можно отправить URL-адрес изображения для анализа или обработки. Вы можете использовать этот пример с открытым кодом как шаблон для создания приложения на языке JavaScript, которое использует REST API компьютерного зрения.

Приложение форм JavaScript уже написано, но в нем нет функциональных возможностей API компьютерного зрения. В этом руководстве вы добавите код для API компьютерного зрения, чтобы дополнить функциональные возможности приложения.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="platform-requirements"></a>Требования платформы

Это руководство было разработано с использованием простого текстового редактора.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Получение подписки API компьютерного зрения и ключа подписки 

Перед созданием примера необходимо получить подписку API компьютерного зрения, который является частью Microsoft Cognitive Services. Сведения о подписках и управлении ключами см. в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). В этом руководстве можно использовать как первичный, так и вторичный ключ. 

## <a name="download-the-tutorial-project"></a>Скачивание учебного проекта

Клонируйте репозиторий [Cognitive Services JavaScript Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) или скачайте ZIP-файл и извлеките его содержимое в пустой каталог.

Если вы предпочитаете использовать завершенное руководство с полным учебным кодом, можно использовать файлы в папке **Completed**.

## <a name="add-the-tutorial-code"></a>Добавление учебного кода

В приложении JavaScript заданы шесть HTML-файлов, по одному для каждого компонента. Каждый файл демонстрирует отдельную функцию API компьютерного зрения (анализ, распознавание текста и т. д). Шесть разделов руководства не имеют взаимозависимостей, поэтому учебный код можно добавить в один файл, во все шесть файлов или только в некоторые из них. Коме того, учебный код можно добавлять в файлы в любом порядке.

Давайте приступим.

## <a name="analyze-an-image"></a>Анализ изображения

Функция анализа API компьютерного зрения позволяет анализировать изображения для обнаружения более 2000 распознаваемых объектов, живых существ, пейзажей и действий. После завершения анализа эта функция возвращает объект JSON, описывающий изображение с помощью описательных тегов, анализа цвета, подписей и многого другого.

Чтобы завершить функцию анализа учебного приложения, выполните следующие действия.

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Анализ, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **analyze.html** в текстовом редакторе и найдите функцию **analyzeButtonClick** в нижней части этого файла.

Функция обработчика событий **AnalyzeButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает функцию **AnalyzeImage** для анализа этого изображения.

Скопируйте приведенный ниже код и вставьте его в функцию **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Анализ, шаг 2. Добавление программы-оболочки для вызова REST API

Функция **AnalyzeImage** создает программу-оболочку для вызова REST API для анализа изображения. После успешного возвращения анализ в формате JSON будет отображен в указанном элементе textarea, а подпись будет отображена в указанном элементе span.

Скопируйте код функции **AnalyzeImage** и вставьте его сразу же под функцией **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="analyze-step-3-run-the-application"></a>Анализ, шаг 3. Запуск приложения

Сохраните файл **analyze.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="recognize-a-landmark"></a>Распознавание ориентира

Функция распознавания ориентиров в API компьютерного зрения позволяет анализировать изображения для поиска природных и искусственных ориентиров, например гор или известных зданий. После завершения анализа функция распознавания ориентиров возвращает объект JSON, в котором определены ориентиры, найденные в изображении.

Чтобы завершить функцию распознавания ориентиров учебного приложения, выполните следующие действия.

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Распознавание ориентиров, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **landmark.html** в текстовом редакторе и найдите функцию **landmarkButtonClick** в нижней части этого файла.

Функция обработчика событий **landmarkButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает функцию **IdentifyLandmarks** для анализа этого изображения.

Скопируйте приведенный ниже код и вставьте его в функцию **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Распознавание ориентиров, шаг 2. Добавление программы-оболочки для вызова REST API

Функция **IdentifyLandmarks** создает программу-оболочку для вызова REST API для анализа изображения. После успешного возвращения анализ в формате JSON будет отображен в указанном элементе textarea, а подпись будет отображена в указанном элементе span.

Скопируйте код функции **IdentifyLandmarks** и вставьте его сразу же под функцией **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="landmark-step-3-run-the-application"></a>Распознавание ориентиров, шаг 3. Запуск приложения

Сохраните файл **landmark.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="recognize-celebrities"></a>Распознавание знаменитостей

Функция распознавания знаменитостей API компьютерного зрения анализирует изображения для выявления знаменитых людей. После завершения анализа функция распознавания знаменитостей возвращает объект JSON, в котором определены знаменитости, найденные в изображении.

Чтобы завершить функцию распознавания знаменитостей учебного приложения, выполните следующие действия.

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Распознавание знаменитостей, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **celebrities.html** в текстовом редакторе и найдите функцию **celebritiesButtonClick** в нижней части этого файла.

Функция обработчика событий **celebritiesButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает функцию **IdentifyCelebrities** для анализа этого изображения.

Скопируйте приведенный ниже код и вставьте его в функцию **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Распознавание знаменитостей, шаг 2. Добавление программы-оболочки для вызова REST API

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="celebrities-step-3-run-the-application"></a>Распознавание знаменитостей, шаг 3. Запуск приложения

Сохраните файл **celebrities.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="intelligently-generate-a-thumbnail"></a>Интеллектуальное создание эскиза

Функция создания эскизов API компьютерного зрения создает эскиз изображения. С помощью функции **интеллектуальной обрезки** компонент создания эскизов определит интересующую область в изображении и отцентрирует эскиз по данной области, чтобы сделать его более эстетичным.

Чтобы завершить функцию создания эскизов учебного приложения, выполните следующие действия.

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Создание эскизов, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **thumbnail.html** в текстовом редакторе и найдите функцию **thumbnailButtonClick** в нижней части этого файла.

Функция обработчика событий **thumbnailButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем дважды вызывает функцию **getThumbnail** для создания двух эскизов, одного с использованием интеллектуальной обрезки и одного без ее использования.

Скопируйте приведенный ниже код и вставьте его в функцию **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Создание эскизов, шаг 2. Добавление программы-оболочки для вызова REST API

Функция **getThumbnail** создает программу-оболочку для вызова REST API для анализа изображения. После успешного возвращения в указанном элементе img будет отображен эскиз.

Скопируйте приведенную ниже функцию **getThumbnail** и вставьте ее сразу после функции **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

### <a name="thumbnail-step-3-run-the-application"></a>Создание эскизов, шаг 3. Запуск приложения

Сохраните файл **thumbnail.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Generate Thumbnails** (Создать эскизы), чтобы проанализировать изображение и просмотреть результат.

## <a name="read-printed-text-ocr"></a>Распознавание печатного текста (OCR)

Функция оптического распознавания символов (OCR) API компьютерного зрения анализирует изображение печатного текста. После завершения анализа функция OCR возвращает объект JSON, содержащий текст и расположение текста в изображении.

Чтобы завершить функцию OCR учебного приложения, выполните следующие действия.

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>Распознавание текста, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **ocr.html** в текстовом редакторе и найдите функцию **ocrButtonClick** в нижней части этого файла.

Функция обработчика событий **ocrButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает функцию **ReadOcrImage** для анализа этого изображения.

Скопируйте приведенный ниже код и вставьте его в функцию **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>Распознавание текста, шаг 2. Добавление программы-оболочки для вызова REST API

Функция **ReadOcrImage** создает программу-оболочку для вызова REST API для анализа изображения. После успешного возвращения описание текста и его расположения в формате JSON будет отображено в указанном элементе textarea.

Скопируйте приведенную ниже функцию **ReadOcrImage** и вставьте ее сразу после функции **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="ocr-step-3-run-the-application"></a>Распознавание текста, шаг 3. Запуск приложения

Сохраните файл **ocr.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Read Image** (Распознать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="read-handwritten-text-handwriting-recognition"></a>Распознавание рукописного текста

Функция распознавания рукописного текста API компьютерного зрения анализирует изображение рукописного текста. После завершения анализа функция распознавания рукописного текста возвращает объект JSON, содержащий текст и расположение текста в изображении.

Чтобы завершить функцию распознавания рукописного текста учебного приложения, выполните следующие действия.

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Распознавание рукописного текста, шаг 1. Добавление кода обработчика событий для кнопки формы

Откройте файл **handwriting.html** в текстовом редакторе и найдите функцию **handwritingButtonClick** в нижней части этого файла.

Функция обработчика событий **handwritingButtonClick** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает функцию **HandwritingImage** для анализа этого изображения.

Скопируйте приведенный ниже код и вставьте его в функцию **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Распознавание рукописного текста, шаг 2. Добавление программы-оболочки для вызова REST API

Функция **ReadHandwrittenImage** создает программу-оболочку для двух вызовов REST API, необходимых для анализа изображения. Так как распознавание рукописного текста требует много времени, используется обработка в два этапа. Первый вызов отправляет изображение для обработки. Второй вызов извлекает обнаруженный текст после завершения обработки.

После извлечения текста описание этого текста и его расположения в формате JSON будет отображено в указанном элементе textarea.

Скопируйте приведенную ниже функцию **ReadHandwrittenImage** и вставьте ее сразу после функции **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Распознавание рукописного текста, шаг 3. Запуск приложения

Сохраните файл **handwriting.html** и откройте его в веб-браузере. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Read Image** (Распознать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="next-steps"></a>Дополнительная информация

- [Руководство по API компьютерного зрения для C&#35;](CSharpTutorial.md)
- [Руководство по API компьютерного зрения для Python](PythonTutorial.md)
