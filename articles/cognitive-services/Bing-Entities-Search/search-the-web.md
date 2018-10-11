---
title: Что такое Поиск сущностей Bing?
titlesuffix: Azure Cognitive Services
description: Узнайте, как с помощью API Bing для поиска сущностей искать сущности и места в Интернете.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: overview
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 2b3adf07a8522322434a6596475fa06c0df978e8
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813606"
---
# <a name="what-is-bing-entity-search"></a>Что такое Поиск сущностей Bing?

API Bing для поиска сущностей отправляет запрос на поиск в Bing и получает результаты, которые включают сущности и места. Результаты размещения включают рестораны, гостиницы или другие местные организации. Bing возвращает расположения, если в запросе указывается имя местной организации, или запрашивает ее тип (например, рестораны рядом со мной). Bing возвращает сущности, если в запросе указаны хорошо известные люди, места (туристическая достопримечательность, штаты, страны и т. д.) или вещи.

## <a name="suggesting--using-search-terms"></a>Заполнение и использование условий поиска

Если вы предоставили окно поиска, в котором пользователь вводит свой поисковый запрос, используйте [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), чтобы оптимизировать работу. API возвращает предложенные строки запроса на основе частичного поиска, как пользовательские типы.

После ввода условия поиска URL-адрес закодирует его перед установкой параметра запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query). Например, если пользователь вводит *Marcus Appel*, задайте `q` как *Marcus+Appel* или *Marcus%20Appel*.

Если условие поиска содержит орфографические ошибки, ответ поиска включает объект [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext). Объект показывает исходное написание и исправленное написание, которое Bing использует для поиска.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Запрос сущности

Пример запроса см. в [этой статье](./quick-start.md).

## <a name="the-response"></a>Ответ

Ответ содержит объект [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse). Обнаружив релевантные сущности или места, объект включает поле `entities` или `places`, или оба. В противном случае объект ответа не включает какое-либо поле.
> [!NOTE]
> Ответы сущности поддерживают разные рынки, но ответ Places поддерживает только расположения организаций в США. 

Поле `entities` является объектом [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer), который содержит список объектов [Сущность](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) (см. поле `value`). Список может содержать одну главную сущность, несколько объектов, несколько уточняющих сущностей, или и то, и другое. 

Главная сущность — это сущность, которую Bing считает единственной, соответствующей запросу (не возникло неопределенности, о том, какие сущности соответствующего запросу). Если несколько объектов могут удовлетворять запросу, список содержит несколько объектов для устранения неоднозначности. Например, если запрос использует универсальный заголовок серии фильмов, список, вероятно, будет содержать уточнение сущностей. Но, если в запросе указывается конкретный заголовок из серии фильмов, список, вероятно, будет содержать одну главную сущность.

Сюда входят известные личности, такие как певцы, актеры, спортсмены, модели и т. д; места и достопримечательности, такие как гора Рейнир или Мемориал Линкольна; и такие вещи, как бананы, золотые украшения, книги или название фильма. Поле [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) содержит указания, которые определяют тип сущности. Например, если это лицо, фильм, животное или достопримечательность. Список возможных типов см. в разделе [Entity Types](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types) (Типы сущностей).

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Ниже показан ответ, который включает главную и уточняющую сущность.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

В сущность включены поля `name`, `description` и `image`. Когда вы показываете эти поля в своем пользовательском интерфейсе, им нужно присвоить атрибуты. Поле `contractualRules` содержит список атрибутов, которые необходимо применить. Правило контракта определяет поле, к которому применяется атрибут. Сведения о применении определения принадлежности см. в [этом разделе](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

При отображении информации о сущности (имя, описание и изображение) нужно использовать URL-адрес в поле `webSearchUrl` для ссылки на страницу результатов поиска Bing, содержащую объект.


Поле `places` является объектом [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer), который содержит список объектов [Место](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) (см. поле `value`). Список содержит одну или несколько локальных сущностей, которые соответствуют запросу.

Места включают рестораны, гостиницы или локальные организации. Поле [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) содержит указания, которые определяют тип локальной сущности. Список содержит перечисленные указания, например место, локальные организации, ресторан. Каждое последующее указание в массиве сужает тип сущности. Список возможных типов см. в разделе [Entity Types](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types) (Типы сущностей).

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Ответы сущности поддерживают разные рынки, но ответ Places поддерживает только расположения организаций в США. 

Локальные запросы на объекты, такие как *ресторан рядом со мной*, требуют расположение пользователя, чтобы давать точные результаты. Запросы всегда должны использовать заголовки X-Search-Location и X-MSEdge-ClientIP для указания расположения пользователя. Если Bing считает, что запрос из расположения пользователя будет полезен, он устанавливает полю `askUserForLocation` [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) значение **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Результат запроса места включает имя расположения, адрес, номер телефона и URL-адрес для веб-сайта сущности. При отображении информации о сущности нужно использовать URL-адрес в поле `webSearchUrl` для ссылки на страницу результатов поиска Bing, содержащую объект.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Вы или третья сторона, действующая от вашего имени, можете не использовать, хранить, кэшировать, распределять данные API сущностей URL-адресов или предоставлять доступ к ним с целью тестирования, разработки, обучения, распределения или предоставления доступа любой сторонней службе или функции (не от корпорации Майкрософт).  

## <a name="data-attribution"></a>Определение принадлежности данных

Ответы API сущности Bing в проекте содержат сведения, принадлежащие третьим лицам. Вы несете ответственность за их должное использование, например за соблюдение условий лицензирования Creative Commons в пользовательском интерфейсе.

Если ответ или результат содержит поля `contractualRules`, `attributions` или `provider`, вы должны определить принадлежность данных. Если эти поля отсутствуют, это делать не нужно. Когда ответ содержит поле `contractualRules` и поле `attributions`, и (или) `provider`, принадлежность данных следует определить с помощью договорных правил.

В приведенном ниже примере показана сущность, содержащая договорное правило MediaAttribution и раздел image с полем `provider`. Правило MediaAttribution определяет раздел image как целевой объект, поэтому вы должны игнорировать поле `provider` этого раздела и вместо этого определить принадлежность данных с помощью правила MediaAttribution.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Если договорное правило содержит поле `targetPropertyName`, это правило применяется только к целевому полю. В противном случае правило применяется к родительскому объекту, который содержит поле `contractualRules`.

В приведенном ниже примере правило `LinkAttribution` содержит поле `targetPropertyName`, поэтому это правило применяется к полю `description`. Если правила, применяемые к определенным полям, сразу после целевых данных необходимо включить строку, содержащую гиперссылки на веб-сайта поставщика. Например, чтобы определить принадлежность текста описания, сразу после него включите строку с гиперссылкой на веб-сайта поставщика. В этом случае создайте ссылку на сайт contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Определение принадлежности лицензии

Если список договорных правил содержит правило [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), сразу после содержимого, к которому применяется лицензия, необходимо добавить строку уведомления. Правило `LicenseAttribution` определяет свойство, к которому применяется лицензия, с помощью поля `targetPropertyName`.

Ниже приведен пример с использованием правила `LicenseAttribution`.

![Определение принадлежности лицензии](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Уведомление о лицензии должно содержать гиперссылку на сайт со сведениями о лицензии. Как правило, имя лицензии делают гиперссылкой. Например, если уведомление содержит описание **Текст лицензии CC-BY-SA**, а CC-BY-SA — это имя лицензии, то CC-BY-SA нужно сделать гиперссылкой.

### <a name="link-and-text-attribution"></a>Определение принадлежности текста или ссылки

Как правило, правила [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) и [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) определяют поставщика данных. Поле `targetPropertyName` определяет поле, к которому применяется правило.

Чтобы определить принадлежность поставщиков, сразу после содержимого, к которому применяется определение принадлежности (например, целевое поле), добавьте строку. Строка должна четко указывать, что данные принадлежат этим поставщикам. Например, "Данные с contoso.com". Если используются правила `LinkAttribution`, необходимо создать гиперссылку на веб-сайта поставщика.

Ниже приведен пример с использованием правил `LinkAttribution` и `TextAttribution`.

![Определение принадлежности ссылки](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Определение принадлежности медиаданных

Если сущность содержит изображение, необходимо указать ссылку для перехода на веб-сайта поставщика. Если сущность включает правило [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution), создайте такую ссылку, указав URL-адрес правила. В противном случае используйте URL-адрес в поле `provider` изображения.

Ниже приведен пример, в котором используется поле `provider` изображения и договорные правила. Так как в этом примере содержится договорное правило, вы должны игнорировать поле `provider` изображения и применить правило `MediaAttribution`.

![Определение принадлежности медиаданных](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Поиск и подобные функции

Как и при использовании API Bing для поиска в Интернете, API Bing для поиска сущностей может использоваться только в результате прямого запроса пользователя или поиска, или в результате действия в приложении или пользовательском интерфейсе, который логически может быть интерпретирован как запрос на поиск пользователя. Для иллюстрации ниже приведены следующие примеры приемлемых запросов на поиск или подобные функции.

- Пользователь вводит запрос непосредственно в поле поиска в приложении
- Пользователь выбирает определенный текст или изображение и запрашивает "Подробнее" или "Дополнительные сведения".
- Пользователь запрашивает программу-робот про поиск об определенной теме
- Пользователь останавливается на конкретном объекте или объекте в сценарии типа визуального поиска

Если вы не уверены, что вашу среду можно считать обладающей схожими с поиском функциями, рекомендуется проверить ее с помощью Майкрософт.

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как быстро создать первый запрос, см. в [этой статье](./quick-start.md).

Ознакомьтесь со статьей [Bing Entity Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) (Руководство по API Bing для поиска сущностей версии 7). Руководство содержит заголовки и параметры запроса, которые будут использоваться для запроса результатов поиска. Оно также содержит определения объектов ответа. 

Дополнительные сведения о том, как улучшить работу пользователя в окне поиска, см. в статье [What is Bing Autosuggest?](../bing-autosuggest/get-suggested-search-terms.md) (Что такое Автозаполнение Bing?). Когда пользователь вводит условие поиска, вы можете вызвать этот API для получения релевантных условий запроса, которые использовались другими.

Обязательно прочтите [эту статью](./use-display-requirements.md), чтобы не нарушать какие-либо правила использования результатов поиска.