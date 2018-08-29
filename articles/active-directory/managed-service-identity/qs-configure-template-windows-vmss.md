---
title: Настройка управляемого удостоверения службы в масштабируемом наборе виртуальных машин Azure с помощью шаблона
description: Пошаговые инструкции по настройке управляемого удостоверения службы в масштабируемом наборе виртуальных машин Azure с помощью шаблона Azure Resource Manager.
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
ms.openlocfilehash: 68304b3e5eea50aba28f46344abcbd7ad060c5c8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42146969"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Настройка управляемого удостоверения службы в масштабируемом наборе виртуальных машин с помощью шаблона

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять приведенные ниже операции с управляемым удостоверением службы для масштабируемого набора виртуальных машин Azure с помощью шаблона развертывания Azure Resource Manager.
- Включение и отключение назначаемого системой удостоверения в масштабируемом наборе виртуальных машин Azure
- Добавление и удаление назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин Azure

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения из масштабируемого набора виртуальных машин.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин.

## <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Так же как портал Azure и сценарии, шаблоны [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) предоставляют возможность развертывать новые или измененные ресурсы, определенные в группе ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

   - Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу имеющийся общий или [шаблон из краткого руководства](https://azure.microsoft.com/documentation/templates/).
   - Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) или от [текущего состояния развертывания](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
   - Использование [проекта группы ресурсов Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio для создания и развертывания шаблона.  

Независимо оттого, какой вариант выбран, во время первоначального развертывания и повторного развертывания в шаблоне используется одинаковый синтаксис. Включение управляемого удостоверения службы на новой или существующей виртуальной машине выполняется таким же образом. Коме того, по умолчанию Azure Resource Manager выполняет [добавочное обновление](../../azure-resource-manager/deployment-modes.md) для развертываний.

## <a name="system-assigned-identity"></a>Удостоверение, назначенное системой

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение с помощью шаблона Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Включение назначаемого системой удостоверения для нового или существующего масштабируемого набора виртуальных машин

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.
   
2. Чтобы включить назначаемое системой удостоверение, загрузите шаблон в редактор, найдите интересующий ресурс `Microsoft.Compute/virtualMachinesScaleSets` в разделе ресурсов и добавьте свойство `identity` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachines"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Необязательно.) Добавьте расширение масштабируемого набора виртуальных машин MSI как элемент `extensionsProfile`. Этот шаг необязателен, так как для получения токенов можно также использовать удостоверение Службы метаданных экземпляров Azure (IMDS).  Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что развертывается расширение масштабируемого набора виртуальных машин Windows (`ManagedIdentityExtensionForWindows`). Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

Если для масштабируемого набора виртуальных машин управляемое удостоверение службы больше не требуется, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у вашей виртуальной машины есть только назначаемое системой удостоверение, его можно отключить, изменив тип удостоверения на `None`.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**

   Если apiVersion соответствует `2018-06-01` и в виртуальной машине есть удостоверения, назначаемые как системой, так и пользователем, удалите `SystemAssigned` из типов удостоверения и оставьте `UserAssigned` вместе со значениями словаря userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01 и более ранних версий**

   Если apiVersion соответствует `2017-12-01` и у масштабируемого набора виртуальных машин есть удостоверения, назначаемые как системой, так и пользователем, удалите `SystemAssigned` из типов удостоверения и оставьте `UserAssigned` вместе с массивом назначаемых пользователем удостоверений `identityIds`. 
   
    

   В следующем примере показано, как удалить назначаемое системой удостоверение из масштабируемого набора виртуальных машин без назначаемых пользователем удостоверений.
   
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

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы присвоите удостоверение, назначаемое пользователем масштабируемому набору виртуальных машин с помощью шаблона Azure Resource Manager.

> [!Note]
> Сведения о создании пользовательского удостоверения с помощью шаблона Azure Resource Manager приведены в разделе [Создание пользовательского удостоверения](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Присвоение назначенного пользователем удостоверения масштабируемому набору виртуальных машин Azure

1. Чтобы назначить удостоверение, назначаемое пользователем масштабируемому набору виртуальных машин, в элементе `resources` добавьте приведенную ниже запись.  Не забудьте заменить `<USERASSIGNEDIDENTITY>` именем созданного пользовательского удостоверения.
   
   **Microsoft.Compute/virtualMachineScaleSets API версии 2018-06-01**

   Если apiVersion соответствует `2018-06-01`, удостоверения, назначаемые пользователем, хранятся в формате словаря `userAssignedIdentities`, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменной, определенной в разделе шаблона `variables`.

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
    
   Если `apiVersion` соответствует `2017-12-01` или более ранней версии, удостоверения, назначаемые пользователем, хранятся в массиве `identityIds`, а значение `<USERASSIGNEDIDENTITYNAME>` должно храниться в переменных, определенных в разделе шаблона.

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

2. Чтобы назначить расширение управляемого удостоверения масштабируемому набору виртуальных машин, добавьте в элемент приведенную ниже запись `extensionProfile` (необязательно). Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS). Используйте следующий синтаксис:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление назначаемого пользователем удостоверения из масштабируемого набора виртуальных машин Azure

Если для масштабируемого набора виртуальных машин управляемое удостоверение службы больше не требуется, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у масштабируемого набора виртуальных машин есть только назначаемое пользователем удостоверение, его можно отключить, изменив тип удостоверения на `None`.

   В следующем примере показано, как удалить все назначаемые пользователем удостоверения из виртуальной машины без назначаемых системой удостоверений.

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
    
   Чтобы удалить отдельное назначаемое пользователем удостоверение из масштабируемого набора виртуальных машин, удалите его из словаря `userAssignedIdentities`.

   Если у вас есть удостоверение, назначенное системой, сохраните его в значении `type` в рамках значения `identity`.

   **Microsoft.Compute/virtualMachineScaleSets API версии 2017-12-01**

   Чтобы удалить отдельное назначаемое пользователем удостоверение из масштабируемого набора виртуальных машин, удалите его из массива `identityIds`.

   Если у вас есть удостоверение, назначенное системой, сохраните его в значении `type` в рамках значения `identity`.
   
## <a name="next-steps"></a>Дополнительная информация

- Чтобы получить более обширное представление о компоненте "Управляемое удостоверение службы", прочитайте [обзор компонента "Управляемое удостоверение службы"](overview.md).

