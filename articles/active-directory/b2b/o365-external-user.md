---
title: Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B | Документация Майкрософт
description: В этом документе описано общее использование ресурсов с внешними партнерами с помощью Office 365 и службы совместной работы Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e0a65e4a5807cb9a6b39feecfd2d5b2643ea4a9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330743"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B

Внешний общий доступ в Office 365 (OneDrive, SharePoint Online, единые группы и т. д.) и служба совместной работы Azure Active Directory (Azure AD) B2B — это одно и то же с технической точки зрения. Все решения внешнего общего доступа (за исключением OneDrive и SharePoint Online), в том числе гости в группах Office 365, уже используют для общего доступа API приглашения службы совместной работы Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Чем использование Azure AD B2B отличается от внешнего общего доступа в SharePoint Online?

В OneDrive и SharePoint Online используется другой диспетчер приглашений. Поддержка внешнего общего доступа в OneDrive и SharePoint Online была реализована до того, как ее внедрили в Azure AD. Со временем функция внешнего общего доступа OneDrive и SharePoint Online обросла дополнительными компонентами, и миллионы пользователей используют встроенную модель общего доступа этого продукта. При этом существуют незначительные различия в принципах работы внешнего общего доступа OneDrive и SharePoint Online и службы совместной работы Azure AD B2B. См. дополнительные сведения о [внешнем доступе OneDrive и SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview). Процесс отличается от использования Azure AD B2B следующим образом:

- OneDrive и SharePoint Online добавляют пользователей в каталог после того, как пользователи активировали приглашения. Таким образом, до активации вы не видите пользователя на портале Azure AD. В то же время если пользователь приглашается из другого сайта, то создается новое приглашение. При использовании службы совместной работы Azure AD B2B пользователь добавляется непосредственно после отправки приглашения, поэтому он сразу везде отображается.

- Процесс активации приглашения в OneDrive и SharePoint Online отличается от аналогичной процедуры в службе совместной работы Azure AD B2B. После того как пользователь активирует приглашение, интерфейсы будут выглядеть одинаково.

- Пользователей, приглашенных в службу совместной работы Azure AD B2B, можно выбирать в диалоговых окнах общего доступа OneDrive и SharePoint Online. Приглашенные пользователи OneDrive и SharePoint Online также отображаются в Azure AD после того, как они активируют свои приглашения.

- Требования к лицензированию отличаются. В рамках каждой платной лицензии Azure AD можно разрешить доступ к платной функции Azure AD для пяти гостевых пользователей. См. дополнительные сведения о [лицензировании Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) и [правах внешнего пользователя при использовании внешнего общего доступа SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

Чтобы управлять внешним общим доступом в OneDrive и SharePoint Online с помощью службы совместной работы Azure AD B2B, задайте для параметра внешнего общего доступа OneDrive и SharePoint Online значение **Allow sharing only with the external users that already exist in your organization's directory** (Разрешить общий доступ только внешним пользователям, которые уже существуют в каталоге организации). Пользователи могут перейти на внешние сайты, предоставляющие общий доступ, и выбрать из внешних сотрудников, добавленных администратором. Администратор может добавлять внешних сотрудников с помощью интерфейсов API приглашения службы совместной работы B2B.


![Настройка внешнего общего доступа в OneDrive и SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Дополнительная информация

* [Что такое служба совместной работы Azure AD B2B?](what-is-b2b.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](use-dynamic-groups.md)