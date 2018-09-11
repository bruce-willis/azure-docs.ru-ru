---
title: Краткое руководство по Azure Cognitive Services и API анализа текста для Go | Документация Майкрософт
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API анализа текста в Cognitive Services в Azure.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: nolachar
ms.openlocfilehash: 0969700434e3f848aebfa833f0816c6f9f019560
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "43771434"
---
# <a name="quickstart-for-text-analytics-api-with-go"></a>Краткое руководство по работе с API анализа текста для Go 
<a name="HOLTop"></a>

В этой статье содержатся сведения о том, как [определять язык](#Detect), [анализировать тональность](#SentimentAnalysis), [извлекать ключевые фразы](#KeyPhraseExtraction) и [идентифицировать связанные сущности](#Entities), используя [API анализа текста](//go.microsoft.com/fwlink/?LinkID=759711) и язык Go.

Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).

## <a name="prerequisites"></a>Предварительные требования

Необходимо иметь [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API анализа текста**. Для выполнения действий, указанных в этом кратком руководстве, можно использовать подписку **уровня "Бесплатный" на 5000 транзакций в месяц**.

Также требуются [конечная точка и ключ доступа](../How-tos/text-analytics-how-to-access-key.md), созданный автоматически во время регистрации.

<a name="Detect"></a>

## <a name="detect-language-request"></a>Запрос на определение языка

API распознавания языка определяет язык текстового документа, используя [метод определения языка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
1. Замените расположение в `uriBase` (в настоящее время `westcentralus`) на свой регион регистрации.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например "go build quickstart.go".
1. Запустите файл, например "quickstart".

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/languages"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-language-response"></a>Результат определения языка

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment-request"></a>Запрос на анализ тональности

API анализа тональности определяет тональность набора текстовых записей с помощью [метода определения тональности](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). В следующем примере выполняется оценка двух документов — на английском и на испанском языках.

1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
1. Замените расположение в `uriBase` (в настоящее время `westcentralus`) на свой регион регистрации.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например "go build quickstart.go".
1. Запустите файл, например "quickstart".

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/sentiment"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-sentiment-response"></a>Результат анализа тональности

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases-request"></a>Запрос на извлечение ключевых фраз

API извлечения ключевых фраз извлекает ключевые фразы из текстового документа с помощью [метода ключевых фраз](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Следующий пример извлекает ключевые фразы в документах на английском и испанском языках.

1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
1. Замените расположение в `uriBase` (в настоящее время `westcentralus`) на свой регион регистрации.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например "go build quickstart.go".
1. Запустите файл, например "quickstart".

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/keyPhrases"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="extract-key-phrases-response"></a>Результат извлечения ключевых фраз

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities-request"></a>Запрос на идентификацию связанных сущностей

API связывания сущностей определяет распространенные сущности в текстовом документе, используя [метод связывания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Следующий пример определяет сущности в документах на английском языке.

1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
1. Замените расположение в `uriBase` (в настоящее время `westcentralus`) на свой регион регистрации.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например "go build quickstart.go".
1. Запустите файл, например "quickstart".

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/entities"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "en", "text": "The Seattle Seahawks won the Super Bowl in 2014."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="identify-linked-entities-response"></a>Результат идентификации связанных сущностей

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "4K resolution",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "4K resolution",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/4K_resolution",
                    "bingId": "1d4d689e-9cbf-b9eb-6ecf-f3296aaa96d8"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "Seattle Seahawks",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Seattle Seahawks",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle_Seahawks",
                    "bingId": "1bf844db-8225-f90c-a78e-1787fb8af0ce"
                },
                {
                    "name": "Super Bowl",
                    "matches": [
                        {
                            "text": "Super Bowl",
                            "offset": 29,
                            "length": 10
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Super Bowl",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Super_Bowl",
                    "bingId": "ce1fece8-34c4-6249-a1aa-2e779294760e"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
