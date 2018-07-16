---
title: Синтаксис поиска с лямбда-выражениями в Academic Knowledge API | Документация Майкрософт
description: Дополнительные сведения о синтаксисе поиска лямбда-выражениями, который можно использовать в Academic Knowledge API в Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380077"
---
# <a name="lambda-search-syntax"></a>Синтаксис поиска с лямбда-выражениями

Каждая строка поискового запроса с *лямбда-выражениями* описывает шаблон графа. В запросе должен быть хотя бы один начальный узел, указывающий, с какого узла графа мы начинаем обход. Чтобы задать начальный узел, вызовите метод *MAG. StartFrom()* и передайте его в идентификаторы запроса одного или нескольких узлов объекта, который задает ограничения поиска. Метод *StartFrom()* имеет три перегрузки. Все они принимают два аргумента, второй из которых необязательный. Первый аргумент может быть длинным целым числом, перечислимой коллекцией длинного целого числа или строкой, представляющей объект JSON, с той же семантикой, как и в поиске *json*.
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Процесс написания запроса поиска с лямбда-выражениями представляет собой переход с одного узла на другой. Чтобы указать тип границы для перехода, используйте *FollowEdge()* и передайте необходимые типы границ. *FollowEdge()* принимает произвольное количество строковых аргументов.
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Если не важно, каким типам границ следовать, пропустите *FollowEdge()* между двумя узлами. Запрос пропустит все возможные границы между этими двумя узлами.

Можно указать действия обхода, выполняемые на узле через *VisitNode()*, что означает два варианта: остановить этот узел и вернуть текущий путь или продолжить изучение графа.  Тип перечисления *Action* определяет действия двух типов: *Action.Return* и *Action.Continue*. Можно передать такие значение перечисления непосредственно в *VisitNode()*, или объединить их с помощью побитового оператора and "&". Когда объединяются два действия, это означает, что оба действия будут выполнены. Примечание: не используйте побитовый оператор or "|" для действий. Это приведет к завершению запроса без возвращения. Пропуск *VisitNode()* между двумя вызовами *FollowEdge()* приведет к тому, что запрос безусловно будет исследовать граф после достижения узла.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Для *VisitNode()* можно также передать тип лямбда-выражения *Expression\<Func\<INode, Action\>\>*, которое принимает *INode* и возвращает его обход.

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* предоставляет интерфейсы доступа к данным *только для чтения* и несколько встроенных вспомогательных функций на узле. 

### <a name="basic-data-access-interfaces"></a>Основные интерфейсы доступа к данным

##### <a name="long-cellid"></a>long CellID

64-разрядный идентификатор узла. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(строка fieldName)

Получает значение указанного свойства. *T* — необходимый тип, который должен интерпретироваться как поле. Автоматическое приведение типов будет предпринято, если необходимый тип не может быть неявно преобразован из типа в поле. Примечание. Если свойство многозначно, при использовании *GetField\<string\>* будет выполнена сериализация списка в строке Json ["val1", "val2", ...]. Если свойство не существует, выдается исключение и прерывается текущее исследование графа.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

Указывает, существует ли поле с заданным именем в текущем узле.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

Работает аналогично *GetField\<string\>(fieldName)*. Тем не менее не вызывает исключения, если поле не найдено. При этом возвращается пустое значение string("").

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

Указывает, существует ли данное свойство в текущем узле. То же, что и *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

Указывает, имеет ли свойство заданное значение. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

Получение значения числа данного свойства. Если свойство не существует, возвращает значение 0.

### <a name="built-in-helper-functions"></a>Встроенные вспомогательные функции

##### <a name="action-returnifbool-condition"></a>Действие return_if (условие bool)

Возвращает *Action.Return*, если условие равно *true*. Если условие равно *false* и это выражение не соединено с другими действиями с побитовым оператором И, просмотр графа будет прерван.

##### <a name="action-continueifbool-condition"></a>Действие continue_if (условие bool)

Возвращает *Action.Continue*, если условие равно *true*. Если условие равно *false* и это выражение не соединено с другими действиями с побитовым оператором И, просмотр графа будет прерван.

##### <a name="bool-dicedouble-p"></a>bool dice (double p)

Создает случайное число, равное 0.0 или больше него и меньше 1.0. Эта функция возвращает значение *true*, только если число меньше или равно *p*.

По сравнению с поиском *json*, поиск с *лямбда-выражениями* более выразительный: лямбда-выражения C# могут использоваться для непосредственного указания шаблонов запросов. Ниже приведены два примера.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
