---
title: Синтаксис поиска JSON в Academic Knowledge API | Документация Майкрософт
description: Дополнительные сведения о синтаксисе поиска JSON, который можно использовать в Academic Knowledge API в Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380001"
---
# <a name="json-search-syntax"></a>Синтаксис поиска JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Имена узлов в пути запроса (_v0, v1, ..._) выступают в качестве идентификаторов узлов,на которые можно ссылаться в объекте запроса; имена граничных узлов (_e0,e1, ..._) в пути представляют типы соответствующих граничных узлов. Можно использовать символ звездочки _*_ для имени узла или границы (за исключением начального узла, который должен быть задан изначально), чтобы объявить, что для такого элемента нет ограничений. Например, путь запроса `/v0/*/v1/e1/*/` извлекает пути из графа без ограничения типа граничного узла _(v0, v1)_. В то же время запрос не имеет ограничений на целевом пути (последний узел).

Если путь содержит только один узел, например _v0_, запрос просто вернет все объекты, которые соответствуют ограничениям. Ограничение объектов,которое применяется к исходному узлу, называется *Начальный объект запроса* , спецификация которого задается следующим образом.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Если путь содержит больше, чем просто начальный узел, обработчик запросов выполняет обход графа по указанному шаблону пути. При его поступлении в узел действия обхода, определяемые пользователем, будут активированы, то есть останавливаться ли на текущем узле и возвращаться или продолжать исследовать граф. Если действия обхода не указаны, будут выполнены действия по умолчанию. Для промежуточного узла действие по умолчанию — продолжить изучение графа. Для последнего узла пути действие по умолчанию — остановить и вернуться. Объект ограничения, который определяет действия обхода, называется *Объект действий обхода*. Его спецификация предоставляется следующим образом:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Тело POST поискового *json*-запроса должно содержать по крайней мере шаблон *пути*. Объекты действий обхода необязательные. Ниже приведены два примера.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

