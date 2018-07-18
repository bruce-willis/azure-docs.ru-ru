---
title: Создание базы знаний на языке, отличном от английского, с помощью QnA Maker и Azure Cognitive Services | Документация Майкрософт
description: Создание базы знаний на языке, отличном от английского.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381792"
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
