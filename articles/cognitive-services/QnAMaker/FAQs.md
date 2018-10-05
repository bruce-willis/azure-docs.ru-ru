---
title: Часто задаваемые вопросы — QnA Maker
titleSuffix: Azure Cognitive Services
description: Список часто задаваемых вопросов для службы QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: efefd595c43d7f46ff1ead91577d070cf8fb90e4
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164622"
---
# <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Почему для указанных мной URL-адресов или файлов не извлекаются пары "вопрос — ответ"?

В некоторых случаях QnA Maker не может автоматически извлечь содержимое в формате "вопрос — ответ" по допустимым URL-адресам. В таких случаях поместите нужное содержимое в TXT-файл и проверьте, сможет ли средство извлечь содержимое в таком формате. Кроме того, вы можете использовать интерфейс редактора для добавления содержимого в базу знаний.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Базу данных какого размера можно создать?

Допустимый размер базы знаний зависит от номера SKU, который вы выбрали для Поиска Azure при создании службы QnA Maker. Дополнительные сведения см. [здесь](./Tutorials/choosing-capacity-qnamaker-deployment.md).

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Почему ничего не отображается в раскрывающемся списке, когда я пытаюсь создать базу знаний?

Скорее всего, вы еще не создали службу QnA Maker в Azure. [Здесь](./How-To/set-up-qnamaker-service-azure.md) вы можете узнать, как это сделать.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Как поделиться базой знаний с другими пользователями?

Совместное использование работает на уровне службы QnA Maker, т. е. все базы знаний в службах предоставляются в общий доступ. [Здесь](./How-To/collaborate-knowledge-base.md) подробно описана совместная работа над базой знаний.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Как изменить сообщение, которое отображается по умолчанию при отсутствии подходящего совпадения?

Это сообщение по умолчанию настраивается в службе приложений.
- Перейдите к ресурсу службы приложений на портале Azure.

![Служба приложений QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Щелкните элемент **Параметры**.

![Параметры службы приложений QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Измените значение параметра **DefaultAnswer**.
- Перезапустите службу приложений.

![Перезапуск службы приложений QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Почему ничего не извлекается по ссылке на SharePoint?

Это средство анализирует только общедоступные URL-адреса и сейчас не поддерживает источники данных с аутентификацией. Но вы всегда можете скачать нужный файл и отправить его в службу для извлечения вопросов и ответов.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Обновления, выполненные в базе знаний, не отразились в публикации. Почему так происходит?

Любую операцию изменения (обновление таблиц, тесты или настройки) нужно сохранить, чтобы она была опубликована. Не забывайте нажать кнопку "Save and train" (Сохранить и обучить) после любого изменения данных.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Когда мне нужно обновить ключи конечной точки?

Обновляйте ключи конечной точки каждый раз, когда есть основания подозревать их компрометацию.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Поддерживает ли база знаний форматированные данные или мультимедиа?

База знаний поддерживает Markdown. Но при автоматическом извлечении из URL-адресов возможность преобразования из HTML в Markdown ограничена. Чтобы использовать полнофункциональный язык Markdown, вы можете изменить содержимое напрямую в таблице или передать базу знаний с форматированным содержимым.

Данные мультимедиа, например изображения и видео, сейчас не поддерживаются.

## <a name="does-qna-maker-support-non-english-languages"></a>Поддерживает ли QnA Maker языки, отличные от английского?

Сведения о поддерживаемых языках вы найдете на [этой странице](./Overview/languages-supported.md).

Если у вас есть содержимое на нескольких языках, обязательно создайте отдельную службу для каждого языка.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Нужна ли платформа Bot Framework для использования QnA Maker?

Нет, вам не нужно использовать Bot Framework с QnA Maker. Но QnA Maker предоставляется в числе шаблонов службы Azure Bot. Служба Azure Bot позволяет быстро разрабатывать интеллектуальные боты на платформе Microsoft Bot Framework, которые выполняются в бессерверной среде.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Как создать бота с помощью QnA Maker?

Следуйте инструкциям в [этом](./Tutorials/create-qna-bot.md) документе, чтобы создать бота в Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Как внедрить службу QnA Maker в веб-сайт?

Чтобы внедрить службу QnA Maker в качестве элемента управления веб-чатом, сделайте следующее:

1. Создайте бота вопросов и ответов, следуя [этим](./Tutorials/create-qna-bot.md) инструкциям.
2. Включите веб-чат, выполнив шаги из [этой статьи](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat).


