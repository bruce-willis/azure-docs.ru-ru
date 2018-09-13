---
title: Как создать и удалить управляемое удостоверение, назначаемое пользователем, с помощью Azure Resource Manager
description: Пошаговые инструкции по созданию и удалению управляемого удостоверения, назначаемого пользователем, с помощью Azure Resource Manager.
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
ms.openlocfilehash: adfa7ebfd911bfcbc88e01030777e91c48841784
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338351"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создать управляемое удостоверение, назначаемое пользователем, с помощью Azure Resource Manager.

С помощью шаблона Azure Resource Manager невозможно получить список управляемых удостоверений, назначаемых пользователем, или удалить их.  Создание управляемых удостоверений, назначаемых пользователем, и получение их списка описывается в следующих статьях:

- [Отображение управляемого удостоверения, назначаемого пользователем](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Удаление управляемого удостоверения, назначаемого пользователем](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций, описанных в этой статье, учетной записи требуются следующие роли.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (получения списка) и удаления управляемых удостоверений, назначаемых пользователем.

## <a name="template-creation-and-editing"></a>Создание и редактирование шаблона

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

- Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
- Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
- Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона. 

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем 

Для создания управляемого удостоверения, назначаемого пользователем, используйте следующий шаблон. Замените `<USER ASSIGNED IDENTITY NAME>` собственным значением.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure с помощью шаблона Azure Resource Manager, см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью шаблонов](qs-configure-template-windows-vm.md).


 
