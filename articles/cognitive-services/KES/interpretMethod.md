---
title: Метод interpret в API службы поиска и распознавания данных | Документация Майкрософт
description: Узнайте, как использовать метод interpret в API службы поиска и распознавания данных (KES) в Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380233"
---
# <a name="interpret-method"></a>Метод interpret
Метод *interpret* принимает строку запроса на естественном языке и возвращает отформатированные интерпретации намерений пользователя на основе грамматики и данных индекса.  Для обеспечения интерактивного поиска этот метод может быть вызван при вводе каждого знака пользователем. Для этого параметру *complete* необходимо присвоить значение 1, чтобы включить автозавершение.

## <a name="request"></a>Запрос
`http://<host>/interpret?query=<query>[&<options>]`

ИМЯ|Значение| ОПИСАНИЕ
----|----|----
query    | Текстовая строка | Запрос, введенный пользователем.  Если для параметра complete установлено значение 1, запрос будет интерпретироваться как префикс для создания автозавершения запросов.        
complete | 0 (по умолчанию) или 1 | Значение 1 означает, что предложения автозавершения создаются на основе грамматики и данных индекса.         
count    | Число (по умолчанию — 10) | Максимальное количество возвращаемых интерпретаций.         
offset   | Число (по умолчанию — 0) | Индекс первой интерпретации для возврата.  Например, *count=2&offset=0* возвращает интерпретации 0 и 1. *count=2&offset=2* возвращает интерпретации 2 и 3.       
timeout  | Число (по умолчанию — 1000) | Время ожидания в миллисекундах. Возвращаются только интерпретации, которые найдены до истечения времени ожидания.

С помощью параметров *count* и *offset* можно постепенно получать все больше результатов, используя несколько запросов.

## <a name="response-json"></a>Ответ (JSON)
JSONPath     | ОПИСАНИЕ
---------|---------
$.query |Параметр *query* из запроса.
$.interpretations   |Массив, содержащий 0 или более способов сопоставления входного запроса и грамматики.
$.interpretations[\*].logprob   |Относительная логарифмическая вероятность интерпретации (меньше 0).  Чем больше значение, тем выше вероятность.
$.interpretations[\*].parse |Строка XML, которая показывает, как интерпретируется каждая часть запроса.
$.interpretations[\*].rules |Массив, содержащий 1 или более правил, определенных в грамматике, вызванной во время интерпретации.
$.interpretations[\*].rules[\*].name    |Имя правила.
$.interpretations[\*].rules[\*].output  |Семантические выходные данные правила.
$.interpretations[\*].rules[\*].output.type |Тип данных семантических выходных данных.
$.interpretations[\*].rules[\*].output.value|Значение семантических выходных данных.  
$.aborted | Если истекло время ожидания запроса, то имеет значение true.

### <a name="parse-xml"></a>Анализ XML
При анализе XML в запрос (выполненный) добавляются аннотации со сведениями о степени соответствия правилам грамматики и атрибутам в индексе.  Ниже приведен пример из области научных публикаций.

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

Элемент `<rule>` задает границы диапазона в запросе, обеспечивая сопоставление с правилом, определенным атрибутом `name`.  Он может быть вложенным, если при анализе используются ссылки на правила в грамматике.

Элемент `<attr>` задает границы диапазона в запросе, обеспечивая сопоставление с атрибутом индекса, определенным атрибутом `name`.  Если при поиске совпадений во входном запросе используется синоним, атрибут `canonical` будет содержать каноническое значение, соответствующее синониму из индекса.

## <a name="example"></a>Пример
Приведенный ниже запрос из примера для научных публикаций возвращает не более 2 предложений автозавершения префикса запроса "papers by jaime".

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Ответ содержит две верхние ("count=2") наиболее вероятные интерпретации, которые завершают частичный запрос "papers by jaime": "papers by jaime teevan" и "papers by jaime green".  Служба сгенерировала завершение запроса вместо рассмотрения только точных совпадений для автора "jaime", потому что для указанного запроса "complete=1". Обратите внимание, что каноническое значение "j l green" подобранно через синоним "jamie green", как указано в анализе.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Если выбран тип семантических выходных данных "query", как показано в этом примере, то соответствующие объекты можно получить, передав *output.value* в API [*evaluate*](evaluateMethod.md) посредством параметра *expr*.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
