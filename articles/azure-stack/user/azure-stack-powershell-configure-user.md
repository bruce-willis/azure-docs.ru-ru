---
title: Настройка пользовательской среды PowerShell в Azure Stack | Документация Майкрософт
description: Настройка пользовательской среды PowerShell в Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: d8b245666989552208f8cbcf0dddfdfc310f65e0
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41947924"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Настройка пользовательской среды PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье предоставляются инструкции по подключению к экземпляру Azure Stack. Необходимо подключиться к управлению ресурсами Azure Stack с помощью PowerShell. Например, с помощью PowerShell можно будет подписываться на предложения, создавать виртуальные машины и развертывать шаблоны Azure Resource Manager. для выполнения командлетов PowerShell.

Чтобы настроить:
  - У вас есть такие требования:
  - Присоединиться к Azure Active Directory (Azure AD) и службам федерации Active Directory (AD FS). 
  - Зарегистрируйте поставщиков ресурсов.
  - Проверьте подключение.

## <a name="prerequisites"></a>Предварительные требования

Приведенные ниже предварительные требования можно выполнить либо с помощью [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо с помощью внешнего клиента на основе Windows, если используется [VPN-подключение](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).

Обязательно замените приведенные ниже переменные сценария значениями из конфигурации Azure Stack:

- **Имя клиента Azure AD**  
  Имя вашего клиента Azure AD, используемое для управления Azure Stack, например yourdirectory.onmicrosoft.com.
- **Конечная точка Azure Resource Manager**  
  Для пакета средств разработки Azure Stack используется значение https://management.local.azurestack.external. Чтобы получить это значение для интегрированных систем Azure Stack, обратитесь к поставщику услуг.

## <a name="connect-with-azure-ad"></a>Подключение к Azure AD

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Подключение к AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId
  ```

## <a name="register-resource-providers"></a>Регистрация поставщиков ресурсов

Поставщики ресурсов не регистрируются автоматически в новых подписках пользователей, для которых на портале не развернуты какие-либо ресурсы. Можно явно зарегистрировать поставщик ресурсов, выполнив следующий сценарий.

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Проверка подключения

Настроив все необходимое, проверьте возможности подключения, воспользовавшись PowerShell для создания ресурсов в Azure Stack. В целях проверки создайте группу ресурсов для приложения и добавьте в нее виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов MyResourceGroup.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
- Вы также можете настроить среду PowerShell для облачного оператора. Это описывается в разделе [Настройка среды PowerShell оператора Azure Stack](../azure-stack-powershell-configure-admin.md).