---
title: Как настроить управляемое удостоверение службы на виртуальной машине Azure с помощью шаблона
description: Пошаговые инструкции по настройке управляемого удостоверения службы на виртуальной машине Azure с помощью шаблона Azure Resource Manager.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 4b25c82de4d2d3f4300fbb688c75be74ce63fe40
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42146557"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Настройка управляемого удостоверения службы виртуальной машины с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с управляемым удостоверением службы для виртуальной машины Azure с помощью шаблона развертывания Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания виртуальной машины, а также включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения для виртуальной машины Azure.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для назначения и удаления назначаемого пользователем удостоверения для виртуальной машины и из нее.

## <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Так же как портал Azure и сценарии, шаблоны [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) предоставляют возможность развертывать новые или измененные ресурсы, определенные в группе ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение системного или пользовательского удостоверения для новой или существующей виртуальной машины выполняется таким же образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/deployment-modes.md) для развертываний.

## <a name="system-assigned-identity"></a>Системное удостоверение

В этом разделе вы узнаете, как включить и отключить системное удостоверение с помощью шаблона Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Включение системного удостоверения для создаваемой или существующей виртуальной машины Azure

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину.

2. Чтобы включить назначаемое системой удостоверение, загрузите шаблон в редактор, найдите интересующий ресурс `Microsoft.Compute/virtualMachines` в разделе `resources` и добавьте свойство `"identity"` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachines"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. (Необязательно.) Добавьте расширение виртуальной машины MSI в качестве элемента `resources`. Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).  Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что расширение виртуальной машины Windows (`ManagedIdentityExtensionForWindows`) развертывается. Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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
   }
   ```

4. Когда все будет готово, следующие разделы должны быть добавлены в раздел `resource` шаблона, который в результате должен выглядеть следующим образом.

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Назначение роли удостоверения, назначенного системой, для виртуальной машины

После включения удостоверения, назначенного системой, на виртуальной машине, вы можете предоставить ему роль, например **Читатель**.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину.
 
2. Загрузите шаблон в [редактор](#azure-resource-manager-templates) и добавьте приведенную ниже информацию, чтобы предоставить виртуальной машине доступ для **чтения** к группе ресурсов, в которой она создана.  Структура шаблона зависит от редактора и выбранной модели развертывания.
   
   Добавьте следующий элемент в раздел `parameters`:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Добавьте следующий элемент в раздел `variables`:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Добавьте следующий элемент в раздел `resources`:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Отключение системного удостоверения в виртуальной машине Azure

При наличии виртуальной машины, которой больше не требуется управляемое удостоверение службы, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachines` в разделе `resources`. Если у вашей виртуальной машины есть только назначаемое системой удостоверение, его можно отключить, изменив тип удостоверения на `None`.  
   
   **Microsoft.Compute/virtualMachines API версии 2018-06-01**

   Если у виртуальной машины есть удостоверения, назначенные как системой, так и пользователями, удалите `SystemAssigned` из типа удостоверения и сохраните `UserAssigned` вместе со значениями словаря `userAssignedIdentities`.

   **Microsoft.Compute/virtualMachines API версии 2018-06-01 и более ранних версий**
   
   Если значение `apiVersion` соответствует `2017-12-01` и у виртуальной машины есть удостоверения, назначенные как системой, так и пользователями, удалите `SystemAssigned` из типа удостоверения и сохраните`UserAssigned` с массивом пользовательских удостоверений `identityIds`.  
   
В следующем примере показано, как удалить назначенное системой удостоверение с виртуальной машины без пользовательских удостоверений:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы присвоите пользовательское удостоверение виртуальной машине Azure с помощью шаблона Azure Resource Manager.

> [!Note]
> Сведения о создании пользовательского удостоверения с помощью шаблона Azure Resource Manager приведены в разделе [Создание пользовательского удостоверения](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Назначение пользовательского удостоверения виртуальной машине Azure

1. Чтобы назначить пользовательское удостоверение виртуальной машине, в элементе `resources` добавьте приведенную ниже запись.  Не забудьте заменить `<USERASSIGNEDIDENTITY>` именем созданного пользовательского удостоверения.

   **Microsoft.Compute/virtualMachines API версии 2018-06-01**

   Если значение `apiVersion` соответствует `2018-06-01`, удостоверения назначаемые пользователем, хранятся в `userAssignedIdentities` формате словаря, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменной, определенной в разделе шаблона `variables`.

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines API версии 2017-12-01 и более ранних версий**
    
   Если значение `apiVersion` соответствует `2017-12-01`, удостоверения, назначаемые пользователем, хранятся в массиве `identityIds`, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменных, определенных в разделе шаблона `variables`.
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. (Необязательно.) Далее, добавьте в элемент `resources` приведенную ниже запись, чтобы назначить расширение управляемых удостоверений виртуальной машине. Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS). Используйте следующий синтаксис:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3. Когда все будет готово, следующие разделы должны быть добавлены в раздел `resource` шаблона, который в результате должен выглядеть следующим образом.
   
   **Microsoft.Compute/virtualMachines API версии 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
   **Microsoft.Compute/virtualMachines API версии 2017-12-01 и более ранних версий**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
    

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Удаление назначаемого пользователем удостоверения из виртуальной машины Azure

При наличии виртуальной машины, которой больше не требуется управляемое удостоверение службы, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, содержащей виртуальную машину.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachines` в разделе `resources`. Если у вашей виртуальной машины есть только назначаемое пользователем удостоверение, его можно отключить, изменив тип удостоверения на `None`.
 
   В следующем примере показано, как удалить все назначаемые пользователем удостоверения из виртуальной машины без назначаемых системой удостоверений.
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines API версии 2018-06-01 и более ранних версий**
    
   Чтобы удалить отдельное назначаемое пользователем удостоверение из виртуальной машины, удалите его из словаря `useraAssignedIdentities`.

   Если у вас есть удостоверение, назначенное системой, сохраните его в значении `type` в рамках значения `identity`.
 
   **Microsoft.Compute/virtualMachines API версии 2017-12-01**

   Чтобы удалить отдельное назначаемое пользователем удостоверение из виртуальной машины, удалите его из массива `identityIds`.

   Если у вас есть удостоверение, назначенное системой, сохраните его в значении `type` в рамках значения `identity`.
   
## <a name="related-content"></a>Связанная информация

- Чтобы получить более обширное представление о компоненте "Управляемое удостоверение службы", прочитайте [обзор компонента "Управляемое удостоверение службы"](overview.md).

