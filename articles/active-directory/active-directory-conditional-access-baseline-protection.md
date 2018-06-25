---
title: Что такое базовая защита в условном доступе Azure Active Directory | Документация Майкрософт
description: Узнайте, как базовая защита обеспечивает наличие по крайней мере базового уровня безопасности в среде.
services: active-directory
keywords: условный доступ к приложениям, условный доступ посредством Azure Active Directory, безопасный доступ к ресурсам организации, политики условного доступа
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248968"
---
# <a name="what-is-baseline-protection"></a>Что такое базовая защита  

За последний год количество атак на удостоверения увеличилось на 300 %. Чтобы защитить среду от все учащающихся атак, Azure Active Directory (Azure AD) представляет новый компонент, который называется базовой защитой. Базовая защита — это набор предопределенных политик условного доступа. Цель этих политик — обеспечить наличие по крайней мере базового уровня безопасности в среде. 

На этапе предварительной версии необходимо включить базовые политики, если вы хотите активировать их. В общедоступной версии эти политики будут включены по умолчанию. 

Первая базовая политика защиты предусматривает выполнение MFA для привилегированных учетных записей. Злоумышленники, получившие доступ к привилегированным учетным записям, могут причинить значительный ущерб, поэтому критически важно защитить эти учетные записи в первую очередь. Эта политика охватывает такие привилегированные роли: 

- Глобальный администратор.  

- администратор SharePoint;  

- администратор Exchange;  

- администратор условного доступа;  

- администратор безопасности;  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Как приступить к работе 

Чтобы включить базовую политику:  

1. Войдите на [портал Azure](https://portal.azure.com) в качестве глобального администратора, администратора безопасности или администратора условного доступа.

2. На **портале Azure** на панели навигации слева щелкните **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![Условный доступ](./media/active-directory-conditional-access-baseline-protection/03.png)

4. В списке политик щелкните **Baseline policy: Require MFA for admins (Preview)** (Базовая политика. Требовать MFA для администраторов (предварительная версия)). 

5. Чтобы включить политику, выберите **Use policy immediately** (Использовать политику немедленно).

6. Выберите команду **Сохранить**. 
 

Базовая политика предоставляет возможность исключать пользователей и группы. Вы можете исключить одну *[административную учетную запись аварийного доступа](active-directory-admin-manage-emergency-access-accounts.md)*, чтобы не заблокировать клиент для себя.
  
 

## <a name="what-you-should-know"></a>Необходимая информация 

Роли каталога, которые включены в базовую политику, являются наиболее привилегированными ролями Azure AD. На основе отзывов клиентов мы можем добавить другие роли в будущем. 

Если у вас есть учетные записи с правами администратора в сценариях, вместо них следует использовать [управляемые удостоверения службы (MSI)](managed-service-identity/overview.md) или [субъекты-службы (с сертификатами)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). Для временного решения проблемы можно исключить отдельные учетные записи пользователей из базовой политики. 

Политика применяется к устаревшим потокам аутентификации, таким как POP, IMAP, старому настольному клиенту Office. 

## <a name="next-steps"></a>Дополнительная информация

Если вы хотите узнать, как настроить политику условного доступа, см. статью о том, как [начать работу с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md). 
