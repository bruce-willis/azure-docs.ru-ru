---
title: Создание бота QnA с помощью службы Azure Bot — Azure Cognitive Services | Документы Майкрософт
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: fc430bf3aa7cad279d7a93bb6892aa19abee3378
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109275"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Создание бота QnA с помощью службы Azure Bot
В этом учебнике приводится пошаговое руководство по созданию бота QnA с помощью службы Azure Bot на портале Azure.

## <a name="prerequisite"></a>Предварительные требования
Перед началом работы выполните инструкции в разделе [Создание базы знаний](../How-To/create-knowledge-base.md), чтобы создать службу QnA Maker с вопросами и ответами.

Бот отвечает на вопросы из созданной базы знаний с помощью QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Создание бота QnA
1. На [портале Azure](https://portal.azure.com) в колонке меню выберите **Создать ресурс**, а затем выберите **Показать все**.

    ![создание службы бота](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. В поле поиска введите **Бот веб-приложения**.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. В колонке **Служба программ-роботов** введите необходимые сведения и нажмите кнопку **Создать**. В Azure будет создана и развернута служба бота с QnAMakerDialog.

    - В поле **Имя приложения** укажите имя бота. При развертывании бота в облаке имя используется в качестве поддомена (например, mynotesbot.azurewebsites.net).
    - Укажите подписку, группу ресурсов, план службы приложений и расположение.
    - Выберите шаблон **Вопрос и ответ** (Node.js или C#) в поля "Шаблон бота".
    - Установите флажок подтверждения для юридического уведомления. Условия юридического уведомления приводятся под флажком.

        ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Убедитесь, что служба бота развернута.

    - Щелкните **Уведомления** (значок колокольчика, расположенный в верхней части портала Azure). Уведомление изменится с **Развертывание начато** на **Развертывание прошло успешно**.
    - После того как уведомление изменится на **Развертывание прошло успешно**, в этом уведомлении выберите **Go to resource** (Перейти к ресурсу).

## <a name="chat-with-the-bot"></a>Чат с ботом
При выборе **Перейти к ресурсу** выполняется переход к колонке ресурсов бота.

После регистрации бота выберите пункт **Test in Web Chat** (Тестировать в веб-чате), чтобы открыть панель веб-чата. В веб-чате введите "hello" (привет).

![веб-чат бота QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Бот выдаст ответ: "Задайте QnAKnowledgebaseId и QnASubscriptionKey в параметрах приложения. Узнайте, как их получить, здесь: https://aka.ms/qnaabssetup". Этот ответ подтверждает, что бот QnA получил сообщение, однако с ним еще не связана база знаний QnA Maker. Мы сделаем это на следующем шаге.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Подключение базы знаний QnA Maker к боту

1. Откройте **параметры приложения** и задайте в полях **QnAKnowledgebaseId**, **QnAAuthKey** и **QnAEndpointHostName** значения для вашей базы знаний QnA Maker.

    ![параметры приложения](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Получите идентификатор базы знаний, URL-адрес узла и ключ конечной точки на вкладке параметров базы знаний в https://qnamaker.ai.
    - Войдите в [QnA Maker](https://qnamaker.ai).
    - Перейдите к базе знаний.
    - Перейдите на вкладку **Параметры**.
    - **Опубликуйте** базу знаний, если она еще не опубликована.

    ![Значения QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Если вы хотите подключить предварительную версию базы знаний к боту QnA, установите для параметра **Ocp-Apim-Subscription-Key** значение **QnAAuthKey**. Оставьте поле **QnAEndpointHostName** пустым.

## <a name="test-the-bot"></a>Тестирование бота
На портале Azure щелкните **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. 

![Бот QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Теперь ваш бот QnA будет получать ответы из базы знаний.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Интеграция QnA Maker и LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>См. также

- [Управление базой знаний](https://qnamaker.ai)
- [Включение бота в разных каналах](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
