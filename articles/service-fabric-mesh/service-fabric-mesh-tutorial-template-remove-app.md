---
title: Руководство. Удаление приложения, работающего в Сетке Azure Service Fabric
description: Из этого руководства вы узнаете, как удалить приложение, работающее в Сетке Service Fabric, вместе с ресурсами.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: f366413ae5f758601dfebc2a29ff848feb756083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960436"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Руководство. Удаление приложения и ресурсов

Это руководство представляет собой четвертую часть цикла. Вы узнаете, как удалить работающее приложение, которое было [ранее развернуто в Сетке Service Fabric](service-fabric-mesh-tutorial-template-deploy-app.md). 

В четвертой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Удаление приложения, работающего в Сетке Service Fabric
> * Удаление ресурсов приложения

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Развертывание приложения в Сетке Service Fabric с помощью шаблона](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Масштабирование приложения, работающего в Сетке Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Обновление приложения, работающего в Сетке Service Fabric](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Удаление приложения

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет Azure подписки до начала работы, можно [создать бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Откройте [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) или [установите Azure CLI и интерфейс командной строки Сетки Azure Service Fabric на локальный компьютер](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Удаление группы ресурсов и ее содержимого

Удалите все созданные ресурсы, если они больше не нужны. Ранее вы [создали группу ресурсов](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) для размещения экземпляра реестра контейнеров Azure и ресурсов приложения Сетки Service Fabric.  Удалив эту группу ресурсов, вы удалите все связанные с нею ресурсы.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Удаление ресурсов по отдельности
Экземпляр ACR, приложение Сетки Service Fabric и сетевые ресурсы можно удалить по отдельности.

Удаление экземпляра ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Удаление приложения Сетки Service Fabric:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Удаление сети:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * Удаление приложения, работающего в Сетке Service Fabric
> * Удаление ресурсов приложения