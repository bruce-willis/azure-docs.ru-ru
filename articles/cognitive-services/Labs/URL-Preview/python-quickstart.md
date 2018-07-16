---
title: Краткое руководство по Python. Использование службы предварительного просмотра URL-адресов в проектах в Microsoft Cognitive Services | Документация Майкрософ
description: Пример скрипта для быстрого начала работы со службой предварительного просмотра URL-адресов в проектах, входящей в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381768"
---
# <a name="url-preview-python-quickstart"></a>Краткое руководство по использованию службы предварительного просмотра URL-адресов для Python

Следующий пример Python создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

В этом примере используется Python 3.6.

## <a name="code-scenario"></a>Сценарий кода 

Следующий код создает представление для предварительного просмотра URL-адреса.
Он реализуется с помощью следующих действий:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Укажите URL-адрес запроса для предварительного просмотра и добавьте параметр запроса.  
3. Задайте параметр запроса.
4. Определите функцию поиска, которая создает запрос и добавляет заголовок *Ocp-Apim-Subscription-Key*.
5. Задайте заголовок *Ocp-Apim-Subscription-Key*. 
6. Выполните подключение и отправьте запрос.
7. Выведите на экран результаты JSON.

Полный код этого примера выглядит так:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство — C#](csharp.md)
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство по использованию службы предварительного просмотра URL-адресов для Node](node-quickstart.md)