---
title: Обновление номера SKU или ОС виртуальных машин в кластере Azure Service Fabric | Документация Майкрософт
description: Сведения об обновлении виртуальных машин в типе первичного узла кластера Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: bf707bf4b1c001b5467dd9954e9c6feb55e65654
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660246"
---
# <a name="upgrade-the-primary-node-type-vms-of-a-service-fabric-cluster"></a>Обновление виртуальных машин в типе первичного узла кластера Service Fabric
В этой статье описывается, как обновить виртуальные машины типа первичного узла кластера Service Fabric, работающего в Azure.  Кластер Service Fabric — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Компьютер или виртуальная машина, которая входит в состав кластера. Масштабируемые наборы виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Azure, [настроен как отдельный масштабируемый набор](service-fabric-cluster-nodetypes.md). Затем каждым типом узла можно управлять отдельно. После создания кластера Service Fabric можно масштабировать тип узла кластера вертикально (изменение ресурсов узлов) или обновить ОС виртуальных машин типа узла.  Кластер можно масштабировать в любое время, даже когда в нем выполняются рабочие нагрузки.  Вместе с кластером автоматически масштабируются ваши приложения.

> [!WARNING]
> Мы рекомендуем не изменять номер SKU виртуальной машины масштабируемого набора или типа узла, если он не выполняется на [уровне надежности Silver или более высоком](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Изменение размера (номера SKU) виртуальной машины — внутренняя операция инфраструктуры, которая может привести к потере данных. Без какой-либо возможности отложить или отследить это изменение вероятна ситуация, в которой операция приведет к потере данных служб с отслеживанием состояния или вызовет другие непредвиденные проблемы в работе, даже для рабочих нагрузок без отслеживания состояния. 
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Обновление размера и операционной системы виртуальных машин типа первичного узла
В этом разделе описана процедура обновления размера и операционной системы виртуальных машин типа первичного узла.  После обновления виртуальные машины типа первичного узла будут иметь размер Standard D4_V2 и работать под управлением Windows Server 2016 Datacenter с контейнерами.

> [!WARNING]
> Прежде чем выполнять эту процедуру для кластера рабочей среды, рекомендуется изучить образцы шаблонов и применить шаги для тестового кластера. Кластер будет недоступен в течение некоторого времени.

1. Разверните исходный кластер с двумя типами узлов и двумя масштабируемыми наборами (один набор на один тип узла), используя эти примеры файлов [шаблона](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) и [параметров](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Оба масштабируемых набора имеют размер Standard D2_V2 и работают под управлением Windows Server 2012 R2 Datacenter.  Подождите, пока на кластере будет выполнено базовое обновление.   
2. Разверните пример приложения с отслеживанием состояния в кластере (необязательно).
3. Когда вы решите обновить виртуальные машины типа первичного узла, добавьте новый масштабируемый набор в тип первичного узла, используя эти примеры файлов [шаблона](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) и [параметров](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json). В итоге в типе первичного узла будет два масштабируемых набора.  Системные службы и пользовательские приложения могут перемещаться между виртуальными машинами в двух разных масштабируемых наборах.  Виртуальные машины нового масштабируемого набора будут иметь размер Standard D4_V2 и работать под управлением Windows Server 2016 Datacenter с контейнерами.  Вместе с новым масштабируемым набором также будут добавлены новая подсистема балансировки нагрузки и общедоступный IP-адрес.  
    Чтобы найти новый масштабируемый набор в шаблоне, выполните поиск ресурса Microsoft.Compute/virtualMachineScaleSets, названного с использованием параметра *vmNodeType2Name*.  Новый масштабируемый набор добавляется в тип первичного узла с использованием параметра "properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef".
4. Проверьте состояние работоспособности кластера и убедитесь, что все узлы работоспособны.
5. Отключите узлы в старом масштабируемом наборе типа первичного узла, чтобы удалить узел. Вы можете отключить все узлы сразу, и операции будут поставлены в очередь. Дождитесь отключения всех узлов, что может занять некоторое время.  Когда старые узлы в типе узла будут отключены, системные службы и начальные узлы будут перемещены на виртуальные машины нового масштабируемого набора в типе первичного узла.
6. Удалите старый масштабируемый набор из типа первичного узла.
7. Удалите подсистему балансировки нагрузки, связанную со старым масштабируемым набором. Кластер недоступен, пока новый общедоступный IP-адрес и подсистема балансировки нагрузки настраиваются для нового масштабируемого набора.  
8. Сохраните параметры DNS общедоступного IP-адреса, связанного с масштабируемым набором старого типа первичного узла, в переменной и удалите этот общедоступный IP-адрес.
9. Замените параметры DNS общедоступного IP-адреса, связанного с масштабируемым набором нового типа первичного узла, параметрами DNS удаленного общедоступного IP-адреса.  Кластер теперь снова станет доступным.
10. Удалите состояние узлов из кластера.  Если уровень устойчивости старого масштабируемого набора был "Серебряный" или "Золотой", этот шаг выполняется системой автоматически.
11. Если на предыдущем шаге вы развернули приложение с отслеживанием состояния, убедитесь в правильности его работы.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об [обновлениях приложений](service-fabric-concepts-scalability.md).
* [Руководство. Масштабирование кластера Service Fabric](service-fabric-tutorial-scale-cluster.md)
* [Масштабируйте кластер Azure программно](service-fabric-cluster-programmatic-scaling.md), используя свободный пакет SDK для вычислений Azure.
* [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md)

