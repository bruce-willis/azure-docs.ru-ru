---
title: Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт
description: Описывается управление службами Azure Analysis Services с помощью PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4e819bdce971e92e4b2d99e68f51ddbf8a22182
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597473"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Для выполнения таких задач управления сервером, как создание или удаление сервера, приостановка или возобновление работы сервера или изменение уровня обслуживания (уровня служб), используются командлеты Azure Resource Manager (AzureRM) и Analysis Services (сервер). Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются командлеты, включенные в том же модуле SqlServer, что и в службах SQL Server Analysis Services.

## <a name="permissions"></a>Разрешения
Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи или субъекта-службы, запускающих планировщик, должны быть привилегии администратора на сервере служб Analysis Services. 

Для выполнения операций с сервером с использованием командлетов AzureRm учетная запись или планировщик учетной записи должны также принадлежать к роли владельца для данного ресурса (указывается в настройках [управления доступом на основе ролей (RBAC) в Azure](../role-based-access-control/overview.md)). 

## <a name="resource-management-operations"></a>Операции управления ресурсами 
Модуль — [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Командлет|ОПИСАНИЕ| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Возвращает сведения об экземпляре сервера.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Создает экземпляр сервера.|   
|[New-AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Создает настройки брандмауэра служб Analysis Services.|   
|[New-AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Создает правило брандмауэра служб Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Удаляет экземпляр сервера.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Возобновляет работу экземпляра сервера.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Приостанавливает работу экземпляра сервера.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Изменяет экземпляр сервера.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Проверяет существование экземпляра сервера.| 

## <a name="server-management-operations"></a>Операции управления сервером

Модуль — [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Командлет|ОПИСАНИЕ| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Добавляет учетную запись, прошедшую аутентификацию и используемую для запросов командлета к серверу Azure Analysis Services.| 
|[Export-AzureAnalysisServicesInstance]()|Экспортирует журнал из экземпляра сервера службы Analysis Services в среде, в которую выполнен вход в данный момент, как указано в команде Add AzureAnalysisServicesAccount.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Перезапускает экземпляр сервера служб Analysis Services в среде, в которую выполнен вход в данный момент. Указан в команде Add AzureAnalysisServicesAccount.|  
|[Sync-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Синхронизирует указанную базу данных на конкретном экземпляре сервера служб Analysis Services со всеми экземплярами запросов с масштабированием в среде, в которую выполнен вход в данный момент, как указано в команде Add-AzureAnalysisServicesAccount.|  

## <a name="database-operations"></a>Операции с базой данных

Для операций с базами данных служб Azure Analysis Services используется тот же модуль [SqlServer](https://www.powershellgallery.com/packages/SqlServer), что и для служб SQL Server Analysis Services. Однако для служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SqlServer предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для служб Azure Analysis Services поддерживаются следующие командлеты из модуля SqlServer.

  
|Командлет|ОПИСАНИЕ|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Добавление участника в роль базы данных.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Архивация базы данных Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Удаление участника из роли базы данных.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Выполнение сценария TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Обработка базы данных.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Обработка секции.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Обработка таблицы.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Объединение секции.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Восстановление базы данных Analysis Services.| 
  

## <a name="related-information"></a>Связанные сведения

* [Скачивание модуля PowerShell SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Скачивание SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Модуль SqlServer в коллекции PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://msdn.microsoft.com/library/mt712541.aspx) (Программирование табличной модели для уровня совместимости 1200 и более высокого)
