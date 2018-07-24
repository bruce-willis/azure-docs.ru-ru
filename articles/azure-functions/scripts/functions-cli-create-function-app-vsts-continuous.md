---
title: Создание в Azure приложения-функции, которое развертывается из Visual Studio Team Services | Документация Майкрософт
description: Создание приложения-функции и развертывание кода функции из Visual Studio Team Services.
services: functions
keywords: ''
author: syntaxc4
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 1b54cfebd3ae36fc8025aeb4ea9c91d336bc5343
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988958"
---
# <a name="create-a-function-app-and-deploy-function-code-from-visual-studio-team-services"></a>Создание приложения-функции и развертывание кода функции из Visual Studio Team Services

В этом разделе показано, как при помощи решения "Функции Azure" создать [бессерверное](https://azure.microsoft.com/overview/serverless-computing/) приложение-функцию с использованием [плана потребления](../functions-scale.md#consumption-plan). Приложение-функция, которая представляет собой контейнер для функций, непрерывно развертывается из репозитория Visual Studio Team Services (VSTS). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Для работы с этой статьей требуется:

* репозиторий VSTS с проектом приложения-функции и права администратора на доступ к такому репозиторию;
* [личный маркер доступа](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) для доступа к репозиторию VSTS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили использовать Azure CLI локально, установите версию 2.0 или более позднюю. Чтобы определить версию Azure CLI, выполните `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложение-функцию Azure и развертывает код функции из Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, учетной записи хранения, приложения-функции и всех связанных ресурсов в этом скрипте используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию в бессерверном [плане потребления](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Связывает приложение-функцию с репозиторием Git или Mercurial. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
