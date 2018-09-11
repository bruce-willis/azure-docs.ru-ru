---
title: Перевод текста с помощью API перевода текстов и Go | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как перевести текст с одного языка на другой, используя API перевода текстов с Go в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 72d4a3bc7563d6427335f1536f787eab6d7a0de4
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2018
ms.locfileid: "43771315"
---
# <a name="quickstart-translate-text-with-go"></a>Краткое руководство по переводу текста с помощью Go

Из этого краткого руководства вы узнаете, как перевести текст с одного языка на другой, используя API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется установить [дистрибутив Go](https://golang.org/doc/install). В примере кода используются только **основные** библиотеки, поэтому внешние зависимости отсутствуют.

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Запрос метода Translate

В приведенном ниже коде исходный текст переводится с одного языка на другой с помощью метода [Translate](./reference/v3-0-translate.md).

1. Создайте проект Go в любом редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Сохраните файл с расширением .go.
5. Откройте командную строку на компьютере, на котором установлен язык Go.
6. Скомпилируйте файл, например go build quickstart-translate.go.
7. Запустите файл, например quickstart-translate.

```golang
package translate

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/translate?api-version=3.0"

    // Translate to German and Italian
    const params = "&to=de&to=it"

    const uri = uriBase + uriPath + params

    const text = "Hello, world!"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
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

## <a name="translate-response"></a>Ответ метода Translate

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о пакетах Go для API-интерфейсов Cognitive Services из [Azure SDK для Go](https://github.com/Azure/azure-sdk-for-go) см. на сайте GitHub.

> [!div class="nextstepaction"]
> [Подробнее о пакетах Go на сайте GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
