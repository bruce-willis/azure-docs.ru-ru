---
title: Настройка CLI для сетки Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке CLI для Сетки Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5e8eaca7f759842a8097184dafc1f3ea183b898c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993193"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric
CLI для Сетки Service Fabric требуется для развертывания и управления ресурсами в Сетке Service Fabric. 

В режиме предварительной версии CLI для Сетки Azure Service Fabric записывается как расширение для Azure CLI. Вы можете установить его в Azure Cloud Shell или локально в Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Установка интерфейса командной строки Сетки Azure Service Fabric локально
Если вы решили установить и использовать CLI локально, используйте Azure CLI версии 2.0.43 и выше. Чтобы узнать версию, выполните команду `az --version`. Если необходимо установить или обновить CLI, ознакомьтесь с [этим разделом][azure-cli-install].

Установите модуль расширения CLI для Сетки Azure Service Fabric. 

```azurecli-interactive
az extension add --name mesh
```

Чтобы обновить существующий модуль службы "Сетка Azure Service Fabric", выполните следующую команду.

```azurecli-interactive
az extension update --name mesh
```

Вы также можете настроить [среду разработки Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli
