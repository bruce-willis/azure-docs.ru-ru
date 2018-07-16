---
title: Метод интерпретации в Academic Knowledge API | Документация Майкрософт
description: Используйте метод интерпретации для возврата форматированных интерпретаций строк запроса пользователя на основе данных Academic Graph и Academic Grammar в Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380057"
---
# <a name="interpret-method"></a>Метод интерпретации

**Интерпретация** REST API принимает строку запроса пользователя (т.е. запрос, введенный пользователем приложения) и возвращает форматированные интерпретации намерений пользователя на основе данных Academic Graph и Academic Grammar.

Чтобы обеспечить интерактивный интерфейс, можно повторно вызывать этот метод после каждого символа, введенного пользователем. В этом случае необходимо установить для параметра **complete** значение 1, чтобы включить автоматическое завершение предложения. Если приложению не требуется автозавершение, необходимо установить для параметра **complete** значение 0.

**Конечная точка REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Параметры запроса

ИМЯ     | Значение | Обязательный?  | ОПИСАНИЕ
---------|---------|---------|---------
**query**    | Текстовая строка | Yes | Запрос, введенный пользователем.  Если для параметра complete установлено значение 1, запрос будет интерпретироваться как префикс для создания запросов автозаполнения предложений.        
**model**    | Текстовая строка | Нет   | Имя модели, которую необходимо запросить.  В настоящее время значение по умолчанию соответствует *latest*.        
**complete** | 0 или 1 | Нет <br>по умолчанию:0  | значение 1 означает, что автозавершения предложения создаются на основе данных грамматики и графа.         
**count**    | Number | Нет <br>по умолчанию:10 | Максимальное количество интерпретаций для возврата.         
**offset**   | Number | Нет <br>по умолчанию:0  | Индекс первой интерпретации для возврата. Например, *count=2&offset=0* возвращает интерпретации 0 и 1. *count=2&offset=2* возвращает интерпретации 2 и 3.       
**timeout**  | Number | Нет <br>по умолчанию:1000 | Время ожидания в миллисекундах. Возвращаются только интерпретации, которые найденные до истечения время ожидания.
<br>
  
## <a name="response-json"></a>Ответ (JSON)
ИМЯ     | ОПИСАНИЕ
---------|---------
**query** |Параметр *query* из запроса.
**interpretations** |Массив из 0 или более разных способов сопоставления пользовательского ввода вопреки грамматике.
**interpretations[x].logprob**  |Относительная натуральная вероятность журнала интерпретации. Большие значения более вероятны.
**interpretations[x].parse**  |Строка XML, которая показывает, как интерпретируется каждая часть запроса.
**interpretations[x].rules**  |Массив из 1 или более правил, определенных в грамматике, на которые ссылались во время интерпретации. Для Academic Knowledge API всегда будет существовать одно правило.
**interpretations[x].rules[y].name**  |Имя правила.
**interpretations[x].rules[y].output**  |Вывод правила.
**interpretations[x].rules[y].output.type** |Тип данных вывода правила.  Для Academic Knowledge API всегда будет существовать "query".
**interpretations[x].rules[y].output.value**  |Вывод правила. Для Academic Knowledge API это строка выражения запроса, которая может быть передана методам оценки и вычисления гистограмм.
**aborted** | Если истекло время ожидания запроса, то значение true.

<br>
#### <a name="example"></a>Пример:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Ответ ниже содержит две верхние (из-за параметра *count=2*) наиболее вероятные интерпретации, которые завершают частичный ввод пользователем строки *papers by jaime*: *papers by jaime teevan* и *papers by jaime green*.  Служба сгенерировала завершение запроса вместо рассмотрения только точных совпадений для автора *jaime*, потому что для указанного запроса *complete=1*. Обратите внимание, что каноническое значение *j l green* подобранно через синоним *jamie green*, как указано в анализе.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Чтобы получить результаты сущности для интерпретации, используйте *output.value* из API **интерпретации** и передайте его в API **оценки** через параметр *expr*. В этом примере запрос для первой интерпретации выглядит следующим образом. 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 