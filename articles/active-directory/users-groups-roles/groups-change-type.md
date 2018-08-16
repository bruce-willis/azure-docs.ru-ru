---
title: Изменение статического типа членства в группах на динамический в Azure Active Directory | Документация Майкрософт
description: Сведения о создании правил членства для автоматического заполнения групп и о ссылках на эти правила.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 4cc9c446c1a8ff7c82b08ba9787a40598a8b4cd4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450734"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Изменение статического типа членства в группах на динамический в Azure Active Directory

В Azure Active Directory (Azure AD) можно изменить тип членства в группе со статического на динамический (или наоборот). Azure AD сохраняет одинаковое имя и идентификатор группы в системе, поэтому все существующие ссылки на группу остаются действительными. Если вместо этого вы создадите новую группу, вам нужно будет обновить эти ссылки. Динамическое членство в группах исключает накладные расходы на управление при добавлении и удалении пользователей. В этой статье рассказывается, как преобразовывать существующее членство в группах со статического на динамическое с помощью командлетов PowerShell или Центра администрирования Azure AD.

> [!WARNING]
> При изменении существующей статической группы на динамическую из нее удаляются все участники, после чего обрабатывается правило членства для добавления новых участников. Если группа используется для управления доступом к приложениям и ресурсам, исходные участники могут временно утратить этот доступ, пока не завершится обработка правила членства.
>
> Рекомендуется заранее проверить новое правило членства, чтобы убедиться в том, что оно соответствующим образом добавляет элементы в группу.

## <a name="change-the-membership-type-for-a-group"></a>Изменение типа членства для группы

1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с помощью учетной записи глобального администратора или администратора учетных записей пользователей в своем клиенте.
2. Выберите **Группы**.
3. В списке **Все группы** откройте группу, которую нужно изменить.
4. Выберите **Свойства**.
5. На странице **Свойства** группы в зависимости от требуемого типа членства выберите для параметра **Тип членства** значение "Assigned (static)" (Назначаемое (статическое)), "Динамический пользователь" или "Динамическое устройство". Для динамического членства можно использовать конструктор правил, чтобы выбрать параметры простого правила или самостоятельно создать правило членства. 

Ниже приведен пример изменения членства в группе со статического на динамическое.

1. На странице **Свойства** выбранной группы щелкните **Тип членства** > **Динамический пользователь**, затем выберите "Да" в диалоговом окне, объясняющем изменения в членстве в группе, чтобы продолжить. 
  
   ![Выбор типа членства "Динамический пользователь"](./media/groups-change-type/select-group-to-convert.png)
  
2. Выберите **Добавить динамический запрос**, а затем укажите правило.
  
   ![Ввод правила](./media/groups-change-type/enter-rule.png)
  
3. После создания правила выберите **Добавить запрос** в нижней части страницы.
4. Щелкните **Сохранить** на странице **Свойства** группы, чтобы сохранить изменения. **Тип членства** группы будет немедленно обновлен в списке групп.

> [!TIP]
> Преобразование группы может завершиться ошибкой, если введено недопустимое правило членства. Уведомление будет отображаться в правом верхнем углу портала. В нем объясняется, почему правило не может быть принято системой. Внимательно прочитайте его, чтобы понять, как нужно изменить правило, чтобы оно стало допустимым. Примеры синтаксиса правил и полный список поддерживаемых свойств, операторов и значений для правил членства см. в статье [Создание динамических групп с членством на основе атрибутов в Azure Active Directory](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Изменение типа членства для группы (PowerShell)

> [!NOTE]
> Для изменения свойств динамической группы потребуется использовать командлеты **предварительной версии** [PowerShell версии 2 для Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Можно установить предварительную версию из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Ниже приведен пример функций, которые переключают управление членством для существующей группы. В этом примере управление свойством GroupTypes осуществляется аккуратно и сохранены все его существующие значения, вне зависимости от использования динамического членства.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Вот как можно сделать группу статической.

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Вот как можно сделать группу динамической.

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Дополнительная информация

В следующих статьях содержатся дополнительные сведения о группах в Azure Active Directory.

* [Просмотр существующих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](groups-dynamic-membership.md)
