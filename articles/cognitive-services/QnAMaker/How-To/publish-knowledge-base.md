---
title: Как опубликовать базу знаний с использованием Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Как опубликовать базу знаний
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381728"
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
