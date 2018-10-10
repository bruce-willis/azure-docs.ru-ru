---
title: Создание в Azure приложения-функции, которое развертывается из Azure DevOps | Документация Майкрософт
description: Создание приложения-функции и развертывание кода функции из Azure DevOps.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: azure-functions
ms.custom: mvc
ms.openlocfilehash: fdc2f50be035c3fe4039c48c09436215d2f5bcc3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956614"
---
# <a name="create-a-function-app-and-deploy-function-code-from-azure-devops"></a>Создание приложения-функции и развертывание кода функции из Azure DevOps

В этом разделе показано, как при помощи решения "Функции Azure" создать [бессерверное](https://azure.microsoft.com/overview/serverless-computing/) приложение-функцию с использованием [плана потребления](../functions-scale.md#consumption-plan). Приложение-функция, которое представляет собой контейнер для функций, непрерывно развертывается из репозитория Azure DevOps. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Для работы с этой статьей требуется:

* репозиторий Azure DevOps с проектом приложения-функции и разрешением администратора на доступ к такому репозиторию;
* [личный маркер доступа](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) для доступа к репозиторию Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили использовать Azure CLI локально, установите версию 2.0 или более позднюю. Чтобы определить версию Azure CLI, выполните `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложение-функцию Azure и развертывает код функции из Azure DevOps.

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
