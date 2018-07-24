---
title: Настройка CLI для сетки Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке CLI для Сетки Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f56bcf0c844545e8883175da2bd3f22afdcd19ea
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089533"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric
CLI для Сетки Service Fabric требуется для развертывания и управления ресурсами в Сетке Service Fabric. 

В режиме предварительной версии CLI для Сетки Azure Service Fabric записывается как расширение для Azure CLI. Вы можете установить его в Azure Cloud Shell или локально в Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать CLI локально, используйте Azure CLI версии 2.0.35 и выше. Чтобы узнать версию, выполните команду `az --version`. Чтобы установить или обновить CLI, см. руководство по [установке Azure CLI 2.0][azure-cli-install].

Удалите предыдущие установки модуля CLI для Сетки Azure Service Fabric.

```azurecli-interactive
az extension remove --name mesh
```

Установите модуль расширения CLI для Сетки Azure Service Fabric. 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

Вы также можете настроить [среду разработки Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli