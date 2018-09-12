---
title: Пример сценария Azure CLI. Сопоставление личного домена с приложением-функцией | Документация Майкрософт
description: Пример сценария Azure CLI для сопоставления личного домена с приложением-функцией в Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: c5ae5ef52d60fd96a9398835b109475c08549f52
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157250"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Сопоставление личного домена с приложением-функцией

Этот пример скрипта создает приложение-функцию в плане службы приложений, а затем сопоставляет его с указанным личным доменом. При размещении приложения-функции в [плане служб приложений](../functions-scale.md#app-service-plan) личный домен можно сопоставить с помощью CNAME или записи A. Для приложений-функций в [плане потребления](../functions-scale.md#consumption-plan) поддерживается только сопоставление с помощью CNAME. Этот пример создает план службы приложений, при этом для сопоставления домена требуется запись A. 

Чтобы запустить этот пример скрипта, у вас должна быть настроенная запись A в личном домене, указывающая на доменное имя веб приложения по умолчанию. См. дополнительные сведения о [сопоставлении инструкций личного домена для Службы приложений Azure](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, это должен быть Azure CLI версии 2.0 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Создает план службы приложений, необходимый для сопоставления личного домена. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию в плане службы приложений. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Сопоставляет личный домен с приложением-функцией. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций см. в [документации по Функциям Azure](../functions-cli-samples.md).
