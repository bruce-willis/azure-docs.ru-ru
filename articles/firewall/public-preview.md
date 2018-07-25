---
title: Включение общедоступной предварительной версии службы "Брандмауэр Azure"
description: Для включения общедоступной предварительной версии службы "Брандмауэр Azure" используется Azure PowerShell
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992383"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Включение общедоступной предварительной версии службы "Брандмауэр Azure"

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Включить при помощи Azure PowerShell

Чтобы включить службу "Брандмауэр Azure" в общедоступной предварительной версии, выполните следующие команды Azure PowerShell:

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Регистрация функции занимает до 30 минут. Чтобы проверить состояние регистрации, выполните следующие команды Azure PowerShell:

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
После регистрации выполните следующую команду:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Дополнительная информация

- [Руководство по развертыванию и настройке службы "Брандмауэр Azure" с помощью портала Azure](tutorial-firewall-deploy-portal.md)

