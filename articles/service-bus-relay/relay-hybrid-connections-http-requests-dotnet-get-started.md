---
title: Начало работы с HTTP-запросами гибридных подключений Azure Relay в .NET | Документация Майкрософт
description: 'Написание консольного приложения #C для HTTP-запросов гибридных подключений Azure Relay в .NET.'
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: 4f7aa0b0c5142237f3b6fc66bb8593302c95f5af
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41919193"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Начало работы с HTTP-запросами гибридных подключений Azure Relay в .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

В этом руководстве содержатся основные сведения о [гибридных подключениях ретранслятора Azure](relay-what-is-it.md#hybrid-connections). Узнайте, как с помощью Microsoft .NET создать клиентское приложение, которое отправляет запросы к соответствующему приложению, ожидающему передачи данных. 

## <a name="what-will-be-accomplished"></a>Что будет выполнено
Для гибридных подключений требуется как компонент клиента, так и компонент сервера. В этом руководстве необходимо выполнить следующие инструкции, чтобы создать два консольных приложения:

1. Создайте пространство имен ретранслятора с помощью портала Azure.
2. Создайте гибридное подключение в этом пространстве имен с помощью портала Azure.
3. Создайте серверное консольное приложение (прослушиватель) для получения запросов.
4. Создайте клиентское консольное приложение (отправитель) для отправки запросов.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* [Visual Studio 2015 или более поздней версии](http://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2017.
* Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure.
Если пространство имен ретранслятора уже создано, перейдите к разделу [Создание гибридного подключения с помощью портала Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Создание гибридного подключения с помощью портала Azure.
Если гибридное подключение уже создано, перейдите к разделу о [создании серверного приложения](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Создание серверного приложения (прослушивателя)
Создайте в Visual Studio консольное приложение C# для прослушивания и получения сообщений, отправленных ретранслятором.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Создание клиентского приложения (отправителя)
Создайте в Visual Studio консольное приложение C# для отправки сообщений в ретранслятор.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Запуск приложений
1. Запустите серверное приложение.
2. Запустите клиентское приложение и введите любой текст.
3. Убедитесь, что в серверном консольном приложении отображается текст, введенный в клиентском приложении.

Поздравляем, вы создали приложение для гибридных подключений.

## <a name="next-steps"></a>Дополнительная информация

* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Создание пространства имен](relay-create-namespace-portal.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)

