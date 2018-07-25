---
title: Масштабирование служб в приложении службы "Сетка Azure Service Fabric" | Документация Майкрософт
description: Узнайте, как независимо масштабировать службы в приложении, работающем в службе "Сетка Service Fabric", с помощью Azure CLI.
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089749"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Масштабирование служб в приложении, работающем в службе "Сетка Service Fabric"

В этой статье показано, как независимо масштабировать микрослужбы в приложении. В этом примере приложение Visual Objects состоит из двух микрослужб, `web` и `worker`. 

Служба `web` — это приложение ASP.NET Core с веб-страницей, которая отображает в браузере треугольники. В браузере отображается один треугольник для каждого экземпляра службы `worker`. 

Служба `worker` перемещает треугольник в пространстве с предопределенным интервалом и отправляет расположение треугольника в службу `web`. Она использует DNS для разрешения адреса службы `web`.

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

## <a name="deploy-the-application-with-one-worker-service"></a>Развертывание приложения с помощью одной рабочей службы
Создайте приложение в группе ресурсов с помощью команды `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Предыдущая команда развертывает приложение Linux, используя [шаблон mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Если необходимо развернуть приложение Windows, используйте [шаблон mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Образы контейнера для ОС Windows больше, чем образы контейнера для Linux, поэтому может потребоваться больше времени для развертывания.

Через несколько минут команда должна вернуть следующий результат.

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Запуск приложения
После успешного развертывания приложения получите общедоступный IP-адрес конечной точки службы и откройте его в браузере. Он позволяет перейти на веб-страницу с одним треугольником, который перемещается в пространстве.

Команда развертывания вернет общедоступный IP-адрес конечной точки службы. При необходимости вы можете запросить сетевой ресурс, чтобы найти общедоступный IP-адрес конечной точки службы. 
 
Имя сетевого ресурса для этого приложения — `visualObjectsNetwork`. Получить сведения о нем можно с помощью следующей команды. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Масштабирование службы `worker`

Масштабируйте службу `worker` до трех экземпляров, используя следующую команду. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Предыдущая команда развертывает приложение Linux, используя [шаблон mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Если необходимо развернуть приложение Windows, используйте [шаблон mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Образы контейнера для ОС Windows больше, чем образы контейнера для Linux, поэтому может потребоваться больше времени для развертывания.

После успешного развертывания приложения браузер должен отобразить веб-страницу с тремя треугольниками, перемещающимися в пространстве.

## <a name="delete-the-resources"></a>Удаление ресурсов

В целях экономии ограниченных ресурсов, выделенных для предварительной версии программы, регулярно удаляйте ресурсы. Чтобы удалить ресурсы, относящиеся к этому примеру, удалите группу ресурсов, в которой они были развернуты.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с примером приложения Visual Objects на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)