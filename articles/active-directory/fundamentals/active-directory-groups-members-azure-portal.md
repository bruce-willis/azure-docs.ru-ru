---
title: Управление участниками группы в Azure Active Directory | Документация Майкрософт
description: Сведения о том, как добавлять или удалять пользователей и устройства, которые входят в группу в Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 947b0c11aba211530e3ae25d6617079bcaf2995f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860493"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Управление участниками групп в клиенте Azure Active Directory
В этой статье объясняется, как управлять участниками группы в Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Как можно найти участников и управлять ими?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Все службы**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все группы**.

   ![Открытие колонки группы](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Выберите группу в колонке **Пользователи и группы — Все группы** .
5. В колонке **Группа — *имя_группы*** выберите **Участники**.

   ![Открытие колонки "Участники"](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Чтобы добавить участников в группу, в колонке **Группа — Участники** щелкните **Добавить участников**.

   ![Команда "Добавить участников"](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. В колонке **Участники** выберите одного или несколько пользователей или устройств для добавления в группу, а затем нажмите кнопку **Выбрать**, расположенную в нижней части колонки, чтобы добавить их в группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.
8. Чтобы удалить участника из группы, выберите его в колонке **Группы — Участники** .
9. В колонке ***имя_участника*** выберите команду **Удалить** и подтвердите свой выбор при появлении соответствующего запроса.

   ![Команда "Удалить участников"](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Завершив изменение состава группы, щелкните **Сохранить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](../users-groups-roles/groups-dynamic-membership.md)
