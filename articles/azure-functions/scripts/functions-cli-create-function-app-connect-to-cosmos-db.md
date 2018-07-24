---
title: Создание функции Azure, которая подключается к Azure Cosmos DB | Документация Майкрософт
description: Пример скрипта Azure CLI для создания функции Azure, которая подключается к Azure Cosmos DB.
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: functions
ms.assetid: ''
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9154cef897d38e617c2f9dccdc8a47fe1af72104
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989467"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Создание функции Azure, которая подключается к Azure Cosmos DB

При помощи этого примера скрипта, написанного с использованием решения "Функции Azure", создается приложение-функция. Затем функция подключается к базе данных Azure Cosmos DB. Параметр созданного приложения с данными подключения можно использовать с [триггером службы Azure Cosmos DB или привязкой к ней](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы используете интерфейс командной строки локально, убедитесь, что у вас установлена версия не ниже Azure CLI 2.0. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложение-функцию Azure, а также добавляет конечную точку и ключ доступа Cosmos DB в параметры приложения.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Создайте группу ресурсов с расположением. |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Создание учетной записи хранения |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию в бессерверном [плане потребления](../functions-scale.md#consumption-plan). |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Создает базу данных Azure Cosmos DB. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).




