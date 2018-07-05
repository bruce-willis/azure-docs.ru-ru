---
title: Высокая плотность размещения в службе приложений Azure с использованием независимого масштабирования приложений | Документы Майкрософт
description: Высокая плотность размещения в службе приложений Azure
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36962406"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Высокая плотность размещения в службе приложений Azure с использованием независимого масштабирования приложений
По умолчанию масштабирование приложений службы приложений осуществляется путем увеличения [плана службы приложений](azure-web-sites-web-hosting-plans-in-depth-overview.md), в котором они выполняются. Если в одном плане службы приложений выполняется несколько приложений, в каждом масштабируемом экземпляре выполняются все приложения в плане.

*Независимое масштабирование приложений* можно включить на уровне плана службы приложений. Оно позволяет масштабировать приложение независимо от плана службы приложений, в котором оно размещено. Таким образом, план службы приложений можно увеличить до 10 экземпляров, но приложение максимально может использовать только пять.

> [!NOTE]
> Независимое масштабирование приложений доступно только для ценовых категорий **Стандартный**, **Премиум**, **Премиум V2** и **Изолированный**.
>

## <a name="per-app-scaling-using-powershell"></a>Независимое масштабирование приложений с помощью PowerShell

Чтобы создать план с независимым масштабированием приложений, передайте атрибут ```-perSiteScaling $true``` в командлет ```New-AzureRmAppServicePlan```.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Чтобы обновить существующий план службы приложений с независимым масштабированием приложений: 

- получите целевой план (```Get-AzureRmAppServicePlan```);
- измените свойство локально (```$newASP.PerSiteScaling = $true```);
- опубликуйте изменения в Azure (```Set-AzureRmAppServicePlan```). 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

На уровне приложения настройте несколько экземпляров, которые приложение сможет использовать в плане службы приложений.

В следующем примере для приложения установлено ограничение на два экземпляра независимо от того, сколько экземпляров можно развернуть в пределах базового плана службы приложений.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` отличается от `$newapp.MaxNumberOfWorkers`. Функция "Независимое масштабирование приложений" определяет с параметры масштабирования приложения с помощью свойства `$newapp.SiteConfig.NumberOfWorkers`.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Независимое масштабирование приложений с помощью Azure Resource Manager

Следующий шаблон Azure Resource Manager создает:

- план службы приложений, в пределах которого можно развернуть до 10 экземпляров;
- приложение, для которого настроено масштабирование до пяти экземпляров максимум.

План службы приложений задает для свойства **PerSiteScaling** значение true (`"perSiteScaling": true`). Приложение задает **количество рабочих ролей**, равное 5 (`"properties": { "numberOfWorkers": "5" }`).

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Рекомендуемая конфигурация для высокой плотности размещения
Независимое масштабирование приложений — это функция, которая включается как в глобальных регионах Azure, так и в [средах службы приложений](environment/app-service-app-service-environment-intro.md). Однако рекомендуется использовать среды службы приложений, так как они предоставляют дополнительные возможности и пулы большей емкости.  

Чтобы настроить высокую плотность размещения приложений, выполните следующие действия:

1. Настройте среду службы приложений и выберите рабочий пул, выделенный для сценария высокой плотности размещения.
1. Создайте один план службы приложений и настройте его для использования всей доступной емкости в рабочем пуле.
1. Задайте для флага `PerSiteScaling` плана службы приложений значение "true".
1. Новые приложения будут созданы и назначены этому плану службы приложений, при этом для свойства **numberOfWorkers** будет задано значение **1**. Такая конфигурация позволит достичь максимальной плотности в этом рабочем пуле.
1. Число рабочих ролей можно настроить отдельно для каждого приложения, чтобы предоставить дополнительные ресурсы согласно требованиям. Например: 
    - Для приложения с высоким уровнем использования свойству **numberOfWorkers** можно присвоить значение **3**, чтобы обеспечить большую вычислительную мощность для этого приложения. 
    - Для приложений с низким уровнем использования свойству **numberOfWorkers** можно присвоить значение **1**.

## <a name="next-steps"></a>Дальнейшие действия

- [Подробный обзор планов службы приложений Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Введение в среду службы приложения](environment/app-service-app-service-environment-intro.md)
