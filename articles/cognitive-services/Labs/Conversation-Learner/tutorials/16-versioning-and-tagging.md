---
title: Использование управления версиями и расстановки тегов в модели Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Узнайте, как использовать управление версиями и расстановку тегов в приложении Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170551"
---
# <a name="how-to-use-versioning-and-tagging"></a>Как использовать управление версиями и теги

В этом руководстве показано, как помечать тегами версии модели Conversation Learner и указывать, какая из версий является активной.  

## <a name="requirements"></a>Требования
Для этого руководства требуется эмулятор бота для создания записанных диалогов, а не пользовательский веб-интерфейс записи диалогов.  

Для работы с этим руководством требуется запущенный бот обучения общего назначения.

    npm run tutorial-general

## <a name="details"></a>Сведения

При редактировании вы всегда изменяете тег "master": вы можете создавать помеченные тегами версии на основе версии "master" (по сути это моментальные снимки версии "master"), но не можете изменять версии с тегом.

## <a name="steps"></a>Действия

### <a name="install-the-bot-framework-emulator"></a>Установка эмулятора Bot Framework

- Перейдите к [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Скачайте и установите эмулятор.

### <a name="create-an-model"></a>Создание модели

1. Щелкните "Новая модель"
2. В поле "Name" (Имя) введите "Tutorial-16-Versioning".
3. Нажмите кнопку "Создать" 
4. Щелкните "Параметры"
5. Скопируйте идентификатор модели

### <a name="configure-the-emulator"></a>Настройка эмулятора

- Откройте ENV-файл в корневой папке Conversation Learner.
- Вставьте идентификатор модели в качестве значения CONVERSATION_LEARNER_MODEL_ID.
- Перезапустите службу Conversation Learner, выйдя из командной строки и повторно выполнив приведенную ниже команду.
 
    npm run tutorial-general 

### <a name="actions"></a>Действия

Давайте создадим действие.

1. Подключитесь на пользовательский веб-интерфейс.
1. Щелкните "Actions" (Действия) и "New Action" (Новое действие).
2. В поле "Response" (Ответ) введите "hi there (version 1)".
3. Щелкните Сохранить.


![](../media/tutorial16_action1.PNG)

Создайте тег.

3. Щелкните "settings" (Параметры) и создайте тег.
    - Назовите его "version 1".
4. Задайте состояние "live" для "version 1".  
    - Это приведет к тому, что каналы, использующие этот бот, будут использовать тег "version 1".
    - На помеченные тегами версии моделей не влияют изменения (изменение действий, сущностей, добавление диалогов для обучения).  
    - Изменения, внесенные в модель (изменение действий, сущностей, добавление диалогов для обучения), всегда реализуются в версии с тегом "master".  Другими словами, версия "master" — единственная, которую можно изменить. Другие теги относятся к фиксированным моментальным снимкам.
    - Записанные диалоги в пользовательском интерфейсе Conversation Learner всегда используют тег "master" (а не тег "live").

![](../media/tutorial16_v1_create.PNG)

Версия, которая была создана в параметрах.

![](../media/tutorial16_settings.PNG)

Давайте добавим второе действие.

1. Щелкните "Actions" (Действия) и "New Action" (Новое действие).
2. В поле "Response" (Ответ) введите "bye bye (version 2)".

Измените первое действие.

1. Щелкните "Actions" (Действия).
2. В разделе "Actions" (Действия) щелкните "bye bye (version 1)".
3. Измените ответ на "hi there (version 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Перейдите в эмулятор бота.

1. В пользовательском интерфейсе бота введите "goodbye".
2. Бот ответит: "hi there (версия 1)".
    - Это значит, что версия 1 находится в состоянии "live". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Переключитесь на пользовательский веб-интерфейс.

1. Щелкните Log Dialogs (Записанные диалоги). Если какие-либо диалоги отсутствуют, нажмите кнопку "Обновить".
2. Щелкните "bye bye (version 2)".

> [!NOTE]
> Исправление можно внести путем выбора правильного действия из всех доступных. Эти изменения будут внесены в версию "master".

Теперь вы знаете, как работает управление версиями и как можно взаимодействовать с ботом с помощью эмулятора Bot Framework.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Демонстрация: сброс пароля](./demo-password-reset.md)
