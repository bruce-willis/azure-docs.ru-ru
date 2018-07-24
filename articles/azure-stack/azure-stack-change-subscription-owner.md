---
title: Обновление владельца пользовательской подписки Azure Stack | Документация Майкрософт
description: Измените владельца выставления счетов для пользовательских подписок Azure STack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: brenduns
ms.reviewer: shnatara
ms.openlocfilehash: de8610944e11d8cf106ac484a0c6634c8661b5a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011242"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Изменение владельца для пользовательской подписки Azure Stack

Операторы Azure Stack могут использовать PowerShell для изменения владельца выставления счетов пользовательской подписки. Одна из причин для изменения владельца заключается в замене пользователя, который увольняется из организации.   

Существует два вида *владельцев*, назначенных к подписке.

- **Владелец выставления счетов** — по умолчанию, это пользовательская учетная запись владельца выставления счетов, которая получает подписку от предложения, а затем является владельцем связи выставления счетов для этой подписки. Эта учетная запись является администратором подписки.  Только одна учетная запись пользователя может иметь это обозначение в подписке. Владелец выставления счетов — это ведущий специалист организации или руководитель команды. 

  Используйте командлет PowerShell **Set-AzsUserSubscription** для изменения владельца выставления счетов.  

- **Владельцы, добавляемые с помощью ролей RBAC** — являются дополнительными пользователями которые предоставляют роли владельца с помощью системы [Role Based Access Control](azure-stack-manage-permissions.md) (RBAC).  Чтобы дополнить владельца выставления счетов, можно добавить любое количество дополнительных пользовательских учетных записей в качестве владельцев. Дополнительные владельцы также являются администраторами подписки и имеют все привилегии для подписки, за исключением разрешения на удаление владельца выставления счетов. 

  Для управления дополнительными владельцами можно использовать PowerShell. Дополнительные сведения см. в разделе [Управление доступом с помощью RBAC и Azure PowerShell]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>Изменение владельца выставления счетов
Выполните следующий сценарий, чтобы изменить владельца выставления счетов пользовательской подписки.  Компьютер, используемый для выполнения сценария, необходимо подключить к Azure Stack и запустить модуль Azure Stack PowerShell версии 1.3.0 или более поздней. Дополнительные сведения см. в статье [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). 

> [!Note]  
>  В многопользовательской службе Azure Stack новый владелец должен быть в той же папке, что и существующий. Прежде чем предоставить владельца подписки пользователю, который находится в другой папке, необходимо сначала [пригласить этого пользователя в свою папку в качестве гостя](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Замените в сценарии следующие значения перед выполнением. 
 
- **$ArmEndpoint**. Указание конечной точки диспетчера ресурсов для среды.  
- **$TenantId**. Указание идентификатора клиента. 
- **$SubscriptionId**. Указание идентификатора подписки.
- **$OwnerUpn**. Указание учетной записи *user@example.com* для добавления нового владельца выставления счетов.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Дополнительная информация
[Управление доступом на основе ролей](azure-stack-manage-permissions.md)
