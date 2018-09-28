---
title: Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure для входа
description: Пошаговые инструкции с примерами использования субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине Azure для входа и доступа к ресурсам из клиентского сценария.
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
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: bf363e4b03fb604e1b9af0d30b6e4ac471a41821
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980293"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure для входа 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Эта статья содержит примеры сценариев PowerShell и интерфейса командной строки, позволяющих войти в систему с использованием субъекта-службы управляемых удостоверений для ресурсов Azure, а также рекомендации по некоторым важным вопросам, таким как обработка ошибок.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Если вы планируете использовать примеры Azure PowerShell или CLI из этой статьи, убедитесь, что у вас установлена последняя версия [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Во всех примерах сценариев в этой статье предполагается, что клиент командной строки выполняется на виртуальной машине с включенными управляемыми удостоверениями для ресурсов Azure. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении управляемых удостоверений для ресурсов Azure на виртуальной машине см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-portal-windows-vm.md) или в одном из вариантов статьи (с использованием PowerShell, интерфейса командной строки, шаблона или пакета SDK для Azure). 
> - Чтобы избежать ошибок во время доступа к ресурсам, для управляемого удостоверения виртуальной машины необходимо предоставить по меньшей мере роль "Читатель" в соответствующей области (на уровне виртуальной машины или выше), то есть разрешить операции Azure Resource Manager на виртуальной машине. Дополнительные сведения см. в статье [Назначение доступа на основе управляемого удостоверения службы для ресурса с помощью портала Azure](howto-assign-access-portal.md).

## <a name="overview"></a>Обзор

Управляемые удостоверения для ресурсов Azure предоставляют [объект субъекта-службы](../develop/developer-glossary.md#service-principal-object), который [создается при включении управляемых удостоверений для ресурсов Azure](overview.md#how-does-it-work) на виртуальной машине. Субъекту-службе можно предоставить доступ к определенным ресурсам Azure и использовать его в качестве удостоверения для входа и использования ресурсов из скриптов и клиентов командной строки. Чтобы клиентский скрипт мог обращаться к защищенным ресурсам с собственным удостоверением, обычно нужно выполнить следующие условия:  

   - зарегистрировать скрипт в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD и получить соответствующее согласие;
   - выполнить вход из скрипта с соответствующим субъектом-службой, используя учетные данные приложения (обычно они встраиваются в скрипт).

Благодаря управляемым удостоверениям для ресурсов Azure вашему клиенту сценария больше не требуется делать ни то, ни другое, так как он может войти с помощью субъекта-службы управляемых удостоверений для ресурсов Azure. 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине;  
2. вызвать Azure Resource Manager и получить идентификатор субъекта-службы виртуальной машины. CLI автоматически управляет получением и использованием маркера. Не забудьте указать правильное имя виртуальной машины вместо `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине;  
2. вызвать командлет Azure Resource Manager для получения сведений о виртуальной машине. PowerShell автоматически управляет использованием маркера.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список ресурсов, которые поддерживают Azure AD и были протестированы с управляемыми удостоверениями для ресурсов Azure и соответствующими идентификаторами ресурсов, приведен в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](services-support-msi.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Руководство по обработке ошибок 

Такие ответы могут обозначать, что управляемые удостоверения для ресурсов Azure на виртуальной машины настроены неправильно:

- PowerShell: *Invoke-WebRequest : Unable to connect to the remote server* (Invoke-WebRequest: Unable to connect to the remote server)
- CLI: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Не удалось получить маркер от http://localhost:50342/oauth2/token, получена ошибка HTTPConnectionPool(host='localhost', port=50342)). 

Если вы встретите любую из этих ошибок, найдите виртуальную машину Azure на [портале Azure](https://portal.azure.com) и выполните на ней следующие действия:

- Перейдите на страницу **Удостоверение** и убедитесь, что параметр **Назначено системой** имеет значение "Да".
- Откройте страницу **Расширения** и убедитесь, что расширение управляемых удостоверений для ресурсов Azure **(прекращение поддержки запланировано на январь 2019 г.)** успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть управляемые удостоверения для ресурсов Azure для ресурса или устранить неполадки развертывания. Если вам нужна помощь с конфигурацией виртуальной машины, ознакомьтесь с разделом [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине с помощью портала Azure](qs-configure-portal-windows-vm.md).

## <a name="next-steps"></a>Дополнительная информация

- Чтобы включить управляемые удостоверения для ресурсов Azure на виртуальной машине Azure, обратитесь к статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md) или [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).






