---
title: Отправка изображений Bing для аналитики | Документация Майкрософт
description: Консольное приложение, которое использует интерфейс "API Bing для поиска изображений" для отправки изображений и получения сведений о них.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380064"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Руководство по отправке изображений Bing для аналитики

API Bing для поиска изображений предоставляет параметр `POST`, который можно использовать для отправки изображения и поиска сведений, связанных с этим изображением. Это консольное приложение C# отправляет изображение с помощью конечной точки для поиска изображений, чтобы получить сведения об этом изображении.
Результаты, если коротко, являются объектами JSON и представлены в таком виде:

![[Результаты JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

В этом учебнике объясняется, как выполнить такие задачи:

> [!div class="checklist"]
> * Использование конечной точки для поиска изображений `/details` в запросе `POST`.
> * Указание заголовков для запроса.
> * Использование параметров URL-адреса для указания результатов.
> * Отправка данных изображений и отправка запроса `POST`.
> * Печать результатов JSON в консоль.

## <a name="app-components"></a>Добавление компонентов

Это учебное приложение состоит из трех частей:

> [!div class="checklist"]
> * Настройка конечной точки для указания конечной точки API Bing для поиска изображений и обязательных заголовков.
> * Отправка файла изображения для запроса `POST` к конечной точке.
> * Анализ результатов JSON, которые являются сведениями, возвращаемыми запросом `POST`.

## <a name="scenario-overview"></a>Обзор сценария
Существует [три конечные точки для поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). Конечная точка `/details` может использовать запрос `POST` с данными изображения в теле запроса.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Параметр `modules` URL-адреса, следующий за параметром `/details?`, указывает, какой тип сведений содержится в результатах:
* `modules=All`
* `modules=RecognizedEntities` (люди или места, видимые в изображении).

Если в запросе `POST` указать `modules=All`, то полученный текст JSON будет содержать такой список:
* `bestRepresentativeQuery` — запрос Bing, который возвращает изображения, похожие на отправленное изображение.
* `detectedObjects` — обнаруженные в изображении объекты, например ограничивающий прямоугольник или гиперобъекты.
* `image` — метаданные изображения.
* `imageInsightsToken` — токен для последующего запроса `GET`, который возвращает результат `RecognizedEntities` (люди или места, видимые в изображении). 
* `imageTags`
* `pagesIncluding` — веб-страницы, содержащие данное изображение.
* `relatedSearches`
* `visuallySimilarImages`

Если в запросе `POST` указать `modules=RecognizedEntities`, то вы получите только токен `imageInsightsToken`, который используется в последующем запросе `GET`. Он идентифицирует людей или места, которые можно увидеть в изображении.

## <a name="webclient-and-headers-for-the-post-request"></a>WebClient и заголовки для запроса POST
Создайте объект `WebClient` и задайте заголовки. Во всех запросах к API-интерфейсам поиска Bing необходимо указывать параметр `Ocp-Apim-Subscription-Key`. В запросе `POST` на отправку изображения необходимо также указать `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Отправка изображения и получение результатов

Класс `WebClient` включает в себя метод `UpLoadFile`, который форматирует данные для запроса `POST`. Он форматирует `RequestStream` и вызывает `HttpWebRequest`, что позволяет избежать более сложных действий.
Вызовите `WebClient.UpLoadFile`, указав конечную точку `/details` и файл изображения для отправки. Ответом являются двоичные данные, которые легко преобразуются в формат JSON. 

Используйте текст JSON для инициализации экземпляра структуры `SearchResult` (контекст можно найти в [исходном коде приложения](tutorial-image-post-source.md)).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Отображение результатов
Остальная часть кода анализирует результат JSON и выводит его на консоль.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>Запрос GET с применением токена ImageInsightsToken
Чтобы использовать токен `ImageInsightsToken`, возвращаемый с результатами запроса `POST`, создайте запрос `GET`, аналогичный приведенному ниже:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Если в изображении удается идентифицировать людей или места, то этот запрос возвращает сведения о них.
[Краткие руководства](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) содержат многочисленные примеры кода.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

