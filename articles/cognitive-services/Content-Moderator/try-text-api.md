---
title: Модерация текстового содержимого с использованием API модерации текста — Content Moderator
titlesuffix: Azure Cognitive Services
description: Опробуйте модерацию текста с помощью API модерации текста в веб-консоли.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 95fd5507287a9294f4fca6af9cc5f01f0ea9fe1c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219793"
---
# <a name="moderate-text-from-the-api-console"></a>Модерация текста с помощью консоли API

Используйте [API модерации текста](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) в Azure Content Moderator для проверки содержимого текста. Операция проверяет содержимое на наличие ненормативной лексики и сравнивает его с пользовательскими и общими "списками запрещенных".


## <a name="get-your-api-key"></a>Получение ключа API
Прежде чем можно будет опробовать API в веб-консоли, необходимо получить ключ подписки. Он указан на вкладке **Settings** (Параметры) в поле **Ocp-Apim-Subscription-Key**. Дополнительные сведения см. в этом [обзоре](overview.md).

## <a name="navigate-to-the-api-reference"></a>Переход к справочнику по API
Перейдите к [справочнику по API модерации текста](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Откроется страница **Text - Screen** (Текст — проверка).

## <a name="open-the-api-console"></a>Открытие консоли API
Для параметра **Open API testing console** (Открытая тестовая консоль API) выберите регион, лучше всего подходящий для вашего расположения. 

  ![Выбор региона на странице "Text - Screen" (Текст — проверка)](images/test-drive-region.png)

  Откроется консоль API **Text - Screen** (Текст — проверка).

## <a name="select-the-inputs"></a>Выбор входных данных

### <a name="parameters"></a>Параметры
Выберите параметры запроса, которые требуется использовать при проверке текста. В этом примере используется значение по умолчанию для параметра **language** (Язык). Можно также оставить его пустым, так как эта операция автоматически определяет предполагаемый язык в ходе выполнения.

> [!NOTE]
> Для параметра **language** (Язык) задайте значение `eng` или оставьте это поле пустым, чтобы увидеть в ответе результат машинной **классификации**, которая сейчас выполняется в режиме предварительной версии. **Эта функция поддерживает только английский язык**.
>
> Для обнаружения **нецензурных терминов** укажите [код ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) для поддерживаемых языков, которые перечислены в этой статье, или оставьте это поле пустым.

Для параметров **autocorrect** (Автозамена), **PII** (Личные сведения) и **classify (preview)** (Классификация (предварительная версия)) выберите значение **true** (Истина). Оставьте поле **ListId** (ИД списка) пустым.

  ![Параметры запроса в консоли "Text - Screen" (Текст — проверка)](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Тип содержимого
Для параметра **Content-Type** (Тип содержимого) выберите тип проверяемого содержимого. В этом примере используйте тип содержимого по умолчанию, **text/plain**. В поле **Ocp-Apim-Subscription-Key** введите свой ключ подписки.

### <a name="sample-text-to-scan"></a>Пример текста для проверки
В поле **Request body** (Текст запроса) введите какой-либо текст. В следующем примере показана преднамеренная опечатка в тексте.

> [!NOTE]
> Недопустимый номер социального страхования в следующем примере текста указан намеренно. Цель — продемонстрировать пример формата входных и выходных данных.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Функция классификации текста

В следующем примере показан ответ функции машинной классификации текста Content Moderator. Она помогает обнаруживать потенциальное наличие нежелательное содержимое. Отмечаемое ей содержимое может считаться недопустимым в некоторых контекстах. Помимо оценки вероятности отнесения к каждой из используемых категорий, функция может рекомендовать проверку содержимого оператором. Она использует обученную модель для выявления лексики оскорбительного, неуважительного или дискриминационного характера. Сюда входят сленговые и сокращенные выражения, оскорбления и специально искаженные слова, которые нужно проверять. 

#### <a name="explanation"></a>Пояснение

- `Category1` обозначает потенциальное наличие лексики, которая в определенных обстоятельствах может считаться порнографической или предназначенной только для взрослых.
- `Category2` обозначает потенциальное наличие лексики, которая в определенных обстоятельствах может считаться сексуально окрашенной или не предназначенной для детей.
- `Category3` обозначает потенциальное наличие лексики, которая в определенных обстоятельствах может считаться оскорбительной.
- `Score` имеет значение в диапазоне от 0 до 1. Чем выше оценка, тем более подходящей модель считает соответствующую категорию. Эта предварительная версия использует статистическую модель прогнозирования, а не оценки, кодированные вручную. Корпорация Майкрософт рекомендует протестировать ее на своих данных, чтобы проверить применимость анализа по каждой категории.
- `ReviewRecommended` принимает значения true или false в зависимости от внутренних порогов оценки. Клиенты могут выбрать, будут ли они использовать значения по умолчанию или настраивать собственные пороги в соответствии с действующими политиками содержимого.

### <a name="analyze-the-response"></a>Анализ ответа
Приведенный ниже ответ показывает различные важные сведения из API. Он содержит потенциальное наличие ненормативной лексики, личных сведений, результаты классификации (предварительная версия) и вариант автозамены.

> [!NOTE]
> Функция машинной классификации находится в режиме предварительной версии и поддерживает только английский язык.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Подробное описание всех разделов в ответе JSON приведено в [обзоре API модерации текста](text-moderation-api.md).

## <a name="next-steps"></a>Дополнительная информация

Используйте в коде REST API или начните с [краткого руководства по модерации текста с помощью .NET](text-moderation-quickstart-dotnet.md), чтобы интегрировать эту функцию в приложение.
