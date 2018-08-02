---
title: Непредвиденный запрос на согласие при входе в приложение | Документы Майкрософт
description: Способы устранения неполадок, когда пользователь видит неожиданный запрос на согласие для приложения, которое вы интегрировали с Azure AD.
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: a5bf4de00073abb5edbbd5db74d8ee3ebce16913
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365337"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Непредвиденный запрос на согласие при входе в приложение

Для правильной работы многим приложениям, интегрирующимся с Azure Active Directory, нужны разрешения на доступ к разным ресурсам. Если эти ресурсы также интегрированы с Azure Active Directory, доступ к ним запрашивается с помощью инфраструктуры согласия Azure AD. 

Это приводит к отображению запроса на согласие при первом запуске приложения, что часто является единовременной операцией. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Сценарии, в которых пользователи видят запрос на согласие

Дополнительные запросы можно ожидать в разных сценариях:

* Изменился набор требуемых приложению разрешений.

* Пользователь, который изначально дал согласие для приложения, не являлся администратором, а теперь другой пользователь (без прав администратора) впервые открывает приложение.

* Пользователь, который изначально дал согласие для приложения, являлся администратором, однако он сделал это не от имени всей организации.

* Приложение использует [добавочное и динамическое согласие](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) для запроса дополнительных разрешений после изначального предоставления согласия. Такой подход часто применяется, когда расширенным функциям приложения нужны дополнительные разрешения сверх базового набора.

* Согласие было отозвано после первоначального предоставления.

* Разработчик настроил приложение для обязательного запроса на согласие при каждом использовании (обратите внимание, что такой подход не является рекомендуемым).

## <a name="next-steps"></a>Дополнительная информация

-   [Приложения, разрешения и согласие в Azure Active Directory (конечная точка версии 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Области, разрешения и согласие в Azure Active Directory (конечная точка версии 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


