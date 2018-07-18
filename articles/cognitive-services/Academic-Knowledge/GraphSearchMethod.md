---
title: Метод поиска с помощью графа в Academic Knowledge API | Документация Майкрософт
description: Метод поиска с помощью графа в Academic Knowledge API используется для возвращения набора учебных сущностей на основе конкретных шаблонов графа в Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d811db117c934c0d41fbfea1220d241cc022e4a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380017"
---
# <a name="graph-search-method"></a>Метод поиска с помощью графа

**Поиск с помощью графа** REST API используется для возвращения набора учебных сущностей на основе данных шаблонов графа.  Ответ представляет собой набор путей графа, которые удовлетворяют ограничениям, заданным пользователем. Путь графа представляет собой чередующуюся последовательность узлов и ребер графа в виде _v0, e0, v1, e1, ..., vn_, где _v0_ является исходным узлом пути.
<br>

**Конечная точка REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Параметры запроса  
ИМЯ     | Значение | Обязательный?  | ОПИСАНИЕ
-----------|-----------|---------|--------
**режим**       | Текстовая строка | Yes | Имя режима, который необходимо использовать. Значение равно *json* или *lambda*.

Метод поиска с помощью графа должен вызываться через запрос POST протокола HTTP. Запрос POST должен включать заголовок типа содержимого: **application/json**.

##### <a name="json-search"></a>Поиск JSON 

Для поиска *json* текст POST является объектом JSON. Объект JSON описывает шаблон пути с ограничениями, заданными пользователем (см. [JSON Search Syntax](JSONSearchSyntax.md) (Синтаксис поиска JSON) для поиска *json*).


##### <a name="lambda-search"></a>Лямбда-поиск

Для *лямбда*-поиска текст POST является строкой простого текста. Текст POST является строкой лямбда-запроса LINQ, которая представляет собой единый оператор C# (см. [Lambda Search Syntax](LambdaSearchSyntax.md) (Синтаксис лямбда-поиска) для *лямбда*-поиска). 

<br>
## <a name="response-json"></a>Ответ (JSON)
ИМЯ | ОПИСАНИЕ
-------|-----   
**результаты** | Массив из 0 или более сущностей, которые соответствуют выражению запроса. Каждая сущность содержит значения запрашиваемых атрибутов. Это поле присутствует в том случае, если запрос был успешно обработан.
**error** | Коды состояния HTTP. Это поле присутствует в том случае, если запрос завершается ошибкой.
**message** | Сообщение об ошибке. Это поле присутствует в том случае, если запрос завершается ошибкой.

Если запрос не может быть обработан за _800 мс_, будет возвращена ошибка превышения _времени ожидания_. 

<br>
#### <a name="example"></a>Пример:

##### <a name="json-search"></a>Поиск JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Для поиска *json*, если мы хотим получить газеты, заголовки статей которых содержат строку "Graph Engine" и написаны самим Bin Shao (Бин Шао), можно указать запрос следующим образом.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Выходные данные запроса представляют собой массив путей графа. Путь графа является массивом объектов узлов, соответствующих узлам, указанным в пути запроса. Эти объекты узла имеют по крайней мере одно свойство *CellID*, которое представляет собой идентификатор сущности. Другие свойства можно получить, указав имена свойств с помощью выбора оператора [*объекта перекрестного действия*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Лямбда-поиск 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
При *лямбда*-поиске, если мы хотим получить идентификаторы автора данной статьи, можно написать запрос, подобный следующему.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Выходные данные поискового *лямбда*-запроса также представляют собой массив путей графа.

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Схема графа

Схема графа полезна для написания запросов поиска по графу. Это показано на следующем рисунке.

![Схема Microsoft Academic Graph](./Images/AcademicGraphSchema.png)
