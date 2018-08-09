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
ms.openlocfilehash: bee75bcefb370382825c6867ea504e14102aa107
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628289"
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

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-or-an-existing-azure-virtual-machine-scale-set"></a>Включение назначаемого системой удостоверения для нового или существующего масштабируемого набора виртуальных машин Azure

1. Загрузив шаблон в редактор, найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Имя вашего ресурса может немного отличаться от указанного на этом снимке экрана, в зависимости от используемого редактора и от того, изменяете ли вы шаблон для нового или имеющегося развертывания.
   
   ![Снимок экрана шаблона: поиск виртуальной машины](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Чтобы включить назначенное системой удостоверение, добавьте свойство `"identity"` на том же уровне, что и свойство `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Используйте следующий синтаксис:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Необязательно.) Добавьте расширение масштабируемого набора виртуальных машин MSI как элемент `extensionsProfile`. Этот шаг необязателен, так как для получения токенов можно также использовать удостоверение Службы метаданных экземпляров Azure (IMDS).  Используйте следующий синтаксис:

   >[!NOTE] 
   > В следующем примере предполагается, что развертывается расширение масштабируемого набора виртуальных машин Windows (`ManagedIdentityExtensionForWindows`). Можно также выполнить настройку для Linux, используя расширение `ManagedIdentityExtensionForLinux` для элементов `"name"` и `"type"`.
   >

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

4. По завершении шаблон должен выглядеть следующим образом.

   ![Снимок экрана шаблона после изменения](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

Если для масштабируемого набора виртуальных машин управляемое удостоверение службы больше не требуется, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у масштабируемого набора виртуальных машин есть только назначаемое системой удостоверение, его можно отключить, изменив тип удостоверения на `None`.  Если у масштабируемого набора виртуальных машин есть назначаемые пользователем удостоверения, удалите `SystemAssigned` из типа удостоверения и сохраните `UserAssigned` вместе с массивом назначаемых пользователем удостоверений `identityIds`.  В следующем примере показано, как удалить назначаемое системой удостоверение из масштабируемого набора виртуальных машин без назначаемых пользователем удостоверений.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы присвоите назначенное пользователем удостоверение масштабируемому набору виртуальных машин Azure с помощью шаблона Azure Resource Manager.

> [!Note]
> Сведения о создании назначенного пользователем удостоверения с помощью шаблона Azure Resource Manager см. в разделе [Создание назначенного пользователем удостоверения](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Присвоение назначенного пользователем удостоверения масштабируемому набору виртуальных машин Azure

1. Чтобы присвоить назначенное пользователем удостоверение масштабируемому набору виртуальных машин, в элементе `resources` добавьте приведенную ниже запись.  Не забудьте заменить `<USERASSIGNEDIDENTITY>` именем созданного пользовательского удостоверения.

   > [!Important]
   > Значение `<USERASSIGNEDIDENTITYNAME>`, показанное в следующем примере, должно храниться в переменной.  Кроме того, для поддерживаемой в настоящее время реализации назначения виртуальной машине назначенных пользователем удостоверений в шаблоне Resource Manager версия api должна соответствовать версии в следующем примере. 

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

3.  По завершении шаблон должен выглядеть следующим образом.
   
      ![Снимок экрана с пользовательским удостоверением](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление назначаемого пользователем удостоверения из масштабируемого набора виртуальных машин Azure

Если для масштабируемого набора виртуальных машин управляемое удостоверение службы больше не требуется, сделайте следующее.

1. После входа в Azure локально или через портал Azure используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

2. Загрузив шаблон в [редактор](#azure-resource-manager-templates), найдите нужный ресурс `Microsoft.Compute/virtualMachineScaleSets` в разделе `resources`. Если у масштабируемого набора виртуальных машин есть только назначаемое пользователем удостоверение, его можно отключить, изменив тип удостоверения на `None`.  Если у масштабируемого набора виртуальных машин есть назначаемое системой удостоверение и назначаемые пользователем удостоверения и вам нужно сохранить назначаемое системой удостоверение, удалите `UserAssigned` из типа удостоверения вместе с массивом назначаемых пользователем удостоверений `identityIds`.
    
   Чтобы удалить отдельное назначаемое пользователем удостоверение из масштабируемого набора виртуальных машин, удалите его из массива `identityIds`.
   
   В следующем примере показано, как удалить все назначаемые пользователем удостоверения из масштабируемого набора виртуальных машин без назначаемых системой удостоверений.
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="next-steps"></a>Дополнительная информация

- Чтобы получить более обширное представление о компоненте "Управляемое удостоверение службы", прочитайте [обзор компонента "Управляемое удостоверение службы"](overview.md).

