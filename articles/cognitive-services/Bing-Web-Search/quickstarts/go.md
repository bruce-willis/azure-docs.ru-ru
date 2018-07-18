---
title: Краткое руководство по Azure Cognitive Services и API "Поиск в Интернете Bing" для Go | Документация Майкрософт
description: Быстрое начало использования языка Go в запросах API "Поиск в Интернете Bing", который входит в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
author: Nhoya
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 03/09/2018
ms.author: rosh
ms.reviewer: nhoyadx@gmail.com, v-gedod
ms.openlocfilehash: 86cb67d46bca40c83c2f175ab7fdf6fbf52cb02f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380889"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-go"></a>Вызов и ответ: ваш первый запрос API Bing для поиска в Интернете на Go

Как и сайт Bing.com, API "Поиск в Интернете Bing" возвращает результаты поиска, соответствующие запросу пользователя. Результаты могут содержать веб-страницы, изображения, видео, новости и сущности наряду со связанными поисковыми запросами, исправлениями орфографических ошибок, часовыми поясами, преобразованием единиц измерения, переводами и вычислениями. Результаты зависят от релевантности и уровня API-интерфейсов поиска Bing, на которые вы подписаны.

Подробные сведения об API-интерфейсах см. в [справочнике по API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

## <a name="prerequisites"></a>предварительным требованиям
Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

Установите [двоичные файлы Go](https://golang.org/dl/).
 
В этом руководстве используются только **основные** библиотеки, поэтому внешние зависимости отсутствуют.

## <a name="core-libraries"></a>Основные библиотеки

С помощью `http` можно отправить запрос к конечной точке, с помощью `ioutil` — считать ответ, с помощью `time` и `json` — обработать JSON, а с помощью `fmt` — вывести выходные данные.

```
package main

import (
    "fmt"
    "net/http"
    "io/iutil"
    "time"
    "encoding/json"
)
```

## <a name="define-variables"></a>Определение переменных
Задайте конечную точку API и условие поиска.

```
//This is the valid endpoint at the time of the writing
const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
//API token
token := "YOUR-ACCESS-KEY"
searchTerm := "Microsoft Cognitive Services"
```

## <a name="building-and-sending-the-request"></a>Создание и отправка запроса

```
//Declare a new GET request
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}
//Add the payload to the request
param := req.URL.Query()
param.Add("q", searchTerm)
//Encoding the payload
req.URL.RawQuery = param.Encode()

//Insert the API token in the request header
req.Header.Add("Ocp-Apim-Subscription-Key", token)

//create new client
client := new(http.Client)
//Send the request
resp, err := client.Do(req)
if err != nil {
    panic(err)
}
//Close the response body at the function exit
defer resp.Body.Close() 
```

## <a name="printing-the-answer"></a>Вывод ответа
Результат поиска находится внутри переменной `resp`. Выведите текст ответа из переменной.

```
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
//Convert body from byte to string.
fmt.Println(string(body))
```

## <a name="put-everything-together"></a>Итоговое объединение

```
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

//The struct that will contain the answer
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"
    
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }
    
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    req.Header.Add("Ocp-Apim-Subscription-Key", token)
    
    client := new(http.Client)
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close() 
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    //creating a new answer struct
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }
    //Iterate over search results
    for _, result := range ans.WebPages.Value {
         //Printing result name and URL
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="next-steps"></a>Дополнительная информация

[Обзор API Bing для поиска в Интернете](../overview.md)  
[Пробная версия](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Получение ключа доступа к бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Справочник по API "Поиск в Интернете Bing"](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

