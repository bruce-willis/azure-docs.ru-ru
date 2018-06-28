---
title: Выбор разрешений для заданного API | Документы Майкрософт
description: Здесь описано, как найти конечные точки проверки подлинности для пользовательского приложения, которое вы разрабатываете или регистрируете в Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 203ecc1dd83bebffadf0e85a08cb3ee383b0d412
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332013"
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
