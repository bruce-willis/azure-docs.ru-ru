---
title: Совместная работа с базой знаний. Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Руководство по совместной работе с базой знаний QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381725"
---
# <a name="collaborate-on-your-knowledge-base"></a>Совместная работа с базой знаний

QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. Эта возможность предоставляется в рамках [управления доступом на основе ролей](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) в Azure. 

Чтобы предоставить другому пользователю доступ к службе QnA Maker, сделайте следующее.

1. Войдите на портал Azure и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Выберите роль **Владелец** или **Участник**.

    ![Добавление роли IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Введите адрес электронной почты пользователя, которому следует предоставить доступ, и нажмите кнопку "Сохранить".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Теперь, когда пользователь, которому предоставлен доступ к службе QnA Maker, выполнит вход на [портал QnA Maker](https://qnamaker.ai), он сможет просматривать все базы знаний в этой службе.

Помните, что доступ к конкретной базе знаний в службе QnA Maker предоставить невозможно. Если требуется более детальное управление доступом, рекомендуем распределить базы знаний по разным службам QnA Maker.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Тестирование базы знаний](./test-knowledge-base.md)
