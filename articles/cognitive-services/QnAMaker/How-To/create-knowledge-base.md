---
title: Как создать базу знаний с помощью QnA Maker и Azure Cognitive Services | Документация Майкрософт
description: Как создать базу знаний
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35383158"
---
# <a name="create-a-knowledge-base"></a>Создание базы знаний

QnA Maker сильно упрощает адаптацию существующих источников данных для создания базы знаний. Можно создать базу знаний студии QnA из страниц часто задаваемых вопросов, руководств по продуктам или структурированных документов. Кроме того, базу знаний может создать редактор.

## <a name="steps"></a>Действия

1. Чтобы приступить к работе, войдите на [портал QnA Maker](https://qnamaker.ai) с помощью учетных данных Azure и щелкните **Create new service** (Создать службу).

    ![Создание базы знаний ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Если служба QnA Maker еще не создана, выберите **Create a QnA service** (Создать службу QnA). В противном случае выберите службу QnA Maker из раскрывающихся списков на шаге 2. Выберите службу QnA Maker, в которой будет размещаться база знаний.

    ![Установка службы QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Введите следующие сведения для создания базы знаний.

    ![Настройка источников данных](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Введите **имя** для службы. Поддерживаются повторяющиеся имена, а также специальные знаки в имени.
    - Вставьте URL-адреса для извлечения данных. Дополнительные сведения о поддерживаемых типах источников приведены [здесь](../Concepts/data-sources-supported.md).
    - Можно также передать файлы, из которых будут извлечены данные. Ознакомьтесь со [сведениями о ценах](https://aka.ms/qnamaker-pricing
), чтобы узнать, сколько документов можно добавить.
    - Если вы хотите вручную добавить вопросы и ответы, можно пропустить указание ссылок на файлы.

4. Нажмите кнопку **Создать**.

    ![Создание базы знаний](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Извлечение данных может занять несколько минут.

    ![Извлечение](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Если база знаний успешно создана, вы будете перенаправлены на страницу **Knowledge base** (База знаний).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Импорт базы знаний](../Tutorials/migrate-knowledge-base.md)
