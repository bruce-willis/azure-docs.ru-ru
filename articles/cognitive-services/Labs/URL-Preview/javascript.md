---
title: Краткое руководство по JavaScript. Использование службы предварительного просмотра URL-адресов в проектах в Microsoft Cognitive Services | Документация Майкрософт
description: Пример скрипта для быстрого начала работы с API предварительного просмотра URL-адресов Bing в Microsoft Cognitive Services в Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381773"
---
# <a name="url-preview-in-javascript"></a>Использование службы предварительного просмотра URL-адресов с JavaScript 

В следующем одностраничном приложении с помощью JavaScript создается представление для предварительного просмотра URL-адреса сайта SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>предварительным требованиям

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="code-scenario"></a>Сценарий кода
В следующем примере JavaScript содержится объект для ввода данных в текстовое поле, куда пользователь вводит URL-адрес для предварительного просмотра.  При нажатии пользователем кнопки **Preview** (Просмотр) метод onclick перенаправляется в `getPreview`, где код формирует веб-запрос к конечной точке **UrlPreview**.

Код создает *XMLHttpRequest*, добавляет заголовок *Ocp-Apim-Subscription-Key* и ключ, а затем отправляет запрос.  При этом для обработки ответа добавляется обработчик асинхронных событий.

Если ответ успешно возвращается, обработчик назначает текст JSON ответа абзацу `demo` на странице. Другие элементы ответа обрабатываются в следующих абзацах для отображения.

**Необработанный ответ JSON**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**Запущенная демонстрация**

![Пример предварительного просмотра URL-адреса на JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Запуск приложения

Чтобы запустить приложение, сделайте следующее:

1. Замените значение `YOUR-SUBSCRIPTION-KEY` действующим ключом доступа для своей подписки.
2. Сохраните HTML и скрипт в файле с расширением .html.
3. Запустите веб-страницу в браузере.
4. Используйте существующий URL-адрес или введите другой в текстовое поле.
5. Нажмите кнопку **Preview** (Просмотр).

**Исходный код:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](csharp.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство для Node](node-quickstart.md)
- [Краткое руководство для Python](python-quickstart.md)