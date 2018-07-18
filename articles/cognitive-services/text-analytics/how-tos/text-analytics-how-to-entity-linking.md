---
title: Инструкции по связыванию сущностей в анализе текста REST API (Microsoft Cognitive Services в Azure) | Документация Майкрософт
description: Это пошаговое руководство содержит сведения об обнаружении и распознавании сущностей с помощью анализа текста REST API в Microsoft Cognitive Services в Azure.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380900"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>Обнаружение связанных сущностей в службе Анализ текста (предварительная версия)

[API связывание сущности](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) принимает неструктурированный текст и возвращает для каждого документа JSON список однозначно разрешенных сущностей со ссылками на дополнительные сведения в Интернете (Википедия и Bing). 

## <a name="entity-linking-vs-named-entity-recognition"></a>Связывание сущностей и Распознавание именованных сущностей

В предметной области обработки естественного языка можно легко спутать концепции связывания сущности и распознавания именованных сущностей. В предварительной версии конечной точки `entities` Текстовой аналитики поддерживается только связывание сущностей.

Связывание сущностей позволяет идентифицировать сущности, обнаруженные в тексте и устранить неоднозначности (например, упоминание слова "Марс" может обозначать планету или римского бога войны). Этот процесс требует наличия базы знаний, с которыми будут связываться распознанные сущности. Например, конечная точка `entities` Текстовой аналитики использует в качестве такой базы знаний сайт Википедии.

### <a name="language-support"></a>Поддержка языков

Связывание сущностей на разных языках требует наличия базы знаний для каждого из этих языков. В Текстовой аналитике это означает, что конечная точка `entities` для распознанных сущностей на каждом поддерживаемом языке предоставляет ссылку на соответствующий раздел Википедии на этом языке. Поскольку наполнение Википедии существенно различается для разных языков, функциональность связывания сущностей также будет иметь существенные различия.


## <a name="preparation"></a>Подготовка

Необходимо иметь документы JSON в следующем формате: "идентификатор","текст","язык".

Поддерживаемые языки перечислены в [этом списке](../text-analytics-supported-languages.md).

Документ должен содержать менее 5 000 символов, и иметь до 1 000 элементов (идентификаторов) в коллекции. Коллекция передается в тексте запроса. Ниже приведен пример иллюстрации содержимого, который можно отправлять для связывания сущностей.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Шаг 1: Структура запроса

Сведения об определении запроса можно найти в [Способ вызова API анализ текста](text-analytics-how-to-call-api.md). Для удобства повторим следующие моменты.

+ Создайте запрос **POST**. Изучите документацию по API для этого запроса: [API связывания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

+ Создайте конечную точку HTTP для извлечения ключевых фраз. Она должна включать ресурс `/entities`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ Задайте заголовок запроса, чтобы включить ключ доступа для операций Текстовой аналитики. Дополнительные сведения см. в статье [о поиске конечных точек и ключей доступа](text-analytics-how-to-access-key.md).

+ В тексте запроса укажите набор документов JSON, которые подготовлены для этого анализа.

> [!Tip]
> Используйте [Postman](text-analytics-how-to-call-api.md) или откройте **консоль тестирования API** в [документации](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) чтобы создать запрос правильной структуры и передать его в службу в запросе POST.

## <a name="step-2-post-the-request"></a>Шаг 2: Передача запроса

Анализ выполняется при получении запроса. Служба принимает до 100 запросов в минуту. Каждый запрос не должен превышать 1 МБ.

Не забывайте, что эта служба работает без отслеживания состояния. Данные не хранятся в учетной записи. Результаты возвращаются немедленно в ответе.

## <a name="step-3-view-results"></a>Шаг 3: Просмотр результатов

Все запросы POST вернут ответ в формате JSON с идентификаторами и обнаруженными свойствами.

Вывод возвращается немедленно. Можно выполнить потоковую передачу результатов в приложение, которое принимает JSON или сохранить выходные данные в файле локальной системы, а затем импортировать их в приложение, которое дает возможность сортировки, поиска и управления данными.

Далее показан пример выходных данных для связывания сущностей:

```
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
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

Всегда, если это возможно, ответ включает идентификатор Википедии, URL-адрес Википедии и идентификатор Bing для каждой обнаруженной сущности. С их помощью вы можете расширить возможности приложения, используя дополнительные сведения о связанных сущностях.


## <a name="summary"></a>Сводка

В этой статье рассматриваются основные понятия и рабочий процесс связывания сущностей в службе Текстовой аналитики Cognitive Services. Краткая сводка.

+ [API связывания сущностей](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) доступен для некоторых языков.
+ В тексте запроса передаются документы JSON, которые содержат идентификатор, текст и код языка.
+ Отправьте запрос POST на конечную точку `/entities`, используя правильные значения [ключа доступа и конечной точки](text-analytics-how-to-access-key.md) из вашей подписки.
+ Ответ будет содержать выходные данные, содержащие связанные сущности (включая оценки уверенности, смещения и веб-ссылки для каждого идентификатора документа), которые можно использовать в любом приложении

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения о Текстовой аналитике)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)</br>
 [Страница продукта для Текстовой аналитики](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [API анализа текста](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
