---
title: Устранение ошибок в API отчетов Azure Active Directory | Документы Майкрософт
description: Описание устранения ошибок, возникающих при вызове API отчетов Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a05cb2325dc193b9d96e5d454cb2d934aa926a02
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906210"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Устранение ошибок в API отчетов Azure Active Directory

В этой статье перечислены распространенные сообщения об ошибках, с которыми вы можете столкнуться при доступе к отчетам о действиях с помощью API MS Graph, а также меры по их устранению.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internal server error while accessing Microsoft Graph V2 endpoint (Внутренняя ошибка сервера 500 HTTP при обращении к конечной точке Microsoft Graph версии 2)

Конечная точка Microsoft Graph версии 2 сейчас не поддерживается, для доступа к журналам действий используйте конечную точку Microsoft Graph версии 1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Error: Failed to get user roles from AD Graph (Ошибка: не удалось получить роли пользователей из AD Graph)

Это сообщение об ошибке может появиться при попытке обращения ко входам с помощью песочницы Graph. Войдите в учетную запись, используя обе кнопки входа в пользовательском интерфейсе песочницы Graph, как показано на рисунке ниже. 

![Песочница Graph](./media/active-directory-reporting-troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Error: Failed to do premium license check from AD Graph (Ошибка: не удалось проверить лицензию уровня "Премиум" от AD Graph) 

Если при попытке обращения ко входам с помощью песочницы Graph появляется это сообщение об ошибке, выберите **Изменить разрешения** под своей учетной записью на левой панели навигации, а затем выберите **Tasks.ReadWrite** и **Directory.Read.All**. 

![Пользовательский интерфейс для изменения разрешений](./media/active-directory-reporting-troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error: Neither tenant is B2C or tenant doesn't have premium license (Ошибка: клиент не относится к типу B2C и у него нет лицензии уровня "Премиум")

Для доступа к отчетам о входе требуется лицензия Azure Active Directory Premium 1 (P1). Если вы видите это сообщение об ошибке при обращении ко входам, убедитесь, что клиент лицензируется по лицензии Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error: User is not in the allowed roles (Ошибка: пользователь не относится к разрешенным ролям) 

Если вы видите это сообщение об ошибке при обращении к журналам аудита или входам с помощью API, убедитесь, что ваша учетная запись является частью роли **Читатель безопасности** или **Читатель отчета** в клиенте Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: Application missing AAD 'Read directory data' permission (Ошибка: у приложения нет разрешения "Чтение данных каталога" AAD) 

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Error: Application missing MSGraph API 'Read all audit log data' permission (Ошибка: у приложения нет разрешения "Чтение всех данных журнала аудита" API MS Graph)

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

## <a name="next-steps"></a>Дальнейшие действия

[Справочник по использованию API аудита](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/directoryaudit)
[Справочник по использованию API отчетов о действиях при входе](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/signin)