---
title: Формат данных в API службы поиска и распознавания данных | Документация Майкрософт
description: Сведения о формате данных в API службы поиска и распознавания данных (KES) в Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380076"
---
# <a name="data-format"></a>Формат данных
В файле данных приводится список объектов для индексирования.
Каждая строка в файле указывает значения атрибутов объекта в [формате JSON](http://json.org/) и кодировке UTF-8.
В дополнение к атрибутам, определенным в [схеме](SchemaFormat.md), каждый объект имеет необязательный атрибут logprob, который указывает относительную логарифмическую вероятность среди объектов.
Когда служба возвращает объекты в порядке убывания вероятности, с помощью атрибута logprob можно указать порядок возвращения соответствующих объектов.
Если задать вероятность *p* от 0 до 1, то соответствующую логарифмическую вероятность можно вычислить как log(*p*), где log() — функция натурального логарифма.
Если значение logprob не указано, используется значение по умолчанию, 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Для атрибутов String, GUID и Blob значение представлено в виде заключенной в кавычки строки JSON.  Для числовых атрибутов (Int32, Int64, Double) значение представлено в виде числа JSON.  Значение составных атрибутов — это объект JSON, который определяет значения вложенных атрибутов.  Чтобы ускорить создание индекса, предварительно отсортируйте объекты в порядке убывания логарифмической вероятности.

Как правило, атрибут может иметь 0 или более значений.  Если атрибут не имеет значения, мы просто удалим его из JSON.  Если атрибут содержит 2 или более значений, мы можем повторить пару значений атрибута в объекте JSON.  Кроме того, можно назначить массив JSON, содержащий несколько значений атрибута.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
