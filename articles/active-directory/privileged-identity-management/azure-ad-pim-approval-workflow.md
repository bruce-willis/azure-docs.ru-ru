---
title: Подтверждение или отклонение запросов на роли каталога Azure AD в PIM | Документация Майкрософт
description: Узнайте, как подтвердить или отклонить запросы на роли каталога Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189164"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Подтверждение или отклонение запросов на роли каталога Azure AD в PIM

С помощью управления привилегированными пользователями можно настроить роли, чтобы требовать утверждения при активации, а также выбрать одного или несколько пользователей или группы в качестве делегированных утверждающих лиц.

## <a name="view-pending-approvals-requests"></a>Просмотр запросов, ожидающих утверждения

При наличии запроса, ожидающего вашего утверждения, вы, как делегированное утверждающее лицо, получите уведомление по электронной почте. Чтобы просмотреть эти запросы на портале PIM, на панели мониторинга (в новом меню навигации) выберите вкладку "Ожидающие утверждения запросы" на левой панели навигации.

![](media/azure-ad-pim-approval-workflow/image023.png)

Отобразится список запросов, ожидающих утверждения:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Подтверждение или отклонение запросов на повышение роли (по одному и (или) массово)

Выберите запросы, которые необходимо утвердить или отклонить, а затем нажмите кнопку на панели действий, которая соответствует вашему решению:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Указание обоснования для своего утверждения или отказа

Откроется новая колонка, позволяющая утвердить или отклонить несколько запросов одновременно. Введите обоснование своего решения и щелкните "Утвердить" (или "Отклонить") в нижней части колонки:

![](media/azure-ad-pim-approval-workflow/image029.png)

Когда запрос обработан, значок состояния отразит принятое решение (в данном примере — "Утвердить"):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Дополнительная информация

- [Подтверждение или отклонение запросов для ролей ресурсов Azure в PIM](pim-resource-roles-approval-workflow.md)
- [Уведомления по электронной почте в PIM](pim-email-notifications.md)
