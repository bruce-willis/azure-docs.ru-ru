---
title: Настройка оповещений о работоспособности ресурсов Azure с помощью шаблонов Resource Manager | Документация Майкрософт
description: Создание оповещений, которые уведомляют о том, когда ресурсы Azure стали недоступны, программными средствами.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/4/2018
ms.author: shtabriz
ms.openlocfilehash: b19a840e5d0d3434d3e3ff91e0d7af037d64fb5a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975366"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Настройка оповещений о работоспособности ресурсов с помощью шаблонов Resource Manager

В этой статье показано, как создать оповещения журнала действий службы "Работоспособность ресурсов" программным образом, используя шаблоны Azure Resource Manager и Azure PowerShell.

Служба "Работоспособность ресурсов Azure" позволяет получать сведения о текущем и прошлых состояниях работоспособности ресурсов Azure. Оповещения службы "Работоспособность ресурсов Azure" помогают практически в реальном времени сообщить вам об изменении состояния работоспособности этих ресурсов. Используя программные средства, клиенты могут создать и настроить оповещения службы "Работоспособность ресурсов" в пакетном режиме.

## <a name="prerequisites"></a>Предварительные требования

Чтобы следовать инструкциям на этой странице, необходимо заранее сделать следующее:

1. Настроить [модуль Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (`AzureRm`).
2. [Создать или повторно использовать группу действий](../monitoring-and-diagnostics/monitoring-action-groups.md), настроенную на уведомление.

## <a name="instructions"></a>Указания
1. С помощью PowerShell войдите в Azure с помощью учетной записи и выберите нужную подписку.

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > С помощью командлета `Get-AzureRmSubscription` можно перечислить подписки, к которым у вас есть доступ.

2. Найдите полный идентификатор Azure Resource Manager для вашей группы действий и сохраните его:

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Создайте шаблон Resource Manager для оповещения службы "Работоспособность ресурсов" в виде файла `resourcehealthalert.json` ([подробности см. ниже](#resource-manager-template-for-resource-health-alerts)) и сохраните его:

4. Создайте развертывание Azure Resource Manager с помощью этого шаблона:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Вам будет предложено ввести имя оповещения и идентификатор ресурса группы действий, скопированные ранее:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resouceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Если все вышло, вы получите подтверждение в PowerShell:

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Обратите внимание, что если вы планируете полностью автоматизировать процесс, вам нужно просто изменить шаблон Resource Manager, чтобы он не выводил запрос на ввод значения на шаге 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Шаблон Resource Manager для оповещений службы "Работоспособность ресурсов"

Этот базовый шаблон можно использовать в качестве отправной точки для создания оповещений службы "Работоспособность ресурсов". Он будет работать так, как написано. Вы подпишетесь на получение оповещений для всех только что активированных событий работоспособности ресурсов во всех ресурсах подписки.

> В нижней части этой статьи мы также включили более сложный шаблон оповещения, который должен увеличить соотношение сигнала и помех для оповещений службы "Работоспособность ресурсов" по сравнению с этим шаблоном.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Тем не менее такое широкое оповещение обычно не рекомендуется использовать. В следующем разделе вы узнаете, как можно ограничить область этого оповещения и сосредоточиться на событиях, которые нас интересуют.

### <a name="adjusting-the-alert-scope"></a>Корректировка области оповещения

Службу "Работоспособность ресурсов" можно настроить для мониторинга событий в трех различных областях:

 * уровень подписки;
 * уровень группы ресурсов;
 * уровень ресурса.

Приведенный здесь шаблон оповещения настроен на уровне подписки, но если необходимо настроить оповещение только для определенных ресурсов или ресурсов в определенной группе ресурсов, нужно просто изменить раздел `scopes` в шаблоне выше.

Раздел с заданной областью на уровне группы ресурсов должен выглядеть так:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

А раздел с заданной областью на уровне ресурса должен выглядеть так:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Например: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Чтобы получить эту строку, перейдите на портал Azure и посмотрите на URL-адрес в разделе ресурса Azure.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Корректировка типов ресурсов, о которых приходят оповещения

Оповещения на уровне подписки или группы ресурсов могут иметь различные типы ресурсов. Если вы хотите, чтобы оповещения поступали только из определенного подмножества типов ресурсов, это можно определить в разделе `condition` шаблона следующим образом:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Здесь мы используем оболочку `anyOf`, чтобы оповещение о работоспособности ресурса сопоставлялось с одним из указанных условий и отправлялись оповещения, предназначенные для определенных типов ресурсов.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Корректировка событий службы "Работоспособность ресурсов", о которых поступают оповещения
Когда в ресурсах происходит событие работоспособности, они могут проходить через ряд шагов, представляющих состояния событий работоспособности: `Active`, `InProgress`, `Updated` и `Resolved`.

Вам может требоваться уведомление только в том случае, если ресурс становится неработоспособным. В этом случае нужно настроить срабатывание оповещения, только когда `status` имеет значение `Active`. Однако, если вы хотите получать уведомления и на других этапах, эти сведения можно добавить следующим образом:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Если необходимо получать уведомления для всех четырех этапов событий работоспособности, можно удалить это условие, и вы будете получать оповещение независимо от свойства `status`.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Корректировка оповещений службы "Работоспособность ресурсов" во избежание событий Unknown (Неизвестно)

Служба "Работоспособность ресурсов" может сообщить последние сведения о работоспособности ресурсов, постоянно отслеживая их с помощью тестовых запусков. Соответствующие состояния работоспособности: Available (Доступно), Unavailable (Недоступно) и Degraded (Пониженная функциональность). Однако, в ситуациях, когда запускатель и ресурс Azure не могут обмениваться данными, для ресурса сообщается состояние работоспособности Unknown (Неизвестно), и это считается активным событием работоспособности.

Тем не менее когда ресурс сообщает о состоянии Unknown (Неизвестно), вполне вероятно, что состояние работоспособности не изменилось с момента последнего точного отчета. Если вы хотите исключить оповещения о событиях Unknown (Неизвестно), можно указать эту логику в шаблоне:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

В этом примере мы уведомляем только о событиях, у которых текущее и будущее состояния работоспособности не соответствуют состоянию Unknown (Неизвестно). Это изменение может быть полезным дополнением, если оповещения отправляются непосредственно на мобильный телефон или на электронную почту.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Корректировка оповещения, чтобы избежать событий, инициированных пользователем

События службы "Работоспособность ресурсов" могут активироваться с помощью событий, инициированных платформой или пользователем. Оповещение имеет смысл отправлять только тогда, когда событие работоспособности вызвано платформой Azure.

Для оповещения можно легко настроить фильтрацию событий только этих типов:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```

## <a name="recommended-resource-health-alert-template"></a>Рекомендуемый шаблон оповещений для службы "Работоспособность ресурсов"

Используя различные корректировки, описанные в предыдущем разделе, можно создать комплексный шаблон предупреждений, настроенный для обеспечения максимального соотношения сигнала и помех.

Мы рекомендуем использовать следующий файл:
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Тем не менее вам лучше знать, какие конфигурации эффективны для вас, поэтому используйте инструменты, приведенные в этой документации, чтобы настроить свою собственную конфигурацию.

## <a name="next-steps"></a>Дополнительная информация

Дополнительная информация о службе "Работоспособность ресурсов".
-  [Обзор службы "Работоспособность ресурсов Azure"](Resource-health-overview.md)
-  [Типы ресурсов и проверок работоспособности, доступные в службе работоспособности ресурсов Azure](resource-health-checks-resource-types.md)

Создание оповещений службы "Работоспособность служб":
-  [Создание оповещений журнала действий для уведомлений службы](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 
