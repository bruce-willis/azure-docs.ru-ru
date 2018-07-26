---
title: Отчеты о доступе и использовании для Azure MFA | Документация Майкрософт
description: Эта статья содержит информацию о том, как использовать функцию отчетов службы Многофакторной идентификации Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ead1c5a899057bb26154b45c75251e7d9e481147
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160898"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Отчеты в службе Многофакторной идентификации Azure

Служба "Многофакторная идентификация Azure" предоставляет несколько типов отчетов, которые могут быть полезны для вас и вашей организации. Просмотреть их можно на портале Azure. В следующей таблице перечислены доступные отчеты:

| Отчет | Расположение | ОПИСАНИЕ |
|:--- |:--- |:--- |
| Журнал заблокированных пользователей | "Azure AD > Сервер MFA > Блокировать или разблокировать пользователей" | История запросов на блокировку или разблокирование пользователей. |
| Предупреждения об использовании и о мошенничестве | "Azure AD > События входа" | Сведения об общем использовании, сводка пользователей, сведения о пользователях, а также история предупреждений о мошенничестве, отправленных в течение указанного диапазона дат. |
| Использование локальных компонентов | "Azure AD > Сервер MFA > Отчет о действиях" | Сведения об общем использовании MFA через расширение NPS, ADFS и сервер MFA. |
| Журнал обойденных пользователей | "Azure AD > Сервер MFA > Одноразовый обход проверки" | История запросов на обход многофакторной проверки подлинности для пользователя. |
| Состояние сервера | "Azure AD > Сервер MFA > Состояние сервера" | Сведения о состоянии серверов Многофакторной идентификации, с которыми связана ваша учетная запись. |

## <a name="view-reports"></a>Просмотр отчетов 

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левой части экрана выберите **Azure Active Directory** > **Сервер MFA**.
3. Выберите отчет, который нужно просмотреть.

   <center>![Облако](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Создание отчетов PowerShell

Определите пользователей, зарегистрированных для многофакторной проверки подлинности с помощью Powershell:

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Определите пользователей, не зарегистрированных для многофакторной проверки подлинности с помощью Powershell:

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Дополнительная информация

* [Для пользователей](../user-help/multi-factor-authentication-end-user.md)
* [Выберите для себя решение "Многофакторная идентификация Microsoft Azure"](concept-mfa-whichversion.md)
