---
title: Пример сценария Azure CLI. Привязка настраиваемого SSL-сертификата к приложению-функции | Документация Майкрософт
description: Пример сценария Azure CLI для привязки настраиваемого SSL-сертификата к приложению-функции в Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9b6779ac7778b721ff566c8553433853dbadbf13
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988267"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Привязка пользовательского SSL-сертификата к приложению-функции

Этот пример сценария создает в службе приложений приложение-функции со связанными ресурсами, а затем привязывает к нему SSL-сертификат имени личного домена. Для этого примера вам потребуются следующие компоненты:

* Доступ к странице конфигурации DNS вашего регистратора доменных имен.
* Допустимый PFX-файл и пароль для SSL-сертификата, который будет отправлен и привязан.
* Настроенная запись A в личном домене, указывающая на доменное имя веб приложения по умолчанию. См. дополнительные сведения о [сопоставлении инструкций личного домена для Службы приложений Azure](https://aka.ms/appservicecustomdns).

Чтобы привязать SSL-сертификат, приложение-функция должно быть создано в плане службы приложений, а не в плане потребления.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, это должен быть Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Создает план службы приложений, необходимый для привязки SSL-сертификатов. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Позволяет создать приложение-функцию в плане службы приложений. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Сопоставляет личный домен с приложением-функцией. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Отправляет SSL-сертификат в приложение-функцию. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Привязывает отправленный SSL-сертификат к приложению-функции. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../functions-cli-samples.md).
