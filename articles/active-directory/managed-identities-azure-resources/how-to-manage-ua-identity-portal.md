---
title: Как управлять удостоверением, назначаемым пользователем, используя портал Azure
description: Пошаговые инструкции по созданию, получению списка и удалению ролей, а также их назначению для управляемого удостоверения, назначаемого пользователем.
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
ms.openlocfilehash: bdbe15a85ad4d2ef6918b7ab7e16942edde5096e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220342"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Создание, получение списка, удаление ролей и их назначение для управляемого удостоверения, назначаемого пользователем, с помощью портала Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

Из этой статьи вы узнаете, как создавать, удалять роли, получать список ролей и назначать их для управляемого удостоверения, назначаемого пользователем, с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (получения списка) и удаления управляемых удостоверений, назначаемых пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для чтения (получения списка) свойств управляемого удостоверения, назначаемого пользователем.

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, чтобы создать управляемое удостоверение, назначаемое пользователем.
2. В поле поиска введите *Управляемые удостоверения*, а затем в разделе **Службы** щелкните **Управляемые удостоверения**.
3. Нажмите кнопку **Добавить** и введите значения в следующие поля в области **Создание назначаемого пользователем управляемого** удостоверения:
   - **Имя ресурса**: это имя управляемого удостоверения, назначаемого пользователем, например UAI1.
   - **Подписка**: выберите подписку, чтобы создать управляемое удостоверение, назначаемое пользователем.
   - **Группа ресурсов**: создайте новую группу ресурсов, которая содержит управляемое удостоверение, назначаемое пользователем, или выберите **Использовать существующий** для создания управляемого удостоверения, назначаемого пользователем, в существующей группе ресурсов.
   - **Расположение**: выберите расположение для развертывания управляемого удостоверения, назначаемого пользователем, например **Западная часть США**.
4. Нажмите кнопку **Создать**.

![Создание управляемого удостоверения, назначаемого пользователем](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, чтобы получить список управляемых удостоверений, назначаемых пользователем.
2. В поле поиска введите *Управляемые удостоверения*, а затем в разделе "Службы" щелкните **Управляемые удостоверения**.
3. Возвращается список управляемых удостоверений, назначаемых пользователем, для вашей подписки.  Чтобы просмотреть сведения об управляемом удостоверении, назначаемом пользователем, щелкните его имя.

![Отображение управляемого удостоверения, назначаемого пользователем](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, чтобы удалить управляемое удостоверение, назначаемое пользователем.
2. Выберите управляемое удостоверение, назначаемое пользователем, и нажмите кнопку **Удалить**.
3. В окне подтверждения выберите **Да**.

![Удаление управляемого удостоверения, назначаемого пользователем](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Назначение роли для управляемого удостоверения, назначаемого пользователем 

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, чтобы получить список управляемых удостоверений, назначаемых пользователем.
2. В поле поиска введите *Управляемые удостоверения*, а затем в разделе "Службы" щелкните **Управляемые удостоверения**.
3. Возвращается список управляемых удостоверений, назначаемых пользователем, для вашей подписки.  Выберите назначаемое пользователем управляемое удостоверение, которому необходимо назначить роль.
4. Выберите **Управление доступом (IAM)** и щелкните **Добавить**.

   ![Начало работы с управляемым удостоверением, назначаемым пользователем](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. В колонке "Добавление разрешений" задайте следующие значения и нажмите кнопку **Сохранить**:
   - **Роль** — роль для назначения.
   - **Назначение доступа к** — ресурс, который присваивается управляемому удостоверению, назначаемому пользователем.
   - **Выбор** — участник, которому требуется назначить доступ.
   
   ![Управляемое удостоверение, назначаемое пользователем (IAM)](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  