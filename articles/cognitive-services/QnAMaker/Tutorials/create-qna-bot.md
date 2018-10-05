---
title: Создание бота QnA с помощью службы Azure Bot — QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 74c7bc5c601cd36a8dd2454506745406bc00dac0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031294"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>Создание бота QnA с помощью службы Azure Bot версии 3
В этом руководстве приводятся пошаговые инструкции по созданию бота QnA с помощью службы Azure Bot версии 3 на портале Azure.

## <a name="prerequisite"></a>Предварительные требования
Перед началом работы выполните инструкции в разделе [Создание базы знаний](../How-To/create-knowledge-base.md), чтобы создать службу QnA Maker с вопросами и ответами.

Бот отвечает на вопросы из созданной базы знаний с помощью QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Создание бота QnA
1. На [портале Azure](https://portal.azure.com) в колонке меню выберите **Создать ресурс**, а затем выберите **Показать все**.

    ![создание службы бота](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. В поле поиска введите **Бот веб-приложения**.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. В колонке **службы Bot** введите необходимые сведения.

    - В поле **Имя приложения** укажите имя бота. При развертывании бота в облаке имя используется в качестве поддомена (например, mynotesbot.azurewebsites.net).
    - Укажите подписку, группу ресурсов, план службы приложений и расположение.

4. Инструкции по созданию бота QnA с помощью пакета SDK версии 4 см. в статье [Использование QnA Maker для ответов на вопросы](https://aka.ms/qna-bot-v4). Чтобы использовать шаблоны версии 3, выберите для пакета SDK версию **SDK v3** (SDK версии 3) и язык **C#** или **Node.js**.

    ![Параметры пакета SDK для бота](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Выберите в поле шаблона бота **Question and Answer** (Вопрос и ответ), а затем сохраните параметры шаблона, нажав кнопку **Выбрать**.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Просмотрите параметры, а затем нажмите кнопку **Создать**. В Azure будет создана и развернута служба бота с QnAMakerDialog.

    ![выбор службы бота](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Убедитесь, что служба бота развернута.

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
