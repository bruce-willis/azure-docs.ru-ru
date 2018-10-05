---
title: База знаний на языке, отличном от английского, — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая созданная база знаний, связанная с определенной службой QnA Maker, задает язык для этой службы.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: b983fb21e8e67a422b6757619d1d0dfe8b6b9dcc
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033912"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Языковая поддержка содержимого базы знаний для QnA Maker
QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая созданная база знаний, связанная с определенной службой QnA Maker, задает язык для этой службы. Список поддерживаемых языков см. [здесь](../Overview/languages-supported.md).

Язык автоматическое распознается из извлекаемого содержимого источников данных. После создания новой службы QnA Maker и новой базы знаний в этой службе можно проверить, правильно ли задан язык.

1. Перейдите на [портал Azure](https://portal.azure.com/).

2. Щелкните **Группы ресурсов** и перейдите к группе ресурсов, в которой развернута служба QnA Maker, и выберите ресурс **Поиск Azure**.

    ![Выбор ресурса "Поиск Azure"](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Выберите индекс **testkb**. Этот индекс поиска Azure всегда создается первым. Он содержит сохраненное содержимое всех баз знаний в этой службе. 

    ![Выбор Test KB (Тестовая база знаний)](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Выберите раздел **Поля**, в котором отображаются сведения о testkb.

    ![Выбор раздела "Поля"](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Установите флажок **Анализатор** для просмотра сведений о языке.

    ![Выбор флажка "Анализатор"](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Вы должны увидеть, что для параметра "Анализатор" выбран определенный язык. Этот язык автоматически определяется при создании базы знаний. После создания ресурса изменить этот язык нельзя.

    ![Выбранный флажок "Анализатор"](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Create a QnA bot with Azure Bot Service](../Tutorials/create-qna-bot.md) (Создание бота QnA с помощью службы Azure Bot)
