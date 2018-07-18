---
title: Назначение пользователю ролей администратора в Azure Active Directory | Документы Майкрософт
description: Как изменить административную информацию о пользователе в Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: ec30f1507bfa45c29709a7f4b7dc1e91aa25ca57
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440754"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Назначение пользователю ролей администратора в Azure Active Directory
В этой статье описывается назначение роли администратора пользователю в Azure Active Directory (Azure AD). Сведения о добавлении новых пользователей в организации см. в статье [Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory](../add-users-azure-active-directory.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## <a name="assign-a-role-to-a-user"></a>Назначение роли пользователю
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора или привилегированного администратора каталога.

2. Щелкните **Azure Active Directory**, выберите **Пользователи**, а затем выберите из списка определенного пользователя.

    ![Открытие страницы "Управление пользователями"](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. Для выбранного пользователя щелкните **Роль каталога**, выберите **Добавить роль**, а затем выберите соответствующие роли администратора из списка **Роли каталога**, например **Администратор условного доступа**. Дополнительные сведения о ролях администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). 

    ![Назначение пользователя для роли](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Нажмите кнопку **Выбрать**, чтобы сохранить изменения.

## <a name="next-steps"></a>Дополнительная информация
* [Краткое руководство по добавлению новых пользователей в Azure Active Directory](add-users-azure-active-directory.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Добавление гостевых пользователей из другого каталога](../b2b/what-is-b2b.md) 
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Восстановление удаленного пользователя](active-directory-users-restore.md)
