---
title: Назначение ролей каталога пользователям с помощью Azure AD PIM | Документация Майкрософт
description: Узнайте, как назначать пользователям роли каталога с помощью Azure Active Directory Privileged Identity Management и портала Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1834addb4e51030afda43a2d7acad5d7ffc1889a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226702"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Назначение ролей каталога пользователям с помощью Azure AD PIM

С помощью Azure Active Directory (Azure AD) глобальный администратор может создавать **постоянные** назначения ролей в каталоге. Эти назначения ролей можно создать с помощью [портала Azure](../users-groups-roles/directory-assign-admin-roles.md) или [команд PowerShell](/powershell/module/azuread#directory_roles).

Служба Azure AD Privileged Identity Management также позволяет администраторам привилегированных ролей создавать постоянные назначения ролей каталога. Кроме того, администраторы привилегированных ролей могут предоставлять пользователям **разрешение** на получение ролей каталога. Такой разрешенный администратор может активировать роль при необходимости. Срок действия его разрешений истекает, когда роль больше не требуется ему для работы. Сведения о ролях, которыми можно управлять с помощью PIM, см. в разделе [Роли каталога, которыми можно управлять с помощью Azure AD Privileged Identity Management](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Предоставление пользователю разрешения на роль

Выполните следующие действия, чтобы предоставить пользователю разрешение на роль каталога Azure AD.

1. Войдите на [портал Azure](https://portal.azure.com/) с учетными данными пользователя, который является участником роли [Администратор привилегированных ролей](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Сведения о том, как предоставить другому пользователю доступ для управления PIM, см. в разделе [Предоставление доступа к Azure AD Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Откройте страницу **Azure AD Privileged Identity Management**.

    Если компонент PIM еще не включен на портале Azure, обратитесь к статье [Приступая к использованию Azure AD Privileged Identity Management](pim-getting-started.md).

1. Щелкните **Роли каталога Azure AD**.

1. Щелкните **Роль (предварительная версия)** или **Участники**.

    ![Роли каталога Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Щелкните **Добавить участника**, чтобы открыть диалоговое окно "Добавление управляемых участников".

1. Щелкните **Выберите роль**, выберите роль, которой вы хотите управлять, и нажмите кнопку **Выбрать**.

    ![Выбор роли](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Щелкните **Выберите участников**, выберите пользователей, которым нужно назначить роли, а затем нажмите кнопку **Выбрать**.

    ![Выбор роли](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. В диалоговом окне "Добавление управляемых участников" нажмите кнопку **ОК**, добавить пользователя к роли.

     После назначения роли выбранный пользователь отобразится в списке участников как **разрешенный** для этой роли.

    ![Пользователь, имеющий разрешение на роль](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Предоставив пользователям разрешение на получение роли, сообщите ему, что он может активировать роль согласно инструкциям в статье [Как активировать и деактивировать роли в компоненте управления привилегированными пользователями Azure AD](pim-how-to-activate-role.md).

    Во время активации разрешенным администраторам будет предложено зарегистрироваться в службе "Многофакторная идентификация Azure" (MFA). Если пользователь не может зарегистрироваться для использования MFA или использует учетную запись Майкрософт (как правило, @outlook.com), необходимо назначить ему все роли на постоянной основе.

## <a name="make-a-role-assignment-permanent"></a>Настройка постоянного назначения роли

По умолчанию новые пользователи имеют разрешение только на роль каталога. Если вы хотите сделать назначение роли постоянным, выполните следующие действия.

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. Щелкните **Роли каталога Azure AD**.

1. Нажмите кнопку **Участники**.

    ![Список участников](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Щелкните **разрешенную** роль, которую нужно сделать постоянной.

1. Щелкните **Дополнительно** и **Make perm** (Сделать постоянной).

    ![Настройка постоянного назначения роли](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Теперь роль отображается как **постоянная**.

    ![Список участников с постоянными ролями](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Удаление пользователя из роли

Пользователей можно удалять из назначений ролей, однако в системе всегда должен оставаться по крайней мере один пользователь с постоянной ролью глобального администратора. Если вам неизвестно, каким пользователям по-прежнему требуются назначенные им роли, можно [запустить проверку доступа для роли](pim-how-to-start-security-review.md).

Выполните следующие действия, чтобы удалить конкретного пользователя из роли каталога.

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. Щелкните **Роли каталога Azure AD**.

1. Нажмите кнопку **Участники**.

    ![Список участников](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Выберите назначение роли, которое вы хотите удалить.

1. Щелкните **Дополнительно** > **Удалить**.

    ![Удаление роли](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. В сообщении с запросом на подтверждение щелкните **Да**.

    ![Удаление роли](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Назначение роли удалено.

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
