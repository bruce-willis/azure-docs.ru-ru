---
title: Справочник по командлетам PowerShell для планировщика Azure
description: Сведения о командлетах PowerShell для планировщика Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991102"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Справочник по командлетам PowerShell для планировщика Azure

> [!IMPORTANT]
> Служба [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из эксплуатации. Для планирования заданий [попробуйте использовать Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Чтобы писать сценарии для создания заданий планировщика и коллекций заданий и управления ими, можно использовать командлеты PowerShell. В этой статье перечислены основные [командлеты PowerShell для планировщика Azure](/powershell/module/azurerm.scheduler) со ссылками на соответствующие справочные статьи. Сведения об установке Azure PowerShell для подписки Azure см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Дополнительные сведения о [командлетах Azure Resource Manager](/powershell/azure/overview) см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

| Командлет | ОПИСАНИЕ |
|--------|-------------|
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Отключает коллекцию заданий. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Включает коллекцию заданий. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Получает задания планировщика. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Получает коллекции заданий. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Получает журнал заданий. |
| [New-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Создает задание HTTP. |
| [New-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Создает коллекцию заданий. |
| [New-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Создает задание очереди служебной шины. |
| [New-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Создает задание раздела служебной шины. |
| [New-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Создает задание очереди хранилища. |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Удаляет задание планировщика. |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Удаляет коллекцию заданий. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Изменяет задание HTTP планировщика. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Изменяет коллекцию заданий. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Изменяет задание очереди служебной шины. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Изменяет задание раздела служебной шины. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Изменяет задание очереди хранилища. |
||| 

Для получения дополнительных сведений можно выполнить любой из этих командлетов: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>См. также

* [Что такое планировщик Azure?](scheduler-intro.md)
* [Основные понятия, терминология и иерархия сущностей](scheduler-concepts-terms.md)
* [Создание и планирование первого задания с помощью портала Azure](scheduler-get-started-portal.md)
* [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)
