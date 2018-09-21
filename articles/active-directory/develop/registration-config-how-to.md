---
title: Выбор разрешений для заданного API | Документы Майкрософт
description: Здесь описано, как найти конечные точки проверки подлинности для пользовательского приложения, которое вы разрабатываете или регистрируете в Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: f6c2540d8f0bb49491a428b085d20067a36d970a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723925"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Выбор разрешений для заданного API

Конечные точки проверки подлинности для приложения можно найти на [портале Azure](https://portal.azure.com).

-   Перейдите на [портал Azure](https://portal.azure.com).

-   В области навигации слева щелкните **Azure Active Directory**.

-   Щелкните **Регистрация приложений** и выберите **Конечные точки**.

-   Откроется страница **Конечные точки** со списком всех конечных точек проверки подлинности для клиента.

-   Используйте соответствующую конечную точку для протокола аутентификации в сочетании с идентификатором приложения, чтобы создать запрос на аутентификацию для своего приложения.

## <a name="next-steps"></a>Дополнительная информация
[Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
