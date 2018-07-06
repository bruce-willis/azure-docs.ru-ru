---
title: Как добавить имеющуюся подписку Azure в каталог Azure AD | Документация Майкрософт
description: Как добавить имеющуюся подписку в каталог Azure AD
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: bcae3f51e2245928c8110d06f3514177d57ac883
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449216"
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Как связать или добавить подписку Azure в Azure Active Directory

Из этой статьи вы узнаете о связи между подпиской Azure и службой Azure Active Directory (Azure AD) и о том, как добавить существующую подписку в каталог Azure AD. Подписка Azure связана с Azure AD отношением доверия. Это означает, что подписка доверяет каталогу проверку подлинности пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. 

Это отношение доверия между подпиской и каталогом отличается от связи подписки с другими ресурсами в Azure (веб-сайтами, базами данных и т. д.). Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Но каталог Azure AD остается в Azure, и вы можете связать другую подписку с этим каталогом и управлять им, используя новую подписку.

Все пользователи относятся к единому домашнему каталогу, который проверяет их подлинность. Они также могут быть гостями в других каталогах. В Azure AD отображаются только каталоги, участником или гостем которых вы являетесь.

## <a name="before-you-begin"></a>Перед началом работы

* Необходимо войти в систему, используя учетную запись с правами владельца RBAC на доступ к подписке.
* Для входа необходимо использовать учетную запись, которая существует в текущем каталоге, связанном с подпиской, и в каталоге, в который необходимо добавить подписку. Дополнительные сведения о получении доступа к другому каталогу см. в статье [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](../b2b/add-users-administrator.md)
* Эта функция недоступна для подписок CSP (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) и Microsoft Imagine (MS-AZR-0144P).

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Привязка имеющейся подписки к каталогу Azure AD

1. Войдите в подписку и выберите ее на [странице подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Щелкните **Сменить каталог**.

    ![Снимок экрана, показывающий кнопку "Сменить каталог"](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Просмотрите предупреждения. Все пользователи [управления доступом на основе ролей (RBAC)](../../role-based-access-control/role-assignments-portal.md) с назначенными правами доступа и все администраторы подписки потеряют доступ, если изменить каталог подписки.
4. Выберите каталог.

    ![Снимок экрана, показывающий элемент интерфейса "Сменить каталог"](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Щелкните **Изменить**.
6. Готово! Используйте переключатель каталогов, чтобы перейти в новый каталог. Отображение всех элементов может занять до 10 минут.

    ![Снимок экрана, показывающий уведомление о выполнении смены каталога](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Снимок экрана, показывающий переключатель](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


Перемещение подписки затрагивает также все имеющиеся хранилища ключей Azure, поэтому необходимо [изменить идентификатор клиента хранилища ключей](../../key-vault/key-vault-subscription-move-fix.md) перед возобновлением операций.

Изменение каталога подписки — это операция, выполняющаяся на уровне обслуживания. Она не влияет на права владения выставлением счетов по подписке, а администратор учетной записи по-прежнему может менять администратора службы в [Центре управления учетной записью](https://account.azure.com/subscriptions). Если необходимо удалить исходный каталог, передайте права владения выставлением счетов по подписке новому администратору учетной записи. Сведения о передаче прав владения выставлением счетов см. в статье [Передача прав владения подпиской Azure другой учетной записи](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о создании каталога Azure AD бесплатно см. в статье [Как получить клиент Azure Active Directory](../develop/active-directory-howto-tenant.md).
* Сведения о передаче прав владения выставлением счетов по подписке Azure см. в статье [Передача прав владения подпиской Azure другой учетной записи](../../billing/billing-subscription-transfer.md).
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения о назначении ролей в Azure AD см. в статье [Назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
