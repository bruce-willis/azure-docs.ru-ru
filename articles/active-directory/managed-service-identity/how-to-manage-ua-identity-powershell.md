---
title: Создание, получение списка и удаление пользовательских удостоверений (MSI) с помощью Azure PowerShell
description: Пошаговые инструкции по созданию, получению списка и удалению пользовательских управляемых удостоверений службы с помощью Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 3c4aa6d91d5f8c1e28cb52f6846f6ac9feeb4edd
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42143385"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Создание, получение списка и удаление пользовательских удостоверений с помощью Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет службы Azure с управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создавать и удалять пользовательские удостоверения, а также получить их список с помощью Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано.
- Чтобы установить и использовать PowerShell локально для работы с этим руководством, понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Если модуль PowerShell запущен локально, необходимо также выполнить следующие действия. 
    - Выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.
    - Установите [PowerShellGet последней версии](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Запустите `Install-Module -Name PowerShellGet -AllowPrerelease`, чтобы получить предварительную версию модуля `PowerShellGet` (может потребоваться `Exit` из текущего сеанса PowerShell после запуска этой команды для установки модуля `AzureRM.ManagedServiceIdentity`).
    - В этой статье необходимо запустить `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease`, чтобы установить предварительную версию модуля `AzureRM.ManagedServiceIdentity` для выполнения назначенных операций идентификации пользователей.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (вывода списка) и удаления назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для чтения (вывода списка) свойств назначаемого пользователем удостоверения.

## <a name="create-a-user-assigned-identity"></a>Создание пользовательского удостоверения

Чтобы создать пользовательское удостоверение, выполните команду [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). Параметр `ResourceGroupName` указывает группу ресурсов, в которой создается пользовательское удостоверение, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Получение списка пользовательских удостоверений

Чтобы получить список пользовательских удостоверений, выполните команду [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано пользовательское удостоверение. Замените `<RESOURCE GROUP>` собственным значением.

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
В ответе пользовательские удостоверения содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Удаление пользовательского удостоверения

Чтобы удалить пользовательское удостоверение, выполните команду [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  Параметр `-ResourceGroupName` указывает группу ресурсов, в которой было создано пользовательское удостоверение, а параметр `-Name` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Удаление пользовательского удостоверения не удаляет ссылки из ресурсов, которым оно было назначено. Назначения удостоверения должны быть удалены отдельно.

## <a name="related-content"></a>Связанная информация

Полный список команд Azure PowerShell для MSI и дополнительные сведения о них приведены в разделе [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
