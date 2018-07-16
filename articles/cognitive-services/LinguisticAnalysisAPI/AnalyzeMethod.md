---
title: Метод Analyze в API лингвистического Analyze | Документация Майкрософт
description: Как использовать метод Analyze в API лингвистического Analyze для анализа определенных входных данных на естественном языке.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380248"
---
# <a name="analyze-method"></a>Метод Analyze

**Analyze** REST API используется для анализа входных данных на естественном языке.
Это может быть связано только с обнаружением [предложений и токенов](Sentences-and-Tokens.md) внутри входных данных, обнаружением [тегов части речи](POS-tagging.md) или обнаружением [дерева элементов](Constituency-Parsing.md).
Вы можете указать нужные результаты, выбрав соответствующие диагностические анализаторы.
Чтобы просмотреть список всех доступных анализаторов, см. раздел **[Анализаторы](AnalyzersMethod.md)**.

Обратите внимание, что необходимо указать язык входной строки.

**Конечная точка REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Параметры запроса

ИМЯ | type | Обязательно | ОПИСАНИЕ
-----|-------|----------|------------
**language**    | строка | Yes | Двухбуквенный ISO-код языка, который будет использоваться для анализа. Например "en" для английского языка.
**analyzerIds** | Список строк | Yes | Список глобальных уникальных идентификаторов анализаторов для применения. Дополнительную информацию см. в документации по методу Analyzers.
**text**        | строка | Yes | Необработанные входные данные для анализа. Это может быть короткая строка, такая как слово или фраза, полное предложение, целый абзац или часть текста.

<br>
## <a name="response-json"></a>Ответ (JSON)
Массив выходных данных анализа, по одному для каждого атрибута, указанного в запросе.

Результаты будут выглядеть следующим образом:

ИМЯ | type | ОПИСАНИЕ
-----|------|--------------
analyzerId | строка | Глобальный уникальный идентификатор указанного анализатора
result | object | результат анализа

Обратите внимание, что тип результата зависит от типа входных данных анализатора.

### <a name="tokens-response-json"></a>Ответ токенов (JSON)

ИМЯ | type | ОПИСАНИЕ
-----|------|-------------
result | список объектов предложения | границы предложения, определенные в тексте |
result[x].Offset | int | смещение стартового символа для каждого предложения |
result[x].Len | int | количество символов каждого предложения |
result[x].Tokens | список объектов токена | границы токена, определенные в предложении |
result[x].Tokens[y].Offset | int | смещение стартового символа токена |
result[x].Tokens[y].Len | int | количество символов токена |
result[x].Tokens[y].RawToken | строка | символы внутри этого токена, до нормализации |
result[x].Tokens[y].NormalizedToken | строка | обычная форма символа, безопасная для использования в [дереве анализа](Constituency-Parsing.md); например, открытый символ круглой скобки "(" заменяется на "-LRB-" |

Пример ввода: "Это тест. Привет."
Пример ответа в формате JSON:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Ответ POS-тегов (JSON)

Результатом является список из списков строк.
Для каждого предложения есть список POS-тегов, один POS-тег для каждого токена.
Чтобы найти токен, соответствующий каждому POS-тегу, может потребоваться запросить объект разметки.

### <a name="constituency-tree-response-json"></a>Отклик дерева группы (JSON)

Результатом является список строк, по одному дереву анализа для каждого предложения, найденного во входных данных.
Деревья анализа представлены в круглых скобках.

<br>

## <a name="example"></a>Пример

`POST /analyze`

Тело запроса: полезные данные JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?" 
}
```

Ответ: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04", 
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2", 
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```

