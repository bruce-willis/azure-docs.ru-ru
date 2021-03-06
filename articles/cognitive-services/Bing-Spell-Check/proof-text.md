---
title: Что такое API Bing для проверки орфографии?
titlesuffix: Azure Cognitive Services
description: API Bing для проверки орфографии использует машинное обучение и статистический машинный перевод для контекстной проверки орфографии.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 81c80ab6c8d10d263de96566f5554709a2404a24
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802599"
---
# <a name="what-is-bing-spell-check-api"></a>Что такое API Bing для проверки орфографии?

API Bing для проверки орфографии позволяет выполнять контекстную проверку орфографии и грамматики.

Какова разница между обычными средствами проверки орфографии и средством проверки орфографии Bing третьего поколения? Обычные средства проверки орфографии проверяют правописание и грамматику с использованием наборов правил на основе словаря, который периодически обновляется и расширяется. Другими словами, средство проверки орфографии настолько же надежно, как связанный с ним словарь, и зависит от редакторов, которые поддерживают этот словарь. Средства проверки орфографии такого типа используются в Microsoft Word и других текстовых редакторах.

Для сравнения Bing разработала веб-средство проверки орфографии, которое использует машинное обучение и статистический машинный перевод для динамического обучения высококонтекстуального и постоянно развивающегося алгоритма. Средство проверки орфографии создано на базе большого количества документации и веб-поисков.

Это средство проверки орфографии может справиться с любым сценарием обработки текстовых данных:

- распознавание сленговых и разговорных выражений;
- определение стандартных ошибок в именах в контексте;
- исправление проблем, связанных с переносом слов, с помощью одного флага;
- исправление омофонов в контексте и других трудновыявляемых ошибок;
- определение новых торговых марок, названий цифровых развлечений и популярных выражений по мере их появления;
- определение слов, которые звучат одинаково, но имеют разные значения и правописание, например "see" и "sea".

## <a name="spell-check-modes"></a>Режимы проверки орфографии

API поддерживает два режима проверки орфографии: `Proof` и `Spell`.  Примеры см. [здесь](https://azure.microsoft.com/en-us/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Режим Proof — для сценариев с документами
`Proof` является режимом по умолчанию. Режим проверки орфографии `Proof` предоставляет наиболее комплексные проверки (проверка капитализации, базовой пунктуации) и другие возможности, которые помогают в создании документа. Но он доступен только для языковых стандартов en-US (английский — США), es-ES (Испания) и pt-BR (Португалия). (Примечание. Для Испании и Португалии доступна только бета-версия.) Для других языковых стандартов (рынков) установите для параметра запроса mode значение Spell. 
<br /><br/>**Примечание.** Если длина текста запроса превышает 4096 символов, он будет усечен до этого количества, а затем обработан. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell — для сценариев с веб-поисками или запросами
`Spell` отличается более строгими требованиями и возвращает более точные результаты поиска. Режим `Spell` определяет большинство орфографических ошибок, но не находит некоторые грамматические ошибки, которые улавливает `Proof`, например регистр букв и повторяющиеся слова.
<br /></br>**Примечание.** Поддерживаемая максимальная длина запроса описана ниже. Если запрос превышает границы, в результате указывается, что запрос не изменен.
<ul><li>130 символов для кодов языков en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. </li>
<li>65 символов для других кодов языков.</li></ul>

## <a name="market-setting"></a>Настройка рынка
Рынок необходимо указать в параметре запроса в самом URL-адресе, в противном случае средство проверки установит рынок по умолчанию на основе IP-адреса.


## <a name="post-vs-get"></a>Запрос POST и ПОЛУЧЕНИЕ

Этот API поддерживает либо HTTP-запрос POST, либо HTTP-запрос GET. Используемый запрос зависит от длины текста, который нужно проверить. Если строки всегда меньше 1500 символов, можно использовать запрос GET. Но если вам нужно поддерживать строки размером до 10 000 символов, используйте запрос POST. Текстовая строка может содержать любой допустимый символ в формате UTF-8.

В следующем примере показан запрос POST для проверки орфографии и грамматики текстовой строки. Пример включает в себя параметр запроса [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) для полноты информации (его можно опустить, так как параметр `mode` по умолчанию имеет значение Proof). Параметр запроса [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) содержит строку, которую нужно проверить.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

При использовании HTTP-запроса GET следует включить параметр запроса `text` в строку запроса с URL-адресом.
  
Ниже показан ответ на предыдущий запрос. Ответ будет содержать объект [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck). 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
В поле [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) перечислены орфографические и грамматические ошибки, найденные API в строке [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text). Поле `token` содержит слова для замены. Используйте смещение, отсчитываемое с нуля, в поле `offset`, чтобы найти токен в строке `text`. Необходимо заменить слово в данном расположении на слово из поля `suggestion`. 

Если в поле `type` указано значение RepeatedToken, вам все еще нужно заменить токен на `suggestion`, но, скорее всего, потребуется удалить конечный пробел.

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как быстро создать первый запрос, см. в [этой статье](quickstarts/csharp.md).

Ознакомьтесь со справочными материалами по [API проверки орфографии Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). Руководство содержит список конечных точек, заголовков и параметров запроса, которые будут использоваться для запроса результатов поиска, а также определения объектов ответа. 

Обязательно прочтите [эту статью](./useanddisplayrequirements.md), чтобы не нарушать какие-либо правила использования результатов.
