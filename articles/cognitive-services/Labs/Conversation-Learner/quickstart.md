---
title: Как создать приложение Conversation Learner в Microsoft Cognitive Services с помощью Node.js | Документация Майкрософт
titleSuffix: Azure
description: Узнайте, как создать приложение Conversation Learner с помощью Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382481"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Создание приложения Conversation Learner с помощью Node.js

Conversation Learner упрощает создание ботов. Он обеспечивает рабочий процесс гибридной разработки, позволяющий с помощью ручного программирования и машинного обучения сократить объем кода, необходимого для написания ботов. Некоторые фиксированные части приложения, например проверку входа пользователя или отправку запроса API для проверки запасов на складе, по-прежнему нужно программировать. Тем не менее, другие изменения выбора состояний и действий могут быть изучены на примере диалогов, предоставленных экспертом в предметной области или разработчиком.

## <a name="invitation-required"></a>Требуется приглашение

*Для доступа к Project Conversation Learner требуется приглашение.*

Project Conversation Learner содержит пакет SDK, добавляемый в ваш бот, и облачную службу, к которой обращается пакет SDK для машинного обучения.  В настоящее для доступа к облачной службе Project Conversation Learner требуется приглашение.  Если вы еще не приглашены, [запросите приглашение](https://aka.ms/conversation-learner-request-invite).  Если вы не получили приглашение, то вы не сможете получить доступ к API облака.

## <a name="prerequisites"></a>предварительным требованиям

- Node 8.5.0 или более поздней версии и npm 5.3.0 или более поздней версии. Установить эти компоненты можно отсюда: [https://nodejs.org](https://nodejs.org).
  
- Ключ разработки LUIS.

  1. Войдите на сайт [http://www.luis.ai](http://www.luis.ai).

  2. Щелкните свое имя в правом верхнем углу, затем щелкните "settings" (Параметры).

  3. Ключ разработки отобразится на открывшейся странице.

  (Ваш ключ разработки LUIS выполняет 2 роли.  Во-первых, он будет выполнять роль ключа разработки Conversation Learner.  Во-вторых, Conversation Learner использует LUIS для извлечения сущностей. Ключ разработки LUIS используется для создания моделей LUIS от вашего имени.)

- Веб-браузер Google Chrome. Установить его можно отсюда: [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- Git. Установить его можно отсюда: [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Установить его можно отсюда: [https://code.visualstudio.com/](https://code.visualstudio.com/). Это рекомендуется, но не является обязательным.

## <a name="quick-start"></a>Быстрый запуск 

1. Установка и выполнение сборки.

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Во время `npm install` эту ошибку можно игнорировать: `gyp ERR! stack Error: Can't find Python executable`

2. Настройка.

   В каталоге `cl-bot-01` создайте файл `.env`.  Он должен содержать следующий код.

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Запуск бота.

    ```
    npm start
    ```

    Будет запущен универсальный пустой бот в `cl-bot-01/src/app.ts`.

3. Запустите пользовательский интерфейс Conversation Learner.

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Откройте в браузере страницу http://localhost:5050. 

Вы запустили Conversation Learner, и теперь вы можете создать и обучить модель Conversation Learner.  

> [!NOTE]
> При запуске доступ к Project Conversation Learner осуществляется по приглашению.  Если http://localhost:5050 показывает ошибку HTTP `403`, это означает, ваша учетная запись не была приглашена.  [Запросите приглашение](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Руководства, демонстрационные версии и переключение между ботами

Приведенных выше инструкциях описан запуск пустого универсального бота.  Чтобы вместо него запустить учебный или демонстрационный бот, сделайте следующее.

1. Если у вас открыт пользовательский веб-интерфейс Conversation Learner, вернитесь к списку приложений по адресу http://localhost:5050/home.
    
2. Если запущен другой бот (например, `npm start` или `npm run demo-pizza`), остановите его.  Нет необходимости останавливать процесс пользовательского интерфейса или закрывать веб-браузер.

3. Запустите демонстрационный бот из командной строки (см. шаг 2 выше).  Демонстрационные версии содержат следующее.

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Если вы этого еще не сделали, переключитесь на пользовательский веб-интерфейс Conversation Learner в Chrome, загрузив http://localhost:5050/home. 

5. Щелкните "Import tutorials" (Импортировать руководства) (достаточно выполнить один раз).  Этот процесс займет около минуты. Модели Conversation Learner для всех руководств будут скопированы в вашу учетную запись Conversation Learner.

6. В пользовательском интерфейсе Conversation Learner щелкните демонстрационную модель, соответствующую запущенному примеру.

Исходные файлы для демонстрационных версий находятся в `cl-bot-01/src/demos`.

## <a name="create-a-bot-which-includes-back-end-code"></a>Создание бота с кодом серверной части

1. Если у вас открыт пользовательский веб-интерфейс Conversation Learner, вернитесь к списку приложений по адресу http://localhost:5050/home.
    
2. Если запущен бот (например, `npm run demo-pizza`), остановите его.  Нет необходимости останавливать процесс пользовательского интерфейса или закрывать веб-браузер.

3. При необходимости измените код в `cl-bot-01/src/app.ts`.

4. Повторно выполните сборку и запустите бот.

    ```bash    
    npm run build
    npm start
    ```

5. Если вы этого еще не сделали, переключитесь на пользовательский веб-интерфейс Conversation Learner в Chrome, загрузив http://localhost:5050/home. 

6. Создайте новое приложение Conversation Learner в пользовательском интерфейсе и начните обучение.

7. Чтобы внести изменения в код в `cl-bot-01/src/app.ts`, повторите предыдущие шаги, начиная с шага 2.

## <a name="vscode"></a>VSCode

В VSCode для каждой демонстрационной версии имеются конфигурации запуска, как и для пустого бота в `cl-bot-01/src/app.ts`.  Откройте папку `cl-bot-01` в VSCode.

## <a name="advanced-configuration"></a>Расширенная конфигурация

Доступен файл шаблона `.env.example`, в котором показано, какие переменные среды можно задать для настройки примеров.

Вы можете настроить эти порты, чтобы избежать конфликтов между другими службами, выполняющихся на компьютере, добавив файл с расширением `.env` в корневой каталог проекта.

```bash
cp .env.example .env
```

Используется стандартная конфигурация, что позволяет запустить бот локально и приступить к работе с Conversation Learner.  (Позже, чтобы развернуть бот на платформе Bot Framework, потребуется внести в этот файл некоторые изменения.)

## <a name="support"></a>Поддержка

- Добавляйте к вопросам на форуме [Stack Overflow](https://stackoverflow.com) тег "microsoft cognitive".
- Запросить функцию можно на нашей [странице User Voice](https://aka.ms/conversation-learner-uservoice).
- Отправьте проблему в наш [репозиторий GitHub](https://github.com/Microsoft/ConversationLearner-Samples).

## <a name="contributing"></a>Участие

Этот проект соответствует [правилам поведения Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. на странице [Вопросы и ответы о правилах поведения](https://opensource.microsoft.com/codeofconduct/faq/) или отправьте вопросы и комментарии по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="source-repositories"></a>Исходные репозитории

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Hello world](./tutorials/1-hello-world.md)
