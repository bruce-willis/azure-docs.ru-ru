---
title: Проверка подлинности пользователя Azure Active Directory
description: Как я могу как администратор Azure AD защитить проверку подлинности пользователя, одновременно снижая влияние конечного пользователя?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 0b232ed8bacfeb896fd5ee6ff9e2a58b71dc1517
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163002"
---
# <a name="what-methods-are-available-for-authentication"></a>Какие методы доступны для проверки подлинности?

Мы слышим новости о кражах паролей и компрометации удостоверений. Требование второго фактора помимо пароля сразу же повышает безопасность организации. Чтобы помочь администраторам защитить организации и пользователей с помощью дополнительных методов проверки подлинности, Microsoft Azure Active Directory (Azure AD) включает в себя такие функции, как Многофакторная идентификация Azure (Azure MFA) и самостоятельный сброс пароля Azure AD (SSPR).

Когда пользователю нужно получить доступ к конфиденциальному приложению, сбросить пароль или включить Windows Hello, может потребоваться дополнительная проверка того, что он действительно тот, кем он является.

Дополнительная проверка может проводиться с помощью таких методов:

* код, указанный в сообщении электронной почты или текстовом сообщении;
* телефонный звонок;
* уведомление или код по телефону;
* ответы на контрольные вопросы.

![Пример страницы входа на веб-сайте login.microsoftonline.com в Chrome](media/overview-authentication/overview-login.png)

Самостоятельный сброс пароля Azure MFA и Azure AD дает администраторам контроль над конфигурациями, политикой, мониторингом и созданием отчетов с помощью Azure AD и портала Azure для защиты организаций.

## <a name="self-service-password-reset"></a>Самостоятельный сброс пароля

Самостоятельный сброс пароля дает пользователям возможность сбрасывать пароль без вмешательства администратора, где и когда им нужно.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

Самостоятельный сброс пароля включает в себя следующее.

* **Изменение пароля.** Я знаю свой пароль, но хочу изменить его на новый.
* **Сброс пароля.** Я не могу войти и хочу сбросить свой пароль с помощью одного или нескольких утвержденных методов проверки подлинности.
* **Разблокирование учетной записи.** Я не могу войти, так как моя учетная запись заблокирована, и я хочу разблокировать ее с помощью одного или нескольких утвержденных методов проверки подлинности.

## <a name="multi-factor-authentication"></a>Многофакторная идентификация

Многофакторная идентификация (MFA) Azure — решение Майкрософт для двухэтапной проверки. С помощью утвержденных администратором методов проверки подлинности Azure MFA помогает защитить доступ к данным и приложениям, при этом не усложняя простой процесс входа в систему.

## <a name="next-steps"></a>Дополнительная информация

Следующий шаг — перейти к настройке самостоятельного сброса пароля и Многофакторной идентификации Azure.

См. раздел [Быстрое развертывание самостоятельного сброса пароля в Azure AD](quickstart-sspr.md).

Дополнительные сведения в статье [Руководство по самостоятельному сбросу пароля в Azure AD](concept-sspr-howitworks.md)

Подробнее в статье [Принципы работы службы Многофакторной идентификации Azure](concept-mfa-howitworks.md)