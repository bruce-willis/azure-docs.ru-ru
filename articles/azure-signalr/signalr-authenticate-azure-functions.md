---
title: Руководство по аутентификации в службе Azure SignalR с помощью Функций Azure | Документация Майкрософт
description: Изучив это руководство, вы узнаете, как аутентифицировать клиенты службы Azure SignalR
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 8af657c39217f3edcadef6ec0981a31ec7e89aa6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978433"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Руководство по аутентификации в службе Azure SignalR с помощью Функций Azure

Пошаговое руководство по созданию комнаты чата с аутентификацией и обменом частными сообщениями с помощью Функций Azure, проверки подлинности службы приложений и службы SignalR.

## <a name="introduction"></a>Введение

### <a name="technologies-used"></a>Применяемые технологии

* [Функции Azure](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) — API серверной части для аутентификации пользователей и отправки сообщений чата.
* [Служба Azure SignalR](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) — широковещательная передача сообщений чата подключенным клиентам.
* [Служба хранилища Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) — размещение статического веб-сайта, выполняющего роль пользовательского интерфейса клиента чата.

### <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется следующее программное обеспечение:

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (версия 10.x).
* [Пакет SDK для .NET](https://www.microsoft.com/net/download) (версия 2.x, которая требуется для расширений Функций).
* [Основные инструменты службы "Функции Azure"](https://github.com/Azure/azure-functions-core-tools) (версия 2).
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) со следующими расширениями:
    * [Функции Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) — работа с Функциями Azure в VS Code;
    * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) — локальное отображение веб-страниц для тестирования.


## <a name="sign-into-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/) со своими учетными данными.


## <a name="create-an-azure-signalr-service-instance"></a>Создание экземпляра службы Azure SignalR

Вы будете компилировать и тестировать приложение Функций Azure локально. Это приложение будет обращаться к экземпляру службы SignalR в Azure, который следует создать заранее.

1. Нажмите кнопку **Создать ресурс** (**+**), чтобы создать новый ресурс Azure.

1. Выполните поиск по строке **служба SignalR** и выберите эту службу. Нажмите кнопку **Создать**.

    ![Новая служба SignalR](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | Имя ресурса | Уникальное имя для экземпляра службы SignalR |
    | Группа ресурсов | Создание новой группы ресурсов |
    | Расположение | Выберите близкое к вам расположение |
    | Ценовая категория | Free |
    
1. Нажмите кнопку **Создать**.


## <a name="initialize-the-function-app"></a>Инициализация приложения-функции

### <a name="create-a-new-azure-functions-project"></a>Создание проекта Функций Azure

1. В новом окне VS Code выберите в меню пункт `File > Open Folder`, чтобы создать и открыть пустую папку в соответствующем расположении. Это будет главная папка проекта для создаваемого приложения.

1. С помощью расширения "Функции Azure" в VS Code инициализируйте приложение-функцию в главной папке проекта.
    1. Откройте палитру команд в VS Code, последовательно выбрав **Представление > Палитра команд** (или с помощью клавиш `Ctrl-Shift-P`, в macOS — `Cmd-Shift-P`).
    1. Найдите команду **Azure Functions: Create New Project** (Функции Azure: создание нового проекта) и выберите ее.
    1. Должна отобразиться главная папка проекта. Выберите ее (или найдите с помощью кнопки "Обзор").
    1. При появлении запроса на выбор языка выберите **JavaScript**.

    ![Создание приложения-функции](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)


### <a name="install-function-app-extensions"></a>Установка расширений приложения-функции

В этом руководстве для взаимодействия со службой Azure SignalR используются привязки Функций Azure. Как и большинство других привязок, привязки службы SignalR доступны в виде расширения. Его следует установить с помощью интерфейса командной строки основных инструментов службы "Функции Azure", чтобы использовать эти привязки.

1. Откройте окно терминала в VS Code, последовательно выбрав **Вид > Интегрированный терминал** из меню, вызываемого сочетанием клавиш (Ctrl-`).

1. Убедитесь, что текущим каталогом является главная папка проекта.

1. Установите расширение приложения-функции службы SignalR.
    ```
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Настройка параметров приложения

При локальном запуске и отладке среды выполнения Функций Azure параметры приложений считываются из файла **local.settings.json**. Добавьте в этот файл строку подключения к экземпляру службы SignalR, который вы создали ранее.

1. В VS Code найдите файл **local.settings.json** на панели обозревателя и откройте его.

1. Замените содержимое этого файла приведенным ниже кодом.
    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Поместите строку подключения к службе Azure SignalR в параметр с именем `AzureSignalRConnectionString`. Это значение можно получить на странице **Ключи** для ресурса службы Azure SignalR на портале Azure. Можно использовать любую из двух строк подключения — первичную или вторичную.
    * Параметр `WEBSITE_NODE_DEFAULT_VERSION` не используется локально, но он потребуется при развертывании в Azure.
    * Раздел `Host` настраивает порт и параметры CORS для локального узла Функций (этот параметр не действует при работе в Azure).

    ![Получение ключа службы SignalR](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Сохраните файл.

    ![Обновление локальных параметров](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)


## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Создание функции для аутентификации пользователей в службе SignalR

При первом открытии приложения чата в браузере оно запросит допустимые учетные данные для подключения к службе Azure SignalR. Чтобы получить эти сведения о подключении, вы создадите в приложении-функции новую функцию *SignalRInfo*, активируемую HTTP-запросом.

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`).

1. Найдите и выберите команду **Azure Functions: Create Function** (Функции Azure: создание функции).

1. При появлении запроса введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | Папка приложения-функции | Выберите главную папку проекта |
    | Шаблон | Триггер HTTP |
    | ИМЯ | SignalRInfo |
    | Уровень авторизации | Анонимные |
    
    Создается папка с именем **SignalRInfo**, которая содержит новую функцию.

1. Откройте файл **SignalRInfo/function.json**, чтобы настроить привязки для функции. Замените содержимое файла указанным ниже кодом. Этот код добавляет входную привязку, которая создает действительные учетные данные для подключения клиента к центру службы Azure SignalR с именем `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    Свойство `userId` в привязке `signalRConnectionInfo` используется для создания аутентифицированного подключения к службе SignalR. Оставьте это свойство пустым для локальной разработки. Оно понадобится вам только при развертывании приложения-функции в Azure.

1. Откройте **SignalRInfo/index.js**, чтобы просмотреть текст функции. Замените содержимое файла указанным ниже кодом.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Эта функция принимает сведения о подключении SignalR из входной привязки и возвращает их клиенту в тексте ответа HTTP.


## <a name="create-a-function-to-send-chat-messages"></a>Создание функции для отправки сообщений чата

Также этому веб-приложению нужен API-интерфейс HTTP для отправки сообщений чата. Вы создадите активируемую по запросу HTTP функцию с именем *SendMessage*, которая отправляет сообщения всем подключенным клиентам через службу SignalR.

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`).

1. Найдите и выберите команду **Azure Functions: Create Function** (Функции Azure: создание функции).

1. При появлении запроса введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | Папка приложения-функции | Выберите главную папку проекта |
    | Шаблон | Триггер HTTP |
    | ИМЯ | SendMessage |
    | Уровень авторизации | Анонимные |
    
    Будет создана папка с именем **SendMessage**, которая содержит новую функцию.

1. Откройте **SendMessage/function.json**, чтобы настроить привязки для функции. Замените содержимое файла указанным ниже кодом.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Он вносит два изменения в исходную функцию:
    * изменяет значение route (маршрут) на `messages` и ограничивает триггер HTTP только методом **POST**;
    * добавляет выходную привязку службы SignalR, которая отправляет сообщения всем клиентам, подключенным к центру службы SignalR с именем `chat`.

1. Сохраните файл.

1. Откройте файл **SendMessage/index.js**, чтобы просмотреть текст этой функции. Замените содержимое файла указанным ниже кодом.
    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';
            
        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }
    
        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```
    Эта функция принимает текст из HTTP-запроса и отправляет его всем клиентам, подключенным к службе SignalR, посредством вызова функции с именем `newMessage` на каждом клиенте.

    Эта функция может считывать идентификатор отправителя и принимать значение *recipient* (получатель) в тексте сообщения, что позволяет реализовать отправку частных сообщений одному пользователю. Эти функциональные возможности мы применим далее в этом руководстве.

1. Сохраните файл.


## <a name="create-and-run-the-chat-client-web-user-interface"></a>Создание и запуск пользовательского веб-интерфейса для клиентов чата

Пользовательский интерфейс приложения чата представляет собой простое одностраничное приложение, созданное на платформе Vue JavaScript. Оно будет размещено отдельно от приложения-функции. В локальной среде этот веб-интерфейс выполняется с помощью расширения Live Server для VS Code.

1. Создайте в VS Code новую папку с именем **content** в корне главной папки проекта.

1. В папке **content** создайте новый файл с именем **index.html**.

1. Скопируйте и вставьте содержимое файла **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Сохраните файл.

1. Нажмите клавишу **F5**, чтобы локально запустить приложение-функцию и подключить отладчик.

1. При открытом файле **index.html** запустите Live Server через палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`), где нужно выбрать команду **Live Server: Open with Live Server** (Live Server: открыть с помощью Live Server). Live Server откроет приложение в браузере.

1. Приложение открылось. Введите сообщение в окне чата и нажмите клавишу ВВОД. Обновите приложение, чтобы увидеть новые сообщения. Так как аутентификация пока не настроена, все сообщения отправляются от имени пользователя anonymous (аноним).


## <a name="deploy-to-azure-and-enable-authentication"></a>Развертывание в Azure и включение аутентификации

До этого момента вы запускали приложение-функцию и приложение чата локально. Теперь вы развернете их в Azure и включите в приложении аутентификацию и обмен частными сообщениями.


### <a name="log-into-azure-with-vs-code"></a>Вход на портал Azure с помощью VS Code

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`).

1. Найдите и выберите команду **Azure: Sign in** (Azure: вход).

1. Выполните предложенные инструкции по процессу входа в браузере.


### <a name="configure-function-app-for-authentication"></a>Настройка аутентификации для приложения-функции

Сейчас приложение чата работает анонимно. В Azure для аутентификации пользователя вы будете применять [проверку подлинности службы приложений](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview). Идентификатор или имя аутентифицированного пользователя можно передать в привязку *SignalRConnectionInfo*, чтобы сформировать сведения о подключении для аутентификации от имени пользователя.

При отправке сообщения приложение будет определять, нужно ли отправить его всем подключенным клиентам или только тем, которые прошли аутентификацию от имени определенного пользователя.

1. В VS Code откройте файл **SendMessage/function.json**.

1. Вставьте [выражение привязки](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns) в свойство *userId* для привязки *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Так мы сохраним в этом значении имя пользователя, прошедшего аутентификацию. Теперь этот атрибут будет выглядеть следующим образом:

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Сохраните файл.


### <a name="deploy-function-app"></a>Развертывание приложения-функции

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`) и выберите **Azure Functions: Deploy to Function App** (Функции Azure: развертывание приложения-функции). 

1. При появлении запроса введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | Папка для развертывания | Выберите главную папку проекта |
    | Подписка | Выберите свою подписку. |
    | Приложение-функция | Выберите **Create New Function App** (Создание приложения-функции). |
    | Имя приложения-функции | Введите уникальное имя. |
    | Группа ресурсов | Выберите ту же группу ресурсов, что и для экземпляра службы SignalR. |
    | Учетная запись хранения | Выберите **Создать новую учетную запись хранения**. |
    | Имя учетной записи хранения | Введите уникальное имя (от 3 до 24 буквенно-цифровых символов). |
    | Расположение | Выберите близкое к вам расположение. |
    
    В Azure будет создано приложение-функция и начнется развертывание. Дождитесь завершения развертывания.


### <a name="upload-function-app-local-settings"></a>Отправка локальных параметров приложения-функции

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`).

1. Найдите и выберите команду **Azure Functions: Upload local settings** (Функции Azure: отправка локальных параметров).

1. При появлении запроса введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | Файл с локальными параметрами | local.settings.json |
    | Подписка | Выберите свою подписку. |
    | Приложение-функция | Выберите развернутое ранее приложение-функцию. |
    | Имя приложения-функции | Введите уникальное имя. |

Локальные параметры передаются в приложение-функцию в Azure. Если будет предложено перезаписать существующие параметры, выберите **Да для всех**.


### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Предоставление общего доступа к ресурсам независимо от источника (CORS) для приложения-функции

Несмотря на то, что механизм CORS настроен в файле **local.settings.json**, он не распространяется на приложение-функцию в Azure. Необходимо настроить его отдельно.

1. Откройте палитру команд VS Code (`Ctrl-Shift-P`, macOS — `Cmd-Shift-P`).

1. Найдите и выберите команду **Azure Functions: Open in portal** (Функции Azure: открыть на портале).

1. Выберите подписку и имя приложения-функции, которое вы хотите открыть на портале Azure.

1. В открывшемся окне портала перейдите на вкладку **Функции платформы** и выберите **CORS**.

    ![Поиск CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Добавьте запись со значением `*`.

1. Удалите все остальные записи.

1. Щелкните **Сохранить**, чтобы сохранить настройки CORS.

    ![Настройка CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> В реальном приложении не стоит разрешать CORS для всех источников (`*`). Лучше выбрать более безопасный подход, то есть вводить отдельные записи CORS для каждого домена, которому они требуются.


### <a name="update-the-web-app"></a>Обновление веб-приложения

1. На портале Azure перейдите к странице обзорной информации о приложении-функции.

1. Скопируйте URL-адрес приложения-функции.

    ![Получение URL-адреса](media/signalr-authenticate-azure-functions/signalr-get-url.png)


1. Откройте в VS Code файл **index.html** и замените значение `apiBaseUrl` URL-адресом приложения-функции.

1. Можно настроить для приложения аутентификацию с помощью Azure Active Directory, Facebook, Twitter, Google или учетной записи Майкрософт. Выберите поставщик проверки подлинности, который вы хотите использовать, задав значение `authProvider`.

1. Сохраните файл.


### <a name="deploy-the-web-application-to-blob-storage"></a>Развертывание веб-приложения в хранилище BLOB-объектов

Веб-приложение будет размещаться с применением функции статических веб-сайтов хранилища BLOB-объектов Azure.

1. Нажмите кнопку **Создать** (**+**), чтобы создать новый ресурс Azure.

1. В разделе **Параметры** выберите **Учетная запись хранения**.

1. Введите следующие сведения.

    | ИМЯ | Значение |
    |---|---|
    | ИМЯ | Уникальное имя учетной записи хранения BLOB-объектов |
    | Тип учетной записи | StorageV2 (учетная запись общего назначения версии 2) |
    | Расположение | Выберите тот же регион, что и для других ресурсов. |
    | Репликация | Локально избыточное хранилище (LRS) |
    | Производительность | Стандартная |
    | Уровень доступа | Горячий |
    | Группа ресурсов | Выберите ту же группу ресурсов Azure, что и для других служб в этом руководстве. |

1. Нажмите кнопку **Создать**.

    ![Создание хранилища](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Когда завершится создание учетной записи хранения, откройте ее на портале Azure.

1. Выберите вариант **Статический веб-сайт (предварительная версия)** в области навигации слева.

1. Нажмите кнопку **Включить**.

1. В качестве **имени документа индекса** введите `index.html`.

1. Выберите команду **Сохранить**.

    ![Настройка статических сайтов](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Щелкните на этой странице ссылку **$web**, чтобы открыть контейнер больших двоичных объектов.

1. Нажмите кнопку **Отправить** и передайте файл **index.html** в папку **content**.

1. Вернитесь к странице **Статический веб-сайт**. Запишите значение **основной конечной точки**. Это URL-адрес веб-приложения.


### <a name="enable-app-service-authentication"></a>Включение функции проверки подлинности службы приложений

Проверка подлинности службы приложений поддерживает аутентификацию с помощью Azure Active Directory, Facebook, Twitter, Google и учетной записи Майкрософт.

1. Пока приложение-функция еще открыто на портале, найдите вкладку **Функции платформы** и выберите вариант **Аутентификация или авторизация**.

1. **Включите** проверку подлинности службы приложений.

1. В раскрывающемся списке **Предпринимаемое действие, если проверка подлинности для запроса не выполнена** выберите вариант "Log in with (Войти как) {выбранный ранее поставщик проверки подлинности}".

1. В поле **Разрешенные URL-адреса внешнего перенаправления** введите URL-адрес основной конечной точки веб-интерфейса учетной записи хранения, которую вы записали ранее.

1. Выполните инструкции из документации выбранного поставщика входа, чтобы завершить настройку.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)
    - [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)
    - [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication)
    - [Учетная запись Майкрософт](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)
    - [Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)


### <a name="try-the-application"></a>Проверка работы приложения

1. В браузере откройте основную конечную точку веб-интерфейса учетной записи хранения.

1. Выберите **Вход**, чтобы выполнить аутентификацию с помощью выбранного поставщика проверки подлинности.

1. Отправьте групповые сообщения через основное поле чата.

1. Отправьте конфиденциальные сообщения, щелкнув имя пользователя в журнале чата. Эти сообщения получит только выбранный получатель.

Поздравляем! Вы развернули бессерверное приложение чата в реальном времени.

![Демонстрация](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)


## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим руководством, удалите группу ресурсов через портал Azure.


## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как использовать Функции Azure со службой Azure SignalR. Изучите дополнительные сведения о создании бессерверных приложений в реальном времени с привязками службы SignalR для Функций Azure.

> [!div class="nextstepaction"]
> [Создание приложений в реальном времени с помощью решения "Функции Azure"](signalr-overview-azure-functions.md)
