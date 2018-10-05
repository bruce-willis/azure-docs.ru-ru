---
title: Базы знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: cb3426a960a6644b3ae149f02055cdb083febca7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040767"
---
# <a name="knowledge-base"></a>База знаний

База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.

## <a name="key-knowledge-base-concepts"></a>Ключевые понятия базы знаний

* **Вопросы**. Вопрос содержит текст, который наилучшим образом представляет пользовательский запрос. 
* **Ответы**. Ответ, который возвращается, когда пользовательский запрос сопоставляется с соответствующим вопросом.  
* **Метаданные**. Метаданные — это теги, которые связаны с парой QnA и отображаются в виде пар "ключ — значение". Метаданные используются для фильтрации пар QnA и ограничения набора, в котором выполняется сопоставление запросов.

Единственный QnA, представленный числовым идентификатором QnA, имеет несколько вариантов вопроса (альтернативные вопросы), которые сопоставляются с одним ответом. Кроме того, каждая такая пара может иметь несколько полей метаданных, связанных с ней.

![Базы знаний QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Формат содержимого базы знаний

При приеме содержимого в базу знаний QnA Maker пытается преобразовать контент в markdown. Подробнее о форматах markdown, понятных большинству чат-клиентов, см. в [этом](https://aka.ms/qnamaker-docs-markdown-support) блоге.

Поля метаданных состоят из пар "ключ — значение", разделенных двоеточием **(Продукт:Уничтожитель)**. Ключ и значение должны быть только текстом. Ключ метаданных не должен содержать пробелов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также

[Общие сведения о QnA Maker](../Overview/overview.md)