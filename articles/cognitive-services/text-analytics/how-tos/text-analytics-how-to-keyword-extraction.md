---
title: Инструкции по извлечению ключевой фразы в REST API анализа текста (Microsoft Cognitive Services в Azure) | Документация Майкрософт
description: Инструкции по извлечению ключевой фразы с помощью REST API анализа текста в Microsoft Cognitive Services в Azure в этом пошаговом руководстве.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380580"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Инструкции по извлечению ключевых фраз при анализе текста

[API извлечения ключевых фраз](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) оценивает неструктурированный текст и для каждого документа JSON возвращает список ключевых фраз. 

Эта возможность полезна, если необходимо быстро определить основные тезисы в коллекции документов. Например, данный входной текст "Еда была вкусной, и были замечательные сотрудники", служба вернет основные тезисы в записи: "еда" и "замечательные сотрудники".

В настоящее время извлечение ключевых фраз поддерживает английский, немецкий, испанский и японский языки. Другие языки находятся на этапе предварительной версии. Дополнительные сведения см. в [списке поддерживаемых языков](../text-analytics-supported-languages.md).

## <a name="preparation"></a>Подготовка

Извлечение ключевых фраз лучше всего работает с большими фрагментами текста. В отличие от анализа тональности, который эффективнее работает на маленьких блоках текста. Для получения наилучших результатов обеих операций советуем реструктуризировать входные данные соответствующим образом.

Необходимо иметь документы JSON в следующем формате: "идентификатор","текст","язык"

Документ должен содержать менее 5000 символов и иметь до 1 000 элементов (идентификаторов) в коллекции. Коллекция передается в тексте запроса. Ниже приведен пример иллюстрации содержимого, которое можно отправлять для извлечения ключевых фраз.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>Шаг 1. Структурирование запроса

Сведения об определении запроса можно найти в статье [How to call the Text Analytics REST API](text-analytics-how-to-call-api.md) (Способ вызова REST API анализа текста). Для удобства повторим следующие моменты.

+ Создайте запрос **POST**. Проверьте документацию по API для этого запроса: [API ключевых фраз](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Создайте конечную точку HTTP для извлечения ключевых фраз. Она должна включать ресурс `/keyphrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Задайте заголовок запроса, чтобы включить ключ доступа для операций Анализа текста. Дополнительные сведения см. в статье [о поиске конечных точек и ключей доступа](text-analytics-how-to-access-key.md).

+ В тексте запроса укажите набор документов JSON, которые подготовлены для этого анализа

> [!Tip]
> Используйте [Postman](text-analytics-how-to-call-api.md) или откройте **консоль тестирования API** в [документации](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6), чтобы структурировать запрос и передать его (используя метод POST) в службу.

## <a name="step-2-post-the-request"></a>Шаг 2. Передача запроса

Анализ выполняется при получении запроса. Служба принимает до 100 запросов в минуту. Каждый запрос не должен превышать 1 МБ.

Не забывайте, что эта служба работает без отслеживания состояния. Данные не хранятся в учетной записи. Результаты возвращаются немедленно в ответе.

## <a name="step-3-view-results"></a>Шаг 3. Просмотр результатов

Все запросы POST вернут ответ в формате JSON с идентификаторами и обнаруженными свойствами.

Вывод возвращается немедленно. Можно выполнить потоковую передачу результатов в приложение, которое принимает JSON, или сохранить выходные данные в файле локальной системы, а затем импортировать их в приложение, которое дает возможность сортировки, поиска данных и управления ими.

Далее показан пример выходных данных для извлечения ключевой фразы.

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Как уже отмечалось, анализатор находит и отбрасывает слова, которые не представляют смысла и сохраняет одиночные термины или фразы, которые могут быть темой или объектом предложения. 

## <a name="summary"></a>Сводка

В этой статье рассматриваются основные понятия и рабочий процесс извлечения ключевой фразы с помощью анализа текста в Cognitive Services. В разделе "Сводка" сделайте следующее.

+ [API извлечения ключевых фраз](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) доступен для некоторых языков.
+ В тексте запроса передаются документы JSON, которые содержат идентификатор, текст и код языка.
+ Запрос POST передается в конечную точку `/keyphrases`, используя личный [ключ доступа и конечную точку](text-analytics-how-to-access-key.md), допустимые для вашей подписки.
+ Выходные данные ответа, состоящие из ключевых слов и фраз для каждого идентификатора документа, могут передаваться в любое приложение, поддерживающее формат JSON, например Excel и Power BI.

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)</br>
 [Анализ текста](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [API анализа текста](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
