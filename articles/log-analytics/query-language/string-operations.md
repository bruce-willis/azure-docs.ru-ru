---
title: Работа со строками в запросах Azure Log Analytics | Документация Майкрософт
description: В этой статье содержатся сведения об использовании портала аналитики для записи запросов в Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: de1ba8b8560e65586ac59f9a04165a93492f3e05
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190985"
---
# <a name="working-with-strings-in-log-analytics-queries"></a>Работа со строками в запросах Log Analytics


> [!NOTE]
> Прежде чем приступать к этому руководству, необходимо ознакомиться со статьей [Начало работы с порталом аналитики](get-started-analytics-portal.md) и [Начало работы с запросами](get-started-queries.md).

В этой статье описывается, как редактировать, сравнивать, выполнять поиск и многие другие операции со строками. 

Каждый символ в строке имеет номер индекса в соответствии с его расположением. Первый символ имеет индекс 0, следующий — 1 и т. д. Различные строковые функции используют номера индексов по-разному, как показано в следующих разделах. Во многих приведенных примерах используется команда **print** для демонстрации работы со строками без использования определенного источника данных.


## <a name="strings-and-escaping-them"></a>Строки и их экранирование
Строковые значения заключаются в одинарные или двойные кавычки. Обратная косая черта (\\) используется для экранирования идущих за ней символов, например \t для табуляции, \n для новой строки и для символа кавычки \".

```OQL
print "this is a 'string' literal in double \" quotes"
```

Чтобы "\\" не действовала как escape-символ, добавьте \"\@\" как префикс в строке:

```OQL
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Сравнение строк

operator       |ОПИСАНИЕ                         |С учетом регистра|Пример (при `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Равно                              |Yes           |`"aBc" == "aBc"`
`!=`           |Не равно                          |Yes           |`"abc" != "ABC"`
`=~`           |Равно                              |Нет             |`"abc" =~ "ABC"`
`!~`           |Не равно                          |Нет             |`"aBc" !~ "xyz"`
`has`          |Правая часть представляет собой все слово в левой части |Нет |`"North America" has "america"`
`!has`         |Правая часть не является всем словом в левой части       |Нет             |`"North America" !has "amer"` 
`has_cs`       |Правая часть представляет собой все слово в левой части |Yes|`"North America" has_cs "America"`
`!has_cs`      |Правая часть не является всем словом в левой части       |Yes            |`"North America" !has_cs "amer"` 
`hasprefix`    |Правая часть представляет собой префикс слова в левой части         |Нет             |`"North America" hasprefix "ame"`
`!hasprefix`   |Правая часть не является префиксом слова в левой части     |Нет             |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Правая часть представляет собой префикс слова в левой части         |Yes            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Правая часть не является префиксом слова в левой части     |Yes            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Правая часть представляет собой суффикс слова в левой части         |Нет             |`"North America" hassuffix "ica"`
`!hassuffix`   |Правая часть не является суффиксом слова в левой части     |Нет             |"North America" !hassuffix "americ"
`hassuffix_cs`    |Правая часть представляет собой суффикс слова в левой части         |Yes            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Правая часть не является суффиксом слова в левой части     |Yes            |"North America" !hassuffix_cs "icA"
`contains`     |Правая часть является вхождением в левую часть  |Нет             |`"FabriKam" contains "BRik"`
`!contains`    |Правая часть не входит в левую часть           |Нет             |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Правая часть является вхождением в левую часть  |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Правая часть не входит в левую часть           |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Правая часть является начальным вхождением в левую часть|Нет             |`"Fabrikam" startswith "fab"`
`!startswith`  |Правая часть не является начальным вхождением в левую часть|Нет         |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Правая часть является начальным вхождением в левую часть|Yes            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Правая часть не является начальным вхождением в левую часть|Yes        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Правая часть является конечным вхождением в левую часть|Нет              |`"Fabrikam" endswith "Kam"`
`!endswith`    |Правая часть не является конечным вхождением в левую часть|Нет          |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Правая часть является конечным вхождением в левую часть|Yes             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Правая часть не является конечным вхождением в левую часть|Yes         |`"Fabrikam" !endswith "brik"`
`matches regex`|Левая часть содержит соответствие для правой части        |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`           |Соответствует одному из элементов       |Yes           |`"abc" in ("123", "345", "abc")`
`!in`          |Не соответствует одному из элементов   |Yes           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Подсчитывает вхождения подстроки в строку. Можно сопоставить текстовые строки или использовать регулярное выражение. Совпадения для текстовых строк могут перекрывать друг друга, для регулярных выражений — не могут.

### <a name="syntax"></a>Синтаксис
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Аргументы:
- `text` — строка входных данных. 
- `search` — текстовая строка или регулярное выражение, которое проверяется на вхождение в text.
- `kind` - _normal_ | _regex_ (по умолчанию: normal).

### <a name="returns"></a>Результаты

Количество совпадений для строки поиска в контейнере. Совпадения для текстовых строк могут перекрывать друг друга, при этом для регулярных выражений — не могут.

### <a name="examples"></a>Примеры

#### <a name="plain-string-matches"></a>Совпадения текстовой строки

```OQL
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Совпадения регулярного выражения

```OQL
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

Получает соответствие регулярного выражения из указанной строки. При необходимости извлеченная подстрока также преобразуется в указанный тип.

### <a name="syntax"></a>Синтаксис

```OQL
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Аргументы

- `regex` — регулярное выражение.
- `captureGroup` — положительная целочисленная константа, указывающая извлекаемую группу записи. 0 для полного совпадения, 1 — значение, соответствующее первой "("круглой скобке")" в регулярном выражении, 2 или более — последующим круглым скобкам.
- `text` — строка для поиска.
- `typeLiteral` — необязательный литерал типа (например, typeof(long)). Если указан, то извлеченная подстрока преобразуется в этот тип.

### <a name="returns"></a>Результаты
Возвращается подстрока, которая сравнивалась с указанной в группе записи captureGroup, при необходимости преобразованная в тип typeLiteral.
Если соответствия нет или не удается выполнить преобразование типа, возвращается NULL.

### <a name="examples"></a>Примеры

Следующий пример извлекает последний октет *ComputerIP* из записи пакета пульса:
```OQL
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Следующий пример извлекает последний октет, приводит его к типу (числа) *real* и вычисляет следующее значение IP-адреса:
```OQL
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Ниже в примере строки *Trace* производится поиск определения "Duration". Соответствие приводится к типу *real* и умножается на константу времени (1s), *которая приводит Duration к типу timespan*.
```OQL
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* возвращает значение true, если аргумент является пустой строкой или NULL (см. также *isnull*).
- *isnotempty* возвращает значение true, если аргумент не является пустой строкой или NULL (см. также *isnotnull*), Псевдоним: *notempty*.

### <a name="syntax"></a>Синтаксис

```
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Примеры

```OQL
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Разделяет URL-адрес на части (протокол, узел, порт и т. д.) и возвращает объект словаря, содержащий элементы в виде строк.

### <a name="syntax"></a>Синтаксис

```
parseurl(urlstring)
```

### <a name="examples"></a>Примеры

```OQL
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Результат будет таким:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

Заменяет все найденные совпадения регулярного выражения другой строкой. 

### <a name="syntax"></a>Синтаксис

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Аргументы

- `regex` — регулярное выражение для сравнения. Оно может содержать группы захвата в '('круглых скобках')'.
- `rewrite` — регулярное выражение, результат которого заменит все соответствия, найденные сопоставлениями регулярного выражения. Используйте \0 для указания полного соответствия, \1 для первой группы записи, \2 и так далее — для следующих групп записи.
- `input_text` — строка входных данных для поиска.

### <a name="returns"></a>Результаты
Текст после замены всех вхождений регулярного выражения результатами вычислений rewrite. Совпадения не перекрываются.

### <a name="examples"></a>Примеры

```OQL
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Можно получить следующие результаты:
Действие                                        |заменена
------------------------------------------------|----------------------------------------------------------
4663 — была предпринята попытка доступа к объекту.  |Идентификатор действия 4663 — была предпринята попытка доступа к объекту.


## <a name="split"></a>split

Разделяет указанную строку в соответствии с указанным разделителем и возвращает массив результирующих подстрок.

### <a name="syntax"></a>Синтаксис
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Аргументы:

- `source` — строка для разделения в соответствии с указанным разделителем.
- `delimiter` — разделитель, который будет использоваться для разделения исходной строки.
- `requestedIndex` — необязательный индекс, начинающийся с нуля. Если указан, то возвращаемый массив строк будет содержать только этот элемент (если существует).


### <a name="examples"></a>Примеры

```OQL
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Сцепляет строковые аргументы (поддерживает от 1 до 16 аргументов).

### <a name="syntax"></a>Синтаксис
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Примеры
```OQL
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Возвращает длину строки.

### <a name="syntax"></a>Синтаксис
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Примеры
```OQL
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Извлекает подстроку из заданной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

### <a name="syntax"></a>Синтаксис
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Аргументы:

- `source` — исходная строка, из которой будет извлечена подстрока.
- `startingIndex` — отсчитываемая от нуля позиция первого знака запрашиваемой подстроки.
- `length` — необязательный параметр, который указывает требуемую длину возвращаемой подстроки.

### <a name="examples"></a>Примеры
```OQL
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Преобразует все буквы заданной строки в нижний или верхний регистр.

### <a name="syntax"></a>Синтаксис
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Примеры
```OQL
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с дополнительными руководствами по следующим ссылкам:
* [Статистические функции в запросах Log Analytics](aggregations.md)
* [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
* [Создание графиков и диаграмм](charts.md)
* [Работа c JSON и структурами данных](json-data-structures.md)
* [Составление расширенных запросов](advanced-query-writing.md)
* [Соединения в анализе данных из нескольких источников](joins.md)