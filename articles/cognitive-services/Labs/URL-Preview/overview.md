---
title: Предварительный просмотр URL-адресов в проектах в Microsoft Cognitive Services | Документация Майкрософт
description: Общие сведения о предварительном просмотре URL-адресов в проектах.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381749"
---
# <a name="what-is-project-url-preview"></a>Предварительный просмотр URL-адресов в проектах
Конечная точка предварительного просмотра URL-адресов принимает параметр запроса URL-адреса и возвращает ответ JSON с именем целевого ресурса, кратким описанием и ссылкой на изображение для отображения в режиме предварительного просмотра. Также в ответ включается флаг [isFamilyFriendly](url-preview-reference.md#query-parameters), который указывает, содержит ли URL-адрес содержимое, предназначенное только для взрослых, а также пиратское или другое незаконное содержимое. 

Чтобы получить результаты предварительного просмотра URL-адреса, отправьте запрос GET и включите в него заголовок *Ocp-Apim-Subscription-Key* с действующим токеном:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Ответ: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Сценарии 

API предварительного просмотра URL-адресов поддерживает краткие описания веб-ресурсов. Разработчики с его помощью могут создавать многофункциональные сценарии предварительного просмотра.  Пользователи могут предоставлять в общий доступ или добавлять в закладки веб-страницы, новости, блоги, форумы и т. д. Также этот API можно использовать для модерации содержимого.    

В приложениях предварительный просмотр URL-адресов используется для отправки веб-запросов к конечной точке с запросом, назначенным URL-адресу для предварительного просмотра.  Ответ JSON содержит сведения для предварительного просмотра, включая имя, описание ресурса, флаг *familyFriendly*, а также ссылки для доступа к демонстрационному изображению и полному ресурсу в Интернете. 

## <a name="terms-of-use"></a>Условия использования
Используйте только данные предварительного просмотра URL-адресов в проекте для предварительного отображения фрагментов кода и эскизов изображений, связанных гиперссылками с исходными сайтами, при инициированном пользователем предоставлении доступа к URL-адресам в социальных сетях, чат-ботах и подобных предложениях. Не копируйте, не сохраняйте и не кэшируйте данные, полученные при предварительном просмотре URL-адресов в проекте. Учитывайте любые запросы на отключение предварительного просмотра, которые вы можете получить от владельцев веб-сайтов или содержимого.

Ни вы, ни другие пользователи, действующие от вашего имени, не могут использовать, хранить, сохранять, кэшировать, распределять данные API предварительного просмотра URL-адресов или предоставлять доступ к ним для тестирования, разработки, обучения, распределения или предоставления доступа к любой сторонней службе или функции (не от корпорации Майкрософт). 

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство — C#](csharp.md)
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство — Node](node-quickstart.md)
- [Краткое руководство — Python](python-quickstart.md)
