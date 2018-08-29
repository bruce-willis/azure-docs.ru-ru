---
title: Начало работы с API отчетов Azure AD | Документация Майкрософт
description: Как начать работу с API отчетов Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7de7c87e5cf1747f4899f33d9e6b9cbf0bb430e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146038"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Начало работы с API отчетов Azure Active Directory

Azure Active Directory предоставляет разнообразные [отчеты](overview-reports.md). Данные этих отчетов могут быть очень полезными для приложений, например систем SIEM, а также инструментов аудита и бизнес-аналитики. 

С помощью интерфейсов API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

Эта статья предоставляет инструкции по получению доступа к данным отчетов с использованием соответствующих API.

При возникновении проблем ознакомьтесь со статьей [Как получить поддержку для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Предварительные требования

Чтобы получить доступ к API отчетов, даже если вы планируете получить доступ к API, используя сценарий, необходимо следующее:

1. Назначение ролей ("Читатель безопасности", "Администратор безопасности", "Глобальный администратор").
2. Регистрация приложения
3. предоставьте разрешения;
4. Сбор параметров конфигурации

Подробные инструкции см. в [предварительных требованиях для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md).

## <a name="apis-with-graph-explorer"></a>Использование API с песочницей Graph

Вы можете использовать [песочницу MSGraph](https://developer.microsoft.com/graph/graph-explorer) для проверки входа и аудита данных API. Войдите в учетную запись, используя одну из кнопок входа в пользовательском интерфейсе песочницы Graph и задав для клиента разрешения **AuditLog.Read.All** и **Directory.ReadAll**, как показано ниже.   

![Песочница Graph](./media/concept-reporting-api/graph-explorer.png)

![Пользовательский интерфейс для изменения разрешений](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Использование сертификатов для доступа к API отчетов Azure AD 

Если вы планируете получать данные отчетов без вмешательства пользователя, используйте API отчетов Azure AD с сертификатами.

Подробные инструкции см. в статье о [получении данных с помощью API отчетов Azure AD с сертификатами](tutorial-access-api-with-certificates.md).


## <a name="next-steps"></a>Дополнительная информация

 * [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Устранение ошибок в API отчетов Azure Active Directory](troubleshoot-graph-api.md)


