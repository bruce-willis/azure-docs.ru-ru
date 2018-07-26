---
title: Скачивание средств Azure Stack из GitHub | Документация Майкрософт
description: Сведения о скачивании средств, необходимых для работы с Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187457"
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

AzureStack-Tools — это репозиторий GitHub, в котором размещены модули PowerShell. Их можно использовать для администрирования и развертывания ресурсов в Azure Stack.

## <a name="download-targets"></a>Скачивание целевых компонентов

Вы можете скачать и использовать эти модули PowerShell с Пакетом средств разработки Azure Stack или внешним клиентом на основе Windows, который использует VPN-подключение.

## <a name="how-to-get-the-tools"></a>Как получить инструменты

Чтобы получить эти инструменты, клонируйте репозиторий GitHub AzureStack-Tools или скачайте папку AzureStack-Tools, выполнив следующий сценарий.

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Возможности, предоставляемые модулями

Репозиторий AzureStack-Tools содержит модули PowerShell с поддержкой следующих функций для работы в среде Azure Stack:

| Функции | ОПИСАНИЕ | Кто может использовать этот модуль? |
| --- | --- | --- |
| [Возможности облачных служб](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Этот модуль используется для получения возможностей облачных служб. Например, с помощью этого модуля можно получить возможности использования версий API, ресурсов Azure Resource Manager, расширений виртуальных машин и т. д. для Azure Stack и облаков Azure. | Администраторы и пользователи облака. |
| [Политика Resource Manager для Azure Stack](azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с такой же версией и уровнем доступности службы, как и в Azure Stack. | Администраторы и пользователи облака |
| [Подключение к Azure Stack](azure-stack-connect-azure-stack.md) | Этот модуль используется для подключения к экземпляру Azure Stack с помощью PowerShell, а также для настройки VPN-подключения к Azure Stack. | Администраторы и пользователи облака |
| [Проверяющий элемент управления шаблона](azure-stack-validate-templates.md) | Этот модуль позволяет проверить, можно ли развернуть существующий или новый шаблон в Azure Stack. | Администраторы и пользователи облака |

## <a name="next-steps"></a>Дополнительная информация

- [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)
- [Подключение к Пакету средств разработки Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)
