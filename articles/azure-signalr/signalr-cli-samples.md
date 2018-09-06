---
title: Примеры Azure CLI. Служба Azure SignalR | Документация Майкрософт
description: Примеры Azure CLI. Служба Azure SignalR
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664009"
---
# <a name="azure-cli-samples"></a>Примеры Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash для службы Azure SignalR, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание**||
| [Создание службы SignalR и группы ресурсов](scripts/signalr-cli-create-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов.  |
|**Интеграция**||
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR](scripts/signalr-cli-create-with-app-service.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новое веб-приложение и план службы приложений, чтобы разместить веб-приложение ASP.NET Core, которое использует службу SignalR. Веб-приложение настраивается с использованием параметра приложения для подключения к новому ресурсу службы SignalR. |
| [Создание службы SignalR и веб-приложения, настроенного для использования SignalR и OAuth GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Создает ресурс службы Azure SignalR со случайным именем в новой группе ресурсов. Также добавляет новое веб-приложение Azure и соответствующий план для размещения веб-приложения ASP.NET Core, которое использует службу SignalR. Веб-приложение настраивается с использованием параметров для определения строки подключения нового ресурса службы SignalR и секретов клиента для включения поддержки [аутентификации GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), как описано в [руководстве по аутентификации](signalr-authenticate-oauth.md). Также веб-приложение настраивается для использования локального репозитория Git в качестве источника развертывания. |
| | |
