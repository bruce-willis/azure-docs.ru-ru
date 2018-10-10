---
title: Изменение, удаление групп управления и управление ими в Azure
description: Узнайте, как сохранять и обновлять иерархию групп управления.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rithorn
ms.openlocfilehash: a3de0df8fde3b271b7ba9bb9aab01dbcd5c3bf08
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991227"
---
# <a name="manage-your-resources-with-management-groups"></a>Управление ресурсами с помощью групп управления

Группы управления — это контейнеры, которые помогают управлять доступом, политикой и соответствием требованиям в нескольких подписках. Вы можете создавать и удалять такие контейнеры и управлять ими, чтобы создать иерархии, которые можно использовать с [политикой Azure](../policy/overview.md) и [элементами управления доступом на основе ролей Azure](../../role-based-access-control/overview.md). Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](overview.md).

Чтобы внести изменения в группу управления, необходимо иметь роль владельца или участника в этой группе. Чтобы получить сведения об имеющихся разрешениях, выберите группу управления, а затем выберите **IAM**. Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Изменение имени группы управления

Имя группы управления можно изменить с помощью портала, PowerShell или Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Изменение имени на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую требуется переименовать.

1. Выберите параметр **Переименовать группу** в верхней части страницы.

   ![Переименование группы](./media/detail_action_small.png)

1. Когда откроется меню, введите новое имя.

   ![Переименование группы](./media/rename_context.png)

1. Щелкните **Сохранить**.

### <a name="change-the-name-in-powershell"></a>Изменение имени в PowerShell

Чтобы обновить отображаемое имя, воспользуйтесь командой **Update-AzureRmManagementGroup**. Например, чтобы изменить имя группы управления с "ИТ-отдел Contoso" на "Группа Contoso", выполните следующую команду:

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Изменение имени в Azure CLI

Для Azure CLI используется команда обновления.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Удаление группы управления

Чтобы удалить группу управления, необходимо выполнить следующие требования:

1. В группе управления нет дочерних групп управления или подписок.

   - Сведения о том, как переместить подписку из группы управления, см. в [этом разделе](#Move-subscriptions-in-the-hierarchy).

   - Сведения о перемещении группы управления в другую группу управления см. в [этом разделе](#Move-management-groups-in-the-hierarchy).

1. В вашей роли владельца или участника в группе управления есть разрешения на запись. Чтобы получить сведения об имеющихся разрешениях, выберите группу управления, а затем выберите **IAM**. Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Удаление на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую требуется удалить.

1. Выберите **Удалить**.

   - Если значок неактивен, наведите указатель мыши на значок, чтобы узнать причину.

   ![Удаление группы](./media/delete.png)

1. Откроется окно, в котором нужно подтвердить удаление группы управления.

   ![Удаление группы](./media/delete_confirm.png)

1. Выберите **Да**.

### <a name="delete-in-powershell"></a>Удаление в PowerShell

В PowerShell для удаления группы управления используется команда **Remove-AzureRmManagementGroup**.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Удаление в Azure CLI

В Azure CLI используется команда az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Просмотр групп управления

Вы можете просматривать все группы управления, в которых у вас есть прямые или наследуемые роли RBAC.  

### <a name="view-in-the-portal"></a>Просмотр на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Загружается страница иерархии групп управления, где можно изучить все группы управления и подписки, к которым у вас есть доступ. При выборе имени группы открывается нижний уровень в иерархии. Навигация работает так же, как и в обозревателе файлов.

   ![Главная](./media/main.png)

1. Чтобы просмотреть сведения о группе управления, перейдите по ссылке **(подробности)** рядом с заголовком группы управления. Если ссылка недоступна, у вас нет разрешения на просмотр этой группы управления.  

### <a name="view-in-powershell"></a>Просмотр в PowerShell

Для получения всех групп используется команда Get-AzureRmManagementGroup.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Для получения сведений об одной группе управления используйте параметр -GroupName.

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="view-in-azure-cli"></a>Просмотр в Azure CLI

Для извлечения всех групп используется команда вывода списка.  

```azurecli-interactive
az account management-group list
```

Для получения сведений об одной группе управления используется команда отображения.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Перемещение подписок в иерархии

Одна из причин для создания группы управления — объединение подписок. Дочерним элементом группы управления могут быть только подписки и группы управления. При перемещении в группу управления подписка наследует все права доступа и политики пользователя из родительской группы управления.

Чтобы перенести подписку, необходимо иметь определенные разрешения:

- Роль "Владелец" в дочерней подписке.
- Роль "Владелец" или "Участник" в новой родительской группе управления.
- Роль "Владелец" или "Участник" в старой родительской группе управления.

Чтобы получить сведения об имеющихся разрешениях, выберите группу управления, а затем выберите **IAM**. Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Перемещение подписок на портале

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Добавление имеющейся подписки в группу управления

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить подписку**.

1. Выберите подписку в списке с правильным идентификатором.

   ![Дочерние элементы](./media/add_context_sub.png)

1. Щелкните "Сохранить".

#### <a name="remove-a-subscription-from-a-management-group"></a>Удаление подписки из группы управления

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которая является родительской.  

1. Выберите многоточие в конце строки подписки в списке, которую требуется переместить.

   ![Move](./media/move_small.png)

1. Выберите **Переместить**.

1. В открывшемся меню выберите **родительскую группу управления**.

   ![Move](./media/move_small_context.png)

1. Щелкните **Сохранить**.

### <a name="move-subscriptions-in-powershell"></a>Перемещение подписок в PowerShell

Чтобы переместить подписку в PowerShell, используйте команду Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Чтобы удалить связь между подпиской и группой управления, используйте команду Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Перемещение подписок в Azure CLI

Чтобы переместить подписку в CLI, используйте команду добавления.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Чтобы удалить подписку из группы управления, используйте команду удаления.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Перемещение групп управления в иерархии  

При перемещении родительской группы управления вместе с ней перемещаются все дочерние ресурсы, включая группы управления, подписки, группы ресурсов и ресурсы.

### <a name="move-management-groups-in-the-portal"></a>Перемещение групп управления на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить группу управления**.

1. В открывшемся меню выберите создать новую или использовать существующую группу управления.

   - Если вы выберете новую, то создадите новую группу управления.
   - Если вы выберете существующую, вы сможете выбрать доступную группу управления из раскрывающегося списка.  

   ![Перемещение](./media/add_context_MG.png)

1. Щелкните **Сохранить**.

### <a name="move-management-groups-in-powershell"></a>Перемещение групп управления в PowerShell

Чтобы переместить группу управления в другой группе, используйте команду Update-AzureRmManagementGroup в PowerShell.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Перемещение групп управления в Azure CLI

Чтобы переместить группу управления с помощью Azure CLI, используйте команду обновления.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о группах управления:

- [Упорядочение ресурсов с помощью групп управления Azure](overview.md)
- [Создание групп управления для организации ресурсов Azure](create.md)
- [Страница для установки модуля Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Просмотр спецификации REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Установка расширения Azure CLI](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)