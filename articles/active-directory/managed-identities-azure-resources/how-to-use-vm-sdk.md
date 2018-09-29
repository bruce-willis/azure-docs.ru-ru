---
title: Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure с пакетами SDK для Azure
description: Примеры кода для использования пакетов SDK для Azure с виртуальной машиной Azure, для которой включены управляемые удостоверения для ресурсов Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 6827b01e72cd72d3f017df36205ccb985b4f242e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106510"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure с пакетами SDK для Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
В этой статье приведен список примеров для пакета SDK, в которых демонстрируется использование поддержки пакета SDK Azure для управляемых удостоверений для ресурсов Azure.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Во всех примерах кода и сценариев в этой статье предполагается, что клиент выполняется на виртуальной машине с включенными управляемыми удостоверениями для ресурсов Azure. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении управляемых удостоверений для ресурсов Azure на виртуальной машине см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-portal-windows-vm.md) или в одном из вариантов статьи (с использованием PowerShell, интерфейса командной строки, шаблона или пакета SDK для Azure). 

## <a name="sdk-code-samples"></a>Примеры кода SDK

| SDK             | Пример кода |
| --------------- | ----------- |
| .NET            | [Развертывание шаблона Azure Resource Manager с виртуальной машины Windows с помощью управляемых удостоверений для ресурсов Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Вызов служб Azure с виртуальной машины Linux с помощью управляемых удостоверений для ресурсов Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Управление ресурсами с помощью управляемых удостоверений для ресурсов Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Использование управляемых удостоверений для ресурсов Azure для аутентификации из виртуальной машины](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Управление ресурсами из виртуальной машины, на которой включены управляемые удостоверения для ресурсов Azure](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Дополнительная информация

- В разделе [Пакеты SDK](https://azure.microsoft.com/downloads/) приведен полный список ресурсов для пакета Azure SDK, включая файлы для скачивания библиотеки, документацию и многое другое.
- Чтобы включить управляемые удостоверения для ресурсов Azure на виртуальной машине Azure, ознакомьтесь с разделом [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине с помощью портала Azure](qs-configure-portal-windows-vm.md).








