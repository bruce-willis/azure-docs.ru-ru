---
title: Настройка политики регистрации с многофакторной проверкой подлинности в службе "Защита идентификации Azure Active Directory" | Документация Майкрософт
description: Сведения о настройке политики регистрации с многофакторной проверкой подлинности в службе "Защита идентификации Azure Active Directory".
services: active-directory
keywords: защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 792a1fc2403e672c973577efd7a05c9c81d45ad4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054087"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Практическое руководство. Настройка политики регистрации с многофакторной проверкой подлинности

Служба "Защита идентификации Azure AD" облегчает управление развертыванием регистрации с многофакторной проверкой подлинности (MFA) за счет настройки политики. В этой статье объясняется, что можно сделать с помощью этой политики и как ее настроить.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Что такое политика регистрации с многофакторной проверкой подлинности?

Многофакторная идентификация Azure — это метод проверки идентичности пользователя, при котором используются дополнительные средства, а не только имя пользователя и пароль. Это второй уровень безопасности, который применяется для входа пользователя в систему и выполнения транзакций.  

Мы советуем настроить обязательную Многофакторную идентификацию Azure при входе пользователей, так как она:

- обеспечивает строгую аутентификацию с помощью ряда простых параметров проверки;

- играет ключевую роль при подготовке защиты от компрометации, а также восстановления скомпрометированных учетных записей в организации.


Дополнительные сведения см. в статье [Что такое Многофакторная идентификация Azure?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Как найти политику регистрации с многофакторной проверкой подлинности?
   
Политика регистрации с многофакторной проверкой подлинности указывается в разделе **Настройка** страницы [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Политика MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Параметры политики

При настройке политики управления рисками при входе необходимо указать следующее.

- Пользователей и группы, к которым применяется политика:

    ![Пользователи и группы](./media/howto-mfa-policy/11.png)

- Тип доступа, который нужно применить:  

    ![Пользователи и группы](./media/howto-mfa-policy/12.png)

- Состояние политики:

    ![Принудительное применение политики](./media/howto-mfa-policy/14.png)


Диалоговое окно настройки политики дает возможность оценить влияние настроенной конфигурации.

![Оценка влияния](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Возможности для пользователя


Общие сведения о соответствующем взаимодействии с пользователями см. в статьях:

* [Регистрация с многофакторной проверкой подлинности](flows.md#multi-factor-authentication-registration).  
* [Процедуры входа с защитой идентификации Azure AD](flows.md).  



## <a name="next-steps"></a>Дополнительная информация

Общие сведения о службе "Защита идентификации Azure Active Directory" см. в [этом обзоре](overview.md).
