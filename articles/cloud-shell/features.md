---
title: Функции Azure Cloud Shell | Документы Майкрософт
description: Обзор функций Bash в Azure Cloud Shell.
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 1321645d97e7f6ff2faed1e61ddb608afcb7b413
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038359"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Функции и средства для Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell выполняется в `Ubuntu 16.04 LTS`.

## <a name="features"></a>Функции

### <a name="secure-automatic-authentication"></a>Безопасная автоматическая аутентификация

Cloud Shell безопасно и автоматически выполняет аутентификацию доступа к учетным записям для Azure CLI 2.0 и Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Сохранение каталога $Home между сеансами

Чтобы сохранять файлы между сеансами, при первом запуске Cloud Shell предлагается присоединить общую папку Azure.
По завершении настройки Cloud Shell будет автоматически подключать хранилище (подключенное как `$Home\clouddrive`) для всех будущих сеансов.
Кроме того, ваш каталог `$Home` сохраняется в виде IMG-файла в файловом ресурсе Azure.
Файлы вне `$Home` и состояние компьютера не сохраняются между сеансами. Следуйте рекомендациям при хранении секретов, например ключей SSH. Для таких служб, как [Azure Key Vault, предусмотрены руководства по настройке](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Дополнительные сведения о сохранении файлов в Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Диск Azure (Azure:)

PowerShell в Cloud Shell (предварительная версия) запускается на диске Azure (`Azure:`).
Диск Azure упрощает обнаружение ресурсов Azure и перемещение по ним, включая вычислительные ресурсы, сетевые ресурсы, ресурсы хранилища и т. д., предоставляя возможности навигации как у файловой системы.
Для управления этими ресурсами можно воспользоваться привычными [командлетами Azure PowerShell](https://docs.microsoft.com/powershell/azure), независимо от используемого диска.
Любые изменения, внесенные в ресурсы Azure непосредственно на портале Azure или с помощью командлетов Azure PowerShell, отражаются на диске Azure.  Для обновления ресурсов можно запустить `dir -Force`.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Глубокая интеграция со средствами с открытым кодом

В Cloud Shell предварительно настроена аутентификация для таких средств с открытым кодом, как Terraform, Ansible и Chef InSpec. Попробуйте поработать с ней при помощи пошаговых руководств с примерами.

## <a name="tools"></a>Средства

|Категория   |ИМЯ   |
|---|---|
|Средства Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Инструменты Azure            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) и [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Интерфейс командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|Текстовые редакторы           |vim<br> nano<br> emacs       |
|Система управления версиями         |git                    |
|Инструменты сборки            |make<br> maven<br> npm<br> pip         |
|Контейнеры             |[Docker CLI](https://github.com/docker/cli)/[Компьютер Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [Интерфейс командной строки DC/OS](https://github.com/dcos/dcos-cli)         |
|Базы данных              |Клиент MySQL<br> Клиент PostgreSQL<br> [Служебная программа sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Другие                  |Клиент iPython<br> [Интерфейс командной строки Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Поддержка языков

|Язык   |Version (версия)   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.1.0 (предварительная версия 4)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 и 3.5 (по умолчанию)|

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по Bash в Cloud Shell](quickstart.md) <br>
[Краткое руководство по использованию PowerShell в Cloud Shell (предварительная версия)](quickstart-powershell.md) <br>
[Справочник команд Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Дополнительные сведения об Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
