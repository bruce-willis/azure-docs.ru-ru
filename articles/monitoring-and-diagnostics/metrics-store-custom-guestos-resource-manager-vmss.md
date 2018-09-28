---
title: Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для масштабируемого набора виртуальных машин Windows
description: Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для масштабируемого набора виртуальных машин Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d896cb01c7dc2cd4ed028db418f838809c7ce25c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987005"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для масштабируемого набора виртуальных машин Windows

[Расширение системы диагностики Azure (Windows)](azure-diagnostics.md) (WAD) Azure Monitor позволяет собирать метрики и журналы из гостевой операционной системы (гостевой ОС), работающей в составе виртуальной машины, облачной службы или кластера Service Fabric.  Расширение может отправлять данные телеметрии во множество различных расположений, перечисленных в указанной по ссылке выше статье.  

В этой статье описывается процесс отправки метрик производительности гостевой ОС для масштабируемого набора виртуальных машин под управлением Windows в хранилище данных Azure Monitor. Начиная с WAD версии 1.11 метрики можно записывать напрямую в хранилище метрик Azure Monitor, где уже собраны стандартные метрики платформы. Сохранение их в этом расположении позволяет получить доступ к тем же действиям, которые доступны для метрик платформы.  К этим действиям относятся оповещения практически в реальном времени, построение диаграмм, маршрутизация, доступ из REST API и многое другое.  В прошлом расширение WAD выполняло запись в службу хранилища Azure, но не в хранилище данных Azure Monitor.  

Если вы не знакомы с шаблонами Resource Manager, ознакомьтесь с [развертываниями шаблонов](../azure-resource-manager/resource-group-overview.md), а также их структурой и синтаксисом.  

## <a name="pre-requisites"></a>Предварительные требования

- Подписку необходимо зарегистрировать с помощью [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- Необходимо установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) или использовать [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Настройка Azure Monitor в качестве приемника данных 
Расширение системы диагностики Azure использует функцию, называемую "приемники данных", для маршрутизации метрик и журналов в различные расположения.  Ниже показано, как с помощью шаблона Resource Manager и PowerShell развернуть виртуальную машину с использованием нового приемника данных Azure Monitor. 

## <a name="author-resource-manager-template"></a>Создание шаблона Resource Manager 
В этом примере можно использовать общедоступный пример шаблона. Начальные шаблоны можно найти по адресу https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale.  

- **Azuredeploy.json** — это предварительно настроенный шаблон Resource Manager для развертывания масштабируемого набора виртуальных машин.

- **Azuredeploy.Parameters.json** — это файл параметров, в котором хранятся сведения, такие как имя пользователя и пароль, которые вам нужно будет задать для виртуальной машины. Во время развертывания шаблона Resource Manager используются параметры, заданные в этом файле. 

Скачайте и сохраните оба файла в локальном расположении. 

###  <a name="modify-azuredeployparametersjson"></a>Изменение файла azuredeploy.parameters.json
Откройте файл *azuredeploy.parameters.json*. 

- Укажите **vmSKU** (номер SKU виртуальной машины) для развертывания (мы рекомендуем Standard_D2_v3). 
- Укажите **windowsOSVersion** (версия ОС Windows) для масштабируемого набора виртуальных машин (мы рекомендуем 2016-Datacenter). 
- Задайте имя для ресурса масштабируемого набора виртуальных машин, который будет развернут, с помощью свойства **vmssName**. Например, *VMSS-WAD-TEST*.    
- Укажите число виртуальных машин, которые следует запускать в масштабируемом наборе виртуальных машин, с помощью свойства **instanceCount**.
- Введите значения **adminUsername** и **adminPassword** для масштабируемого набора виртуальных машин. Эти параметры используются для удаленного доступа к виртуальным машинам в масштабируемом наборе. Эти параметры используются для удаленного доступа к виртуальной машине. НЕ используйте параметры, указанные в этом шаблоне, во избежание перехвата виртуальной машины. Боты сканируют Интернет на наличие имен пользователей и паролей в общедоступных репозиториях GitHub. Вероятно, они будут тестировать виртуальные машины с этими значениями по умолчанию. 


###  <a name="modify-azuredeployjson"></a>Изменение файла azuredeploy.json
Откройте файл *azuredeploy.json*. 

Добавьте переменную, в которой будут содержаться данные учетной записи хранения в шаблоне Resource Manager. В рамках установки расширения диагностики по-прежнему необходимо указать учетную запись хранения. Все журналы и (или) счетчики производительности, указанные в файле конфигурации диагностики, записываются в указанную учетную запись хранения в дополнение к отправке в хранилище метрик Azure Monitor. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Найдите определение масштабируемого набора виртуальных машин в разделе ресурсов и добавьте в конфигурацию раздел identity. Это обеспечит назначение набору системного удостоверения в Azure. Этот шаг гарантирует, что виртуальные машины в масштабируемом наборе могут отправлять гостевые метрики о себе в Azure Monitor.  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

В ресурсе масштабируемого набора виртуальных машин найдите раздел **virtualMachineProfile**. Добавьте новый профиль с именем **extensionsProfile** для управления расширениями.  


В профиле **extensionProfile** добавьте новое расширение в шаблон, как показано в **разделе VMSS-WAD-extension**.  Этот раздел представляет собой расширение управляемого удостоверения службы (MSI), которое обеспечивает прием выдаваемых метрик в Azure Monitor. В поле **name** может содержаться любое имя. 

Код под расширением MSI также добавляет расширение диагностики и конфигурацию в качестве ресурса расширения в ресурс масштабируемого набора виртуальных машин. При необходимости счетчики производительности можно добавлять или удалять. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed service identity   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Добавьте dependsOn для учетной записи хранения, чтобы обеспечить ее создание в правильном порядке. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Создайте учетную запись хранения, если она еще не создана в шаблоне.  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Сохраните и закройте оба файла. 

## <a name="deploy-the-resource-manager-template"></a>Развертывание шаблона Resource Manager 

> [!NOTE]
> Необходимо запустить расширение системы диагностики Azure версии 1.5 или более поздней, А ТАКЖЕ задать для свойства autoUpgradeMinorVersion значение *true* в шаблоне Resource Manager.  Затем Azure загрузит нужное расширение при запуске виртуальной машины. Если этих параметров нет в шаблоне, измените их и повторно разверните шаблон. 


Для развертывания шаблона Resource Manager мы воспользуемся Azure PowerShell.  

1. Запуск PowerShell 
1. Войдите в Azure, используя командлет `Login-AzureRmAccount`.
1. Получите список подписок с помощью командлета `Get-AzureRmSubscription`.
1. Задайте подписку, в которой вы будете создавать или обновлять виртуальную машину. 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Создайте группу ресурсов для развертываемой виртуальной машины, выполнив указанную ниже команду. 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Не забывайте использовать регион Azure, включенный для пользовательских метрик. См. следующие документы. 
 
1. Выполните приведенные ниже команды, чтобы развернуть виртуальную машину.  
   > [!NOTE] 
   > Если нужно обновить существующий масштабируемый набор, просто добавьте *-Mode Incremental* в конце следующей команды. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. После успешного развертывания вы сможете найти на портале Azure масштабируемый набор виртуальных машин, который будет отправлять метрики в Azure Monitor. 

   > [!NOTE] 
   > С выбранным параметром vmSkuSize могут возникнуть ошибки. В этом случае вернитесь к файлу azuredeploy.json и обновите значение по умолчанию для параметра vmSkuSize. В этом случае рекомендуем попробовать значение "Standard_DS1_v2". 


## <a name="chart-your-metrics"></a>Создание диаграммы метрик 

1. Вход на портал Azure 

1. В меню слева щелкните **Монитор** 

1. На странице "Монитор" щелкните **Метрики**. 

   ![Страница "Метрики"](./media/metrics-store-custom-rest-api/metrics.png) 

1. Измените период агрегирования на **Последние 30 минут**.  

1. В раскрывающемся списке ресурсов выберите масштабируемый набор виртуальных машин, который вы только что создали.  

1. В раскрывающемся списке пространств имен выберите **azure.vm.windows.guest**. 

1. В раскрывающемся списке метрик выберите **Память \% использования выделенной памяти**.  

Затем можно использовать измерения этой метрики для построения диаграммы по метрике для определенной виртуальной машины в масштабируемом наборе или для вывода графика по каждой виртуальной машине в масштабируемом наборе. 



## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).

