---
title: Краткое руководство. Бессерверная служба Azure SignalR для JavaScript | Документация Майкрософт
description: Краткое руководство по использованию служб "Функции Azure" и SignalR для создания чат-комнаты.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.tgt_pltfrm: Node.js
ms.workload: tbd
ms.date: 09/23/2018
ms.author: zhshang
ms.openlocfilehash: 12ac64a89f27744e1e731633d784135c97c423e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991034"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Краткое руководство. Создание чат-комнаты с помощью служб "Функции Azure" и SignalR с использованием JavaScript

Служба Azure SignalR позволяет легко добавлять в приложение функции реального времени. Функции Azure — бессерверная платформа, которая позволяет выполнять код без необходимости управлять какой-либо инфраструктурой. В этом кратком руководстве вы научитесь использовать службы SignalR и "Функции Azure" для построения бессерверного чат-приложения, работающего в режиме реального времени.


## <a name="prerequisites"></a>Предварительные требования

Это краткое руководство предназначено для macOS, Windows или Linux.

Скачайте и установите редактор кодов [Visual Studio Code](https://code.visualstudio.com/).

Установите [Основные инструменты службы "Функции Azure" (версии 2)](https://github.com/Azure/azure-functions-core-tools#installing) для локального запуска приложения "Функции Azure".

Чтобы установить расширения основных инструментов Функции Azure в настоящее время требуется, чтобы был установлен [пакет SDK для .NET Core](https://www.microsoft.com/net/download). Тем не менее для создания приложения "Функции Azure" для JavaScript не требуются знания платформы .NET.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу <https://portal.azure.com/> с помощью своей учетной записи Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]


## <a name="configure-and-run-the-azure-function-app"></a>Настройка и запуск приложения-функции Azure

1. В браузере, где открыт портал Azure, выполнив поиск имени в верхней части портала, убедитесь, что ранее развернутый экземпляр службы SignalR успешно создан. Выберите экземпляр, чтобы открыть его.

    ![Поиск экземпляра службы SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Выберите **ключи** для просмотра строк подключения экземпляра службы SignalR.

1. Выберите и скопируйте основную строку подключения.

    ![Создание службы SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. В редакторе кода откройте папку *chat/src/javascript* из клонированного репозитория.

1. Переименуйте *local.settings.sample.json* на *local.settings.json*.

1. В файл **local.settings.json** вставьте строку подключения как значение параметра **AzureSignalRConnectionString**. Сохраните файл.

1. Функции JavaScript упорядочиваются в папки. В каждой папке содержатся два файла: *function.json* определяет привязки, используемые в функции, а *index.js* представляет собой основную часть функции. В этом приложении-функции есть две функции триггеров HTTP.

    - **Согласование** использует входную привязку *SignalRConnectionInfo* для генерации и возврата сведений о допустимом соединении.
    - **Сообщения**. Эта функция получает мгновенное сообщение в тексте запроса и использует выходную привязку *SignalR* для рассылки сообщений ко всем подключенным клиентским приложениям.

1. В терминале убедитесь, что вы находитесь в папке *чата/src/javascript*. Используйте основные инструменты "Функции Azure" для установки расширений, необходимых для запуска приложения.

    ```bash
    func extensions install
    ```

1. Запустите приложение-функцию.

    ```bash
    func start
    ```

    ![Создание службы SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)


[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали и запустили бессерверное приложение в режиме реального времени в Visual Studio. Далее узнайте, как разрабатывать и развертывать Функций Azure с помощью Visual Studio.

> [!div class="nextstepaction"]
> [Разработка Функций Azure с помощью Visual Studio](../azure-functions/functions-develop-vs.md)