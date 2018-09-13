---
title: Настройка управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью шаблона
description: Пошаговые инструкции по настройке управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью шаблона Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 5acd9050f2044113936d7aef5eb4a92ecf1098bf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338554"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-azure-virtual-machine-scale-using-a-template"></a>Настройка управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин Azure с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять приведенные ниже операции с управляемыми удостоверениями для ресурсов Azure в масштабируемом наборе виртуальных машин Azure с помощью шаблона развертывания Azure Resource Manager.
- Включение и отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин Azure
- Добавление и удаление управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин Azure

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления управляемого удостоверения, назначаемого системой, и (или) управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания управляемого удостоверения, назначаемого пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин.

## <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Так же как портал Azure и сценарии, шаблоны [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) предоставляют возможность развертывать новые или измененные ресурсы, определенные в группе ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение управляемых удостоверений для ресурсов Azure на новой или существующей виртуальной машине выполняется таким же образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/deployment-modes.md) для развертываний.

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

В этом разделе вы узнаете, как включить и отключить управляемое удостоверение, назначаемое системой, с помощью шаблона Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Включение управляемого удостоверения, назначаемого системой, для нового или существующего масштабируемого набора виртуальных машин

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.
   
2. Чтобы включить управляемое удостоверение, назначаемое системой, загрузите шаблон в редактор, найдите интересующий ресурс `Microsoft.Compute/virtualMachinesScaleSets` в разделе ресурсов и добавьте свойство `identity` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachines"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Необязательно.) Добавьте расширение масштабируемого набора виртуальных машин для управляемых удостоверений для ресурсов Azure как элемент `extensionsProfile`. Этот шаг необязателен, так как для получения токенов можно также использовать удостоверение Службы метаданных экземпляров Azure (IMDS).  Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что развертывается расширение масштабируемого набора виртуальных машин Windows (`ManagedIdentityExtensionForWindows`). Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
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

4. Когда все будет готово, необходимо добавить следующие разделы в раздел ресурсов шаблона, который должен выглядеть следующим образом.

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ``` 

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин Azure

Если для масштабируемого набора виртуальных машин управляемое удостоверение, назначаемое системой, больше не требуется, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у вашей виртуальной машины есть только управляемое удостоверение, назначаемое системой, его можно отключить, изменив тип удостоверения на `None`.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**

   Если apiVersion имеет значение `2018-06-01` и в виртуальной машине есть управляемые удостоверения, назначаемые как системой, так и пользователем, удалите `SystemAssigned` из типа удостоверения и оставьте `UserAssigned` вместе со значениями словаря userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01 и более ранних версий**

   Если apiVersion имеет значение `2017-12-01` и у масштабируемого набора виртуальных машин есть управляемые удостоверения, назначаемые как системой, так и пользователем, удалите `SystemAssigned` из типов удостоверения и оставьте `UserAssigned` вместе с массивом управляемых удостоверений, назначаемых пользователем, `identityIds`. 
   
    

   В следующем примере показано, как удалить управляемое удостоверение, назначаемое системой, из масштабируемого набора виртуальных машин без управляемых удостоверений, назначаемых пользователем.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе вы зададите управляемое удостоверение, назначаемое пользователем, для масштабируемого набора виртуальных машин с помощью шаблона Azure Resource Manager.

> [!Note]
> Чтобы создать управляемое удостоверение, назначаемое пользователем, с помощью шаблона Azure Resource Manager, обратитесь к разделу [Создание управляемого удостоверения, назначаемого пользователем](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virutal-machine-scale-set"></a>Задание управляемого удостоверения, назначаемого пользователем, для масштабируемого набора виртуальных машин

1. Чтобы задать управлемое удостоверение, назначаемое пользователем, для масштабируемого набора виртуальных машин, в элемент `resources` добавьте приведенную ниже запись.  Не забудьте заменить `<USERASSIGNEDIDENTITY>` именем созданного управляемого удостоверения, назначаемого пользователем.
   
   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**

   Если apiVersion имеет значение `2018-06-01`, то управляемые удостоверения, назначаемые пользователем, хранятся в формате словаря `userAssignedIdentities`, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменной, определенной в разделе `variables` шаблона.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }
    
   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API версии 2017-12-01**
    
   Если `apiVersion` соответствует `2017-12-01` или более ранней версии, то управляемые удостоверения, назначаемые пользователем, хранятся в массиве `identityIds`, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменной, определенной в разделе variables шаблона.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 

2. (Необязательно.) Добавьте в элемент `extensionProfile` приведенную ниже запись, чтобы задать расширение управляемых удостоверений для ресурсов Azure для масштабируемого набора виртуальных машин. Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS). Используйте следующий синтаксис:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "ManagedIdentityWindowsExtension",
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

3. По завершении шаблон должен выглядеть следующим образом.
   
   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API версии 2017-12-01 и более ранних версий**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин Azure

Если для масштабируемого набора виртуальных машин больше не требуется управляемое удостоверение, назначаемое пользователем, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у масштабируемого набора виртуальных машин есть только управляемое удостоверение, назначаемое пользователем, его можно отключить, изменив тип удостоверения на `None`.

   В следующем примере показано, как удалить все управляемые удостоверения, назначаемые пользователем, из виртуальной машины без управляемых удостоверений, назначаемых системой.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**
    
   Чтобы удалить отдельное управляемое удостоверение, назначаемое пользователем, из масштабируемого набора виртуальных машин, удалите его из словаря `userAssignedIdentities`.

   Если у вас есть удостоверение, назначаемое системой, сохраните его в значении `type` в рамках значения `identity`.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2017-12-01**

   Чтобы удалить отдельное управляемое удостоверение, назначаемое пользователем, из масштабируемого набора виртуальных машин, удалите его из массива `identityIds`.

   Если у вас есть управляемое удостоверение, назначаемое системой, сохраните его в значении `type` в рамках значения `identity`.
   
## <a name="next-steps"></a>Дополнительная информация

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).

