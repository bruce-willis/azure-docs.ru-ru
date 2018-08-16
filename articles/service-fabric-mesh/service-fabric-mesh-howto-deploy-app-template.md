---
title: Развертывание приложения в службе "Сетка Azure Service Fabric" с помощью шаблона | Документация Майкрософт
description: Узнайте, как развернуть приложение .NET Core в службе "Сетка Service Fabric" на основе шаблона с помощью Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038559"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Развертывание приложения службы "Сетка Service Fabric" в службе "Сетка Service Fabric" с помощью шаблона
В этой статье показано, как развернуть приложение .NET Core в службе "Сетка Service Fabric" с помощью шаблона. После завершения этого руководства вы получите приложение для голосования с клиентской частью в виде веб-приложения ASP.NET Core, которое сохраняет результаты голосования во внутренней службе в кластере. Внешний интерфейс использует DNS для разрешения адреса внутренней службы.

## <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric 
Для выполнения этой задачи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Установите модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив следующие [инструкции](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите в Azure и настройте подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Создать группу ресурсов
Создайте группу ресурсов, в которую будет развертываться приложение. Можно использовать существующую группу ресурсов и пропустить этот шаг. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Развертывание приложения
Создайте приложение в группе ресурсов с помощью команды `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Предыдущая команда развертывает приложение Windows, используя [шаблон mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Если необходимо развернуть приложение Linux, используйте [шаблон mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Образы контейнера для ОС Windows больше, чем образы контейнера для Linux, поэтому может потребоваться больше времени для развертывания.

Через несколько минут команда должна вернуть следующий результат.

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Запуск приложения
После успешного развертывания приложения получите общедоступный IP-адрес конечной точки службы и откройте его в браузере. Откроется следующая веб-страница. 

![Приложение для голосования](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Теперь можно добавить в приложение возможности голосования и проголосовать или удалить возможности голосования.

Команда развертывания вернет общедоступный IP-адрес конечной точки службы. При необходимости вы можете запросить сетевой ресурс, чтобы найти общедоступный IP-адрес конечной точки службы. 

Имя сетевого ресурса для этого приложения — `VotingAppNetwork`. Получить сведения о нем можно с помощью следующей команды. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Проверка сведений о приложении
Состояние приложения можно проверить с помощью команды `app show`. Имя развернутого приложения — VotingApp, поэтому получите сведения с помощью этого имени. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Вывод списка развернутых приложений
Чтобы получить список приложений, которые развернуты в подписке, можно использовать команду app list. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда приложение и связанные с ним ресурсами перестанут быть нужны, удалите содержащую их группу ресурсов. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с примером приложения для голосования на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)


