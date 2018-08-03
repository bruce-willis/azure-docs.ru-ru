---
title: Как развернуть бот Conversation Learner — Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Сведения о развертывании бота Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb977df92cf0ada1e50a929a9ea714313a70165a
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171482"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Развертывание бота Conversation Learner

В этом документе описывается развертывание бота Conversation Learner, которое вы можете выполнить локально или в Azure.

## <a name="prerequisite-determine-the-model-id"></a>Предварительное требование: определите идентификатор модели 

Чтобы запустить бот вне пользовательского интерфейса Conversation Learner, необходимо настроить идентификатор для модели Conversation Learner, то есть идентификатор модели машинного обучения в облаке Conversation Learner.  (В тех случаях, когда бот работает в пользовательском интерфейсе Conversation Learner, идентификатор модели ему назначается автоматически).  

Чтобы получить идентификатор модели, выполните следующие действия.

1. Запустите бот и пользовательский интерфейс Conversation Learner.  Ниже кратко описана процедура, полную версию которой вы найдете в кратком руководстве по началу работы.

    В одном командном окне выполните следующее команды:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    В другом командном окне выполните такие команды:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Откройте в браузере страницу http://localhost:5050. 

3. Щелкните модель Conversation Learner, для которой требуется идентификатор.

4. В панели навигации слева щелкните "Параметры".

5. Откроется страница, в верхней части которой указан GUID "Идентификатор модели".

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Вариант 1. Локальное развертывание бота Conversation Learner

Эта процедура развертывает бот на локальном компьютере и демонстрирует доступ к нему через Bot Framework Channel Emulator.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Настройка доступа к боту за пределами пользовательского интерфейса Conversation Learner

Чтобы запустить бот локально, добавьте в файл `.env` для этого бота идентификатор приложения:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

После этого запустите бот:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Теперь он работает на локальном компьютере.  Вы можете обратиться к нему через эмулятор Bot Framework.

### <a name="download-and-install-the-emulator"></a>Скачивание и установка эмулятора

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Подключение эмулятора к боту

1. В верхнем левом углу эмулятора введите значение `http://127.0.0.1:3978/api/messages` в поле URL-адреса конечной точки.  Остальные поля оставьте пустыми и щелкните "Connect" (Подключиться).

2. Откроется окно диалога с ботом.

## <a name="option-2-deploy-to-azure"></a>Вариант 2. Развертывание в Azure

Публикация бота Conversation Learner выполняется почти так же, как и для любого другого бота. Если коротко, то вам нужно лишь передать код на размещенный веб-сайт, задать необходимые значения конфигурации и зарегистрировать бот в нужных каналах. В этом видео представлены подробные инструкции по публикации бота с помощью службы Azure Bot.

Когда бот будет развернут и запущен, вы можете выполнить регистрацию канала Azure Bot и подключить к нему разные каналы взаимодействия, например Facebook, Teams, Skype и т. п. Документация по этому процессу представлена по адресу: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration.

Ниже приведены пошаговые инструкции по развертыванию бота Conversation Learner в Azure.  Эти инструкции предполагают, что исходный код вашего бота размещен в облачном источнике, таком как VSTS, GitHub, BitBucket или OneDrive, и что бот будет настроен для непрерывного развертывания.

1. Войдите на портал Azure на странице https://portal.azure.com.

2. Создайте ресурс "Бот веб-приложения". 

    1. Присвойте новому боту имя.
    2. Щелкните "Bot Template" (Шаблон бота), выберите Node.js, затем "Basic" (Базовый) и нажмите кнопку "Select" (Выбрать).
    3. Щелкните "Create" (Создать), чтобы создать бот веб-приложения.
    4. Дождитесь, пока завершится создание ресурса для бота веб-приложения.

3. На портале Azure измените только что созданный ресурс бота веб-приложения.

    1. Щелкните пункт "Параметры приложения" в меню навигации слева.
    1. Прокрутите вниз до раздела "Параметры приложения".
    2. Добавьте в него следующие параметры.

        Переменная среды | value
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_MODEL_ID      | Идентификатор приложения (GUID), полученный из пользовательского интерфейса Conversation Learner в разделе параметров модели.
        LUIS_AUTHORING_KEY               | Ключ разработки LUIS для этой модели
    
    4. Щелкните "Сохранить" в верхней части страницы.
    5. Выберите элемент навигации "Сборка" слева.
    6. Щелкните "Configure continuous deployment" (Настройка непрерывного развертывания). 
    7. Щелкните значок "Установка" под списком развертываний.
    8. Щелкните "Required Settings" (Обязательные параметры).
    9. Выберите источник, в котором размещен код вашего бота и настройте параметры этого источника.
