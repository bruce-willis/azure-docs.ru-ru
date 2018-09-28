---
title: Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для виртуальной машины Windows
description: Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для виртуальной машины Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984080"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Отправка метрик гостевой ОС в хранилище метрик Azure Monitor с помощью шаблона Resource Manager для виртуальной машины Windows

[Расширение системы диагностики Azure (Windows)](azure-diagnostics.md) (WAD) Azure Monitor позволяет собирать метрики и журналы из гостевой операционной системы (гостевая ОС), выполняемой как часть виртуальной машины, облачной службы или кластера Service Fabric.  Расширение может отправлять телеметрию во множество различных расположений, перечисленных в присоединенной ранее статье.  

В этой статье описывается процесс отправки метрик производительности гостевой ОС с виртуальной машины под управлением Windows в хранилище данных Azure Monitor. Начиная с WAD версии 1.11, метрики можно записывать напрямую в хранилище метрик Azure Monitor, где уже собраны стандартные метрики платформы. Сохранение их в этом расположении позволяет получить доступ к тем же действиям, которые доступны для метрик платформы.  К этим действиям относятся оповещения практически в реальном времени, построение диаграмм, маршрутизация, доступ из REST API и многое другое.  В прошлом расширение WAD выполняло запись в службу хранилища Azure, но не в хранилище данных Azure Monitor.   

Если вы не знакомы с шаблонами Resource Manager, ознакомьтесь с [развертываниями шаблонов](../azure-resource-manager/resource-group-overview.md), а также их структурой и синтаксисом.  

## <a name="pre-requisites"></a>Предварительные требования

- Подписки должны быть зарегистрированы в [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Необходимо установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) или использовать [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Настройка Azure Monitor в качестве приемника данных 
Расширение системы диагностики Azure использует функцию, называемую "приемники данных", для маршрутизации метрик и журналов в различные расположения.  В следующих шагах показано, как с помощью шаблона Resource Manager и PowerShell развернуть виртуальную машину с использованием нового приемника данных Azure Monitor. 

## <a name="author-resource-manager-template"></a>Создание шаблона Resource Manager 
В этом примере можно использовать общедоступный пример шаблона. Начальные шаблоны можно найти по адресу https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows. 

- **Azuredeploy.JSON** — это предварительно настроенный шаблон Resource Manager для развертывания виртуальной машины. 

- **Azuredeploy.Parameters.json** — это файл параметров, в котором хранятся сведения, такие как имя пользователя и пароль, которые вам нужно будет задать для виртуальной машины. Во время развертывания шаблона Resource Manager используются параметры, заданные в этом файле. 

Скачайте и сохраните оба файла в локальном расположении. 

###  <a name="modify-azuredeployparametersjson"></a>Изменение файла azuredeploy.parameters.json
Откройте файл *azuredeploy.parameters.json*. 

1. Введите значения *adminUsername* и *adminPassword* для виртуальной машины. Эти параметры используются для удаленного доступа к виртуальной машине. НЕ используйте параметры, указанные в этом шаблоне, во избежание перехвата виртуальной машины. Боты сканируют Интернет на наличие имен пользователей и паролей в общедоступных репозиториях Github. Вероятно, они будут тестировать виртуальные машины с этими значениями по умолчанию.  

1. Создайте уникальный параметр dnsname для виртуальной машины.  

### <a name="modify-azuredeployjson"></a>Изменение файла azuredeploy.json

Откройте файл *azuredeploy.json*. 

Добавьте идентификатор учетной записи хранения в раздел **variables** шаблона после записи для **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Добавьте расширение "Управляемое удостоверение службы" (MSI) в шаблон в верхней части раздела resources.  Расширение гарантирует, что Azure Monitor принимает исходящие метрики.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

Добавьте конфигурацию "identity" к ресурсу виртуальной машины, чтобы убедиться, что Azure присваивает системное удостоверение расширению MSI. Этот шаг гарантирует, что виртуальная машина может выводить гостевые метрики о себе в Azure Monitor. 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

Добавьте следующую конфигурацию, чтобы включить расширение диагностики на виртуальной машине Windows.  Для простой виртуальной машины на основе Resource Manager можно добавить конфигурацию расширения в массив ресурсов для соответствующей виртуальной машины. Строка "sinks": "AzMonSink" и соответствующий SinksConfig далее в разделе позволяет расширению выводить метрики напрямую в Azure Monitor. При необходимости счетчики производительности можно добавлять или удалять.  


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": { 
    "bootDiagnostics": { 
    "enabled": true, 
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


Сохраните и закройте оба файла. 
 

## <a name="deploy-the-resource-manager-template"></a>Развертывание шаблона Resource Manager 

> [!NOTE]
> Необходимо запустить расширение системы диагностики Azure версии 1.5 или более поздней, а также чтобы свойство autoUpgradeMinorVersion имело значение true в шаблоне Resource Manager.  Затем Azure загружает нужное расширение при запуске виртуальной машины. Если этих параметров в шаблоне нет, измените их и повторно разверните шаблон. 


Для развертывания шаблона Resource Manager мы будем использовать Azure PowerShell.  

1. Запуск PowerShell 
1. Войдите в Azure, выполнив команду `Login-AzureRmAccount`.
1. Получите список подписок с помощью командлета `Get-AzureRmSubscription`.
1. Задайте подписку, в которой вы будете создавать или обновлять виртуальную машину. 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Создайте группу ресурсов для развертываемой виртуальной машины, выполнив указанную ниже команду. 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Не забывайте [использовать регион Azure, включенный для пользовательских метрик](metrics-custom-overview.md). 
 
1. Выполните следующие команды, чтобы развернуть виртуальную машину.  
   > [!NOTE] 
   > Если вы хотите обновить имеющуюся виртуальную машину, просто добавьте *-Mode Incremental* в конце следующей команды. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. После успешного развертывания на портале Azure вы сможете найти виртуальную машину, которая будет отправлять метрики в Azure Monitor. 

   > [!NOTE] 
   > С выбранным параметром vmSkuSize могут возникнуть ошибки. В этом случае вернитесь к файлу azuredeploy.json и обновите значение по умолчанию для параметра vmSkuSize. В этом случае рекомендуем попробовать значение "Standard_DS1_v2". 

## <a name="chart-your-metrics"></a>Создание диаграммы метрик 

1. Войдите на портал Azure. 

1. В меню слева щелкните **Монитор**. 

1. На странице "Монитор" щелкните **Метрики**. 

   ![Страница "Метрики"](./media/metrics-store-custom-rest-api/metrics.png) 

1. Измените период агрегирования на **Последние 30 минут**.  

1. В раскрывающемся списке ресурсов выберите только что созданную виртуальную машину. Если вы не изменяли имя в шаблоне, оно должно быть *SimpleWinVM2*.  

1. В раскрывающемся списке пространств имен выберите **azure.vm.windows.guest**. 

1. В раскрывающемся списке метрик выберите **Память \% использования выделенной памяти**.  
 

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).