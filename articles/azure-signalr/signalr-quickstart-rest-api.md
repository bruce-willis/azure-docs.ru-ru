---
title: Краткое руководство. REST API службы Azure SignalR | Документация Майкрософт
description: Краткое руководство по использованию REST API службы Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972765"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Краткое руководство. Широковещательные сообщения в режиме реального времени из консольного приложения

Служба Azure SignalR предоставляет [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) для поддержки сценариев связи сервера с клиентом, таких как широковещательная передача. Вы можете выбрать любой язык программирования, который может сделать вызов REST API. Вы можете отправлять сообщения всем подключенным клиентам, конкретному клиенту по имени или группе клиентов.

В этом кратком руководстве вы узнаете, как отправлять сообщения из приложения командной строки в подключенные клиентские приложения на языке C#.

## <a name="prerequisites"></a>Предварительные требования

Это краткое руководство предназначено для macOS, Windows или Linux.
* [Базовый пакет SDK для .NET](https://www.microsoft.com/net/download/core)
* Текстовый редактор или редактор кода по вашему выбору.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу <https://portal.azure.com/> с помощью своей учетной записи Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Пока служба развертывается, давайте перейдем к подготовке кода. Клонируйте [пример приложения из GitHub](https://github.com/aspnet/AzureSignalR-samples.git), задайте строку подключения службы SignalR и запустите приложение в локальной среде.

1. Откройте окно терминала git. Перейдите в папку, в которую вы хотите клонировать пример проекта.

1. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Этот пример представляет собой консольное приложение, показывающее использование службы Azure SignalR. Он предоставляет два режима.

- Режим сервера — используйте простые команды для вызова REST API службы Azure SignalR.
- Режим клиента — подключитесь к службе Azure SignalR и получайте сообщения с сервера.

Также вы можете узнать, как создать маркер доступа для проверки подлинности с помощью службы Azure SignalR.

### <a name="build-the-executable-file"></a>Сборка исполняемого файла

В качестве примера мы используем macOS osx.10.13-x64. Можно найти [рекомендации](https://docs.microsoft.com/dotnet/core/rid-catalog) по сборке на других платформах.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Запуск клиента

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Запуск сервера

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Запуск примера без публикации

Можно также выполнить следующую команду, чтобы запустить сервер или клиент

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Использование функции user-secrets для указания строки подключения

Вы можете выполнить `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` в корневом каталоге примера. После этого вам больше не нужен параметр `-c "<ConnectionString>"`.

## <a name="usage"></a>Использование

После запуска сервера используйте команду для отправки сообщения

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Можно запустить несколько клиентов с разными именами.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
