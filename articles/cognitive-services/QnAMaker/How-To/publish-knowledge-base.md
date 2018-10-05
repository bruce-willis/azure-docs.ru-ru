---
title: Публикация базы знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: Публикация базы знаний — это последний шаг в предоставлении базы знаний в качестве конечной точки ответа на вопрос. При публикации базы знаний содержимое раздела вопросов и ответов базы знаний переносится из тестового указателя в производственный указатель в службе поиска Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033421"
---
# <a name="publish-a-knowledge-base"></a>Публикация базы знаний

Публикация базы знаний — это последний шаг в предоставлении базы знаний в качестве конечной точки ответа на вопрос. 

При публикации базы знаний содержимое раздела вопросов и ответов базы знаний переносится из тестового указателя в производственный указатель в службе поиска Azure.

![Публикация из тестового указателя в производственный](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Публикация базы знаний

1. После завершения изменений в базе знаний выберите команду **Опубликовать** на верхней панели навигации. Вы можете публиковать количество баз знаний для поиска Azure, не превышающее заданного количества. 

    ![Публикация базы знаний](../media/qnamaker-how-to-publish-kb/publish.png)

2. Еще раз выберите **Опубликовать**, чтобы просмотреть сведения о конечной точке, которые могут использоваться в вашем приложении или бот-коде.

    ![Публикация базы знаний](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Анализ базы знаний](./get-analytics-knowledge-base.md)
