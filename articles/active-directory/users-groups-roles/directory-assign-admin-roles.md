---
title: Назначение ролей администратора в Azure Active Directory | Документация Майкрософт
description: Роль администратора позволяет создавать или изменять пользователей, назначать административные роли, сбрасывать пароли пользователей, управлять доменами и пользовательскими лицензиями. Пользователь, которому назначена роль администратора, имеет одни и те же разрешения во всех облачных службах, на которые подписана ваша организация.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/27/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9b56f540af2b8d35258a4db79502c9edf83cdb45
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128472"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Назначение ролей администратора в Azure Active Directory

С помощью Azure Active Directory (Azure AD) можно назначить несколько администраторов, которые будут выполнять различные функции. На портале Azure AD можно назначить администраторов для выполнения задач, таких как добавление или изменение пользователей, назначение административных ролей, сброс паролей пользователей, управление лицензиями пользователей и управление доменными именами.

## <a name="details-about-the-global-administrator-role"></a>Сведения о роли глобального администратора

Глобальный администратор имеет доступ ко всем административным функциям. По умолчанию роль глобального администратора каталога назначается пользователю, зарегистрировавшему подписку Azure. Только глобальные администраторы могут назначать другие административные роли.

## <a name="assign-or-remove-administrator-roles"></a>Назначение и удаление ролей администратора

Чтобы узнать, как назначать пользователям роли администратора в Azure Active Directory, ознакомьтесь со статьей [Назначение пользователю ролей администратора в Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Доступные роли

Доступны следующие роли администратора.

* **[Администратор приложения](#application-administrator)**: пользователи с этой ролью могут создавать и контролировать все аспекты корпоративных приложений, регистрации приложений, а также параметры прокси приложения. Кроме того, эта роль позволяет соглашаться на делегированные разрешения и разрешения приложений, за исключением Microsoft Graph и Azure AD Graph. Члены этой роли не добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Разработчик приложения](#application-developer)**: пользователи с этой ролью могут создавать регистрации приложений, когда для параметра "Пользователи могут регистрировать приложения" задано значение "Нет". Кроме того, эта роль позволяет членам давать согласие от своего имени, когда для параметра "Пользователи могут разрешать приложениям доступ к корпоративным данным от своего имени" задано значение "Нет". Члены этой роли добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Администратор выставления счетов](#billing-administrator)**: совершает покупки, управляет подписками и запросами в службу поддержки, а также отслеживает работоспособность службы.

* **[Администратор облачных приложений](#cloud-application-administrator)**: пользователи с этой ролью имеют те же разрешения, что и для роли администратора приложений, за исключением возможности управления прокси приложения. Эта роль позволяет создавать и контролировать все аспекты корпоративных приложений и регистраций приложений. Кроме того, эта роль позволяет соглашаться на делегированные разрешения и разрешения приложений, за исключением Microsoft Graph и Azure AD Graph. Члены этой роли не добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Администратор соответствия требованиям](#compliance-administrator)**: пользователи с этой ролью имеют разрешения на управление в центре безопасности и соответствия требованиям Office 365 и центре администрирования Exchange. Дополнительные сведения см. в статье [Роли администраторов в Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Администратор условного доступа](#conditional-access-administrator)**: у пользователей с этой ролью есть возможность управления параметрами условного доступа Azure Active Directory.
  > [!NOTE]
  > Чтобы развернуть политику условного доступа Exchange ActiveSync в Azure, у пользователя должны быть права глобального администратора.
  
* **[Администраторы устройств](#device-administrators)**: эту роль можно назначить только в качестве роли дополнительного локального администратора в [параметрах устройства](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Пользователи с этой ролью становятся администраторами локальных компьютеров на всех устройствах с Windows 10, присоединенных к Azure Active Directory. Они не могут управлять объектами устройств в Azure Active Directory. 

* **[Читатели каталогов](#directory-readers)**: это устаревшая роль, которая будет назначаться приложениям, не поддерживающим [платформу предоставления разрешений](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ее не следует назначать пользователям.

* **[Учетные записи для синхронизации службы каталогов](#directory-synchronization-accounts)**: не используйте. Эта роль автоматически назначается службе Azure AD Connect, она не предназначена для любого другого использования.

* **[Писатели в каталоги](#directory-writers)**: это устаревшая роль, которая будет назначаться приложениям, не поддерживающим [платформу предоставления разрешений](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ее не следует назначать пользователям.

* **[Администратор службы Dynamics 365 или администратор служб CRM:](#dynamics-365-service-administrator)** пользователи с этой ролью имеют глобальные разрешения в Microsoft Dynamics 365 Online при наличии этой службы, а также возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Дополнительные сведения см. в статье [Использование роли администратора службы для управления клиентом](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Администратор службы Exchange](#exchange-service-administrator)**: у пользователей с этой ролью есть глобальные разрешения в Microsoft Exchange Online при наличии этой службы. Дополнительные сведения см. в статье [Роли администраторов в Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Глобальный администратор или администратор организации](#company-administrator)**: пользователи с этой ролью имеют доступ ко всем административным функциям в Azure Active Directory, а также к службам, использующим идентификаторы Azure Active Directory, например Exchange Online, SharePoint Online и Skype для бизнеса Online. Пользователь, зарегистрировавший клиент Azure Active Directory, становится глобальным администратором. Только глобальные администраторы могут назначать другие административные роли. В компании может быть несколько глобальных администраторов. Глобальные администраторы могут сбросить пароль любого пользователя и администратора.

  > [!NOTE]
  > В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор организации". На [портале Azure](https://portal.azure.com)это "Глобальный администратор".
  >
  >

* **[Лицо, приглашающее гостей](#guest-inviter)**: пользователи с этой ролью могут управлять приглашениями гостевых пользователей Azure Active Directory B2B, если для параметра пользователя **Участники могут приглашать других пользователей** установлено значение "Нет". Дополнительные сведения о службе совместной работы Azure AD B2B см. в [этой статье](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). В нее не входят какие-либо другие разрешения.

* **[Администратор Information Protection](#information-protection-administrator)**: эта роль предусматривает наличие всех разрешений в службе Azure Information Protection. Эта роль позволяет настраивать метки для политики Azure Information Protection, управлять шаблонами защиты и активировать защиту. Эта роль не предоставляет разрешений в центре защиты идентификации, службе управления привилегированными пользователями, службе отслеживания работоспособности служб Office 365, а также Центре безопасности и соответствия требованиям Office 365.

* **[Администратор службы Intune](#intune-service-administrator)**: у пользователей с этой ролью есть глобальные разрешения в Microsoft Intune Online при наличии этой службы. Кроме того, администраторы этой роли могут управлять пользователями и устройствами, чтобы связать политику, а также создавать группы и управлять ими. Дополнительные сведения см. в статье [Управление доступом на основе ролей (RBAC) с помощью Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

* **[Администратор лицензий](#license-administrator)**. Пользователи с этой ролью могут добавлять, удалять и изменять назначенные лицензии для пользователей и групп (с использованием группового лицензирования), а также управлять расположением использования для пользователей. Роль не предоставляет возможности управления подписками или их приобретения, создания групп или управления ими, или создания и управления пользователями за пределами расположения использования.

* **[Читатель Центра сообщений](#message-center-reader)**: пользователи с этой ролью могут отслеживать уведомления и рекомендации по обновлениям работоспособности в [Центре сообщений Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) для своей организации в настроенных службах, таких как Exchange, Intune и Microsoft Teams. Читатели Центра сообщений еженедельно получают по электронной почте хэш-коды публикаций, обновлений и могут размещать общедоступные публикации в центре сообщений в Office 365. В Azure AD у пользователей с этой ролью будет только доступ на чтение данных в службах Azure AD, например данных пользователей и групп. 

* **[Поддержка партнеров уровня 1](#partner-tier1-support)**: не используйте. Она больше не поддерживается и будет удалена из Azure AD в будущем. Эта роль использовалась небольшим числом торговых представителей корпорации Майкрософт и не предназначена для общего использования.

* **[Поддержка партнеров уровня 2](#partner-tier2-support)**: не используйте. Она больше не поддерживается и будет удалена из Azure AD в будущем. Эта роль использовалась небольшим числом торговых представителей корпорации Майкрософт и не предназначена для общего использования.

* **[Администратор паролей или администратор службы поддержки](#helpdesk-administrator)**: пользователи с этой ролью могут изменять пароли, управлять запросами на обслуживание и отслеживать работоспособность служб. Администраторы службы поддержки могут сбрасывать пароли только для непривилегированных пользователей и других администраторов службы поддержки. 

  > [!NOTE]
  > В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор службы поддержки". На [портале Azure](https://portal.azure.com/) это "Администратор паролей".
  >
  >
  
* **[Администратор службы Power BI](#power-bi-service-administrator)**: пользователи с этой ролью имеют глобальные разрешения в Microsoft Power BI при наличии этой службы, а также возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Дополнительные сведения см. в статье [Основные сведения о роли администратора Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Администратор привилегированных ролей](#privileged-role-administrator)**: пользователи с этой ролью могут управлять назначением ролей в Azure Active Directory, а также в Azure AD Privileged Identity Management. Кроме того, эта роль позволяет управлять всеми аспектами управления привилегированными пользователями.

* **[Читатель отчетов](#reports-reader)**: пользователи с этой ролью могут просматривать отчеты об использовании и панель мониторинга с отчетами в центре администрирования Office 365, а также пакет контекста внедрения в PowerBI. Кроме того, эта роль предоставляет доступ к отчетам о входах, сведениям о действиях в Azure AD и данным от API отчетов Microsoft Graph. Пользователь с ролью "Читатель отчетов" имеет доступ только к релевантным метрикам использования и внедрения. Он не приобретает полномочия администратора по настройке или использованию центров администрирования отдельных продуктов (например, Excahange). 

* **[Администратор безопасности](#security-administrator)**: пользователи с этой ролью имеют все разрешения только на чтение, доступные для роли "Чтение данных безопасности", а также возможность управлять конфигурацией служб, связанных с безопасностью, таких как защита идентификации Azure Active Directory, Azure Information Protection, управление привилегированными пользователями и Центр безопасности и соответствия требованиям Office 365. Дополнительные сведения о разрешениях в Office 365 см. в статье [Разрешения в Центре безопасности и соответствия требованиям Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | В | Может |
  | --- | --- |
  | Центр защиты идентификации |<ul><li>Все разрешения роли читателя безопасности.<li>А также возможность выполнять все операции центра защиты идентификации кроме сброса паролей. |
  | Управление привилегированными пользователями (PIM) |<ul><li>Все разрешения роли читателя безопасности.<li>**Не может** управлять принадлежностью к ролям или параметрами ролей Azure AD. |
  | <p>Отслеживание работоспособности службы Office 365</p><p>Центр безопасности и соответствия требованиям Office 365 |<ul><li>Все разрешения роли читателя безопасности.<li>Может настраивать все параметры функции "Дополнительная защита от угроз" (защита от вредоносных программ и вирусов, защита от вредоносных URL-адресов, трассировка URL-адресов и т. д.). |
  
* **[Чтение данных безопасности](#security-reader)**: пользователи с этой ролью имеют глобальный доступ только на чтение, в том числе сведений в Azure Active Directory, службе защиты идентификации, управления привилегированными пользователями, а также имеют возможность чтения отчетов о входе в Azure Active Directory и журналов аудита. Эта роль также предоставляет разрешения только на чтение в Центре безопасности и соответствия требованиям Office 365. Дополнительные сведения о разрешениях в Office 365 см. в статье [Разрешения в Центре безопасности и соответствия требованиям Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | В | Может |
  | --- | --- |
  | Центр защиты идентификации |Чтение всех отчетов о безопасности и сведений о параметрах функций безопасности<ul><li>Защита от нежелательной почты<li>Шифрование<li>Защита от потери данных<li>Защита от вредоносных программ<li>Дополнительная защита от угроз<li>Защита от фишинга<li>Правила обработки почты |
  | Управление привилегированными пользователями (PIM) |<p>Имеет доступ только для чтения ко всем сведениям, отображаемым в службе управления привилегированными пользователями (PIM) Azure AD: политики и отчеты для назначения ролей Azure AD, проверки безопасности, а в будущем также доступ на чтение для данных политик и отчетов, относящихся к сценариям за пределами назначения ролей Azure AD.<p>**Не может** регистрироваться в службе управления привилегированными пользователями Azure AD или вносить в нее какие-либо изменения. Используя портал службы управления привилегированными пользователями или PowerShell, кто-то в этой роли может активировать дополнительные роли (например, глобальный администратор или администратор привилегированных ролей), если пользователь является кандидатом на них. |
  | <p>Отслеживание работоспособности службы Office 365</p><p>Центр безопасности и соответствия требованиям Office 365</p> |<ul><li>Чтение оповещений и управление ими<li>Чтение политик безопасности<li>Чтение данных аналитики угроз, Cloud App Discovery и карантин во время поиска и изучения<li>Чтение всех отчетов |

* **[Администратор службы поддержки](#service-support-administrator)**: пользователи с этой ролью могут открывать запросы в службу поддержки Майкрософт для служб Azure и Office 365, а также просматривать панели мониторинга службы и сообщения из центра сообщений на портале Azure и портале администрирования Office 365. Дополнительные сведения см. в статье [Роли администраторов в Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Администратор службы SharePoint](#sharepoint-service-administrator)**: пользователи с этой ролью имеют глобальные разрешения в Microsoft SharePoint Online при наличии этой службы, а также возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Дополнительные сведения см. в статье [Роли администраторов в Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Администратор службы Skype для бизнеса или администратор службы Lync](#lync-service-administrator)**: у пользователей с этой ролью есть глобальные разрешения в Microsoft Skype для бизнеса при наличии этой службы, а также возможность управлять определенными атрибутами пользователя Skype в Azure Active Directory. Кроме того, эта роль предоставляет возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Дополнительные сведения см. в статье [Роль администратора Skype для бизнеса](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор службы Lync". На [портале Azure](https://portal.azure.com/) это "Администратор службы Skype для бизнеса".
  >
  >

* **[Администратор учетных записей пользователей](#user-account-administrator)**: пользователи с этой ролью могут создавать аспекты пользователей и групп, а также управлять ими. Кроме того, эта роль включает в себя возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Существуют некоторые ограничения. Например, эта роль не позволяет удалить глобального администратора. Администраторы учетных записей могут изменять пароли только для непривилегированных пользователей, администраторов службы технической поддержки и администраторов учетных записей.

| Может | Не может |
| --- | --- |
| <p>Просмотр сведений о компании и пользователях</p><p>Управление запросами в службу поддержки Office</p><p>Изменять пароли только для непривилегированных пользователей, администраторов службы технической поддержки и администраторов учетных записей</p><p>Создание представлений пользователя и управление ими</p><p>Создавать, изменять, удалять пользователей и группы, а также управлять пользовательскими лицензиями без ограничений. Администраторы не могут удалить глобального администратора или создать других администраторов.</p> |<p>Выполнение операций выставления счетов и приобретения продуктов Office</p><p>Управление доменами</p><p>Управление информацией о компании</p><p>Делегирование административных ролей другим пользователям</p><p>Использование синхронизации каталогов</p><p>Включение или отключение Многофакторной идентификации</p><p>Просмотр журналов аудита</p> |

## <a name="deprecated-roles"></a>Устаревшие роли

Не рекомендуется использовать следующие роли. Они больше не поддерживаются и будут удалены из Azure AD в будущем.

* Администратор отдельных лицензий
* Device Join (Присоединение устройства)
* Диспетчеры устройств
* Device Users (Пользователи устройства)
* Создатель проверенного пользователя электронной почты
* администратор почтовых ящиков;
* Присоединение устройства к рабочей области


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Добавление коллеги в качестве глобального администратора

1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) с помощью учетной записи глобального администратора или администратора привилегированной роли каталога клиента.

   ![Открытие Центра администрирования Azure AD](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Выберите **Пользователи**.

3. Найдите пользователя, которого требуется назначить глобальным администратором, и откройте колонку этого пользователя.

4. В колонке пользователя щелкните **Роль каталога**.
 
5. В колонке роли каталога выберите роль **Глобальный администратор** и сохраните изменения.

## <a name="detailed-azure-active-directory-permissions"></a>Детальные разрешения в Azure Active Directory
В следующих таблицах описываются разрешения в Azure Active Directory, предоставленные каждой роли. Некоторые роли, такие как роль глобального администратора, могут иметь дополнительные разрешения в службах Майкрософт за пределами Azure Active Directory.


### <a name="application-administrator"></a>Администратор приложений
Может создавать любые аспекты регистрации приложений и работы с корпоративными приложениями и управлять ими.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Создание объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Удаление объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Изменение стандартных свойств объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Изменение свойства Applications.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Изменение свойства Applications.Owners в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Создание объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Удаление объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Изменение стандартных свойств AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Создание объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Удаление объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Изменение стандартных свойств для объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Изменение свойства Policies.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Может предоставлять согласие от лица всех пользователей для всех ресурсов, кроме Azure Active Directory (Azure AD Graph и Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Создание объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Удаление объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Изменение стандартных свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Изменение свойства ServicePrincipals.AppRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Изменение свойства ServicePrincipals.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Изменение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Изменение свойства ServicePrincipals.Owners в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Чтение отчетов Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="application-developer"></a>Разработчик приложения
Может создавать регистрации приложений независимо от значения параметра **Пользователи могут регистрировать приложения**.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Создание объектов Application в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Создание объектов AppRoleAssignment в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Создание объектов OAuth2PermissionGrants в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Создание объектов ServicePrincipal в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |

### <a name="billing-administrator"></a>администратора выставления счетов; 
Может выполнять основные задачи по выставлению счетов, например изменять платежную информацию.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Изменение стандартных свойств для объектов Organization в Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Изменение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Управление всеми аспектами выставления счетов Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="cloud-application-administrator"></a>Администратор облачных приложений
Может задавать и контролировать любые аспекты регистрации приложений и работы с корпоративными приложениями, за исключением прокси приложения.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Создание объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Удаление объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Изменение стандартных свойств объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Изменение свойства Applications.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Изменение свойства Applications.Owners в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Создание объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Удаление объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Изменение стандартных свойств AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Создание объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Удаление объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Изменение стандартных свойств для объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Изменение свойства Policies.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Может предоставлять согласие от лица всех пользователей для всех ресурсов, кроме Azure Active Directory (Azure AD Graph и Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Создание объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Удаление объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Изменение стандартных свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Изменение свойства ServicePrincipals.AppRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Изменение свойства ServicePrincipals.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Изменение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Изменение свойства ServicePrincipals.Owners в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Чтение отчетов Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="company-administrator"></a>Администратор организации
Может контролировать все аспекты служб Azure AD и Майкрософт, использующих удостоверения Azure AD. В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор организации". На [портале Azure](https://portal.azure.com)это "Глобальный администратор".

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Создание и удаление объектов AdministrativeUnit, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Создание и удаление объектов Application, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Создание и удаление объектов AppRoleAssignment, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Создание и удаление объектов CollaborationSpace, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Создание и удаление объектов Contact, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Создание и удаление объектов Device, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Создание и удаление объектов DirectoryRole, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Создание и удаление объектов DirectoryRoleTemplate, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Создание и удаление объектов DirectorySetting, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Создание и удаление объектов DirectorySettingTemplate, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Создание и удаление объектов Domain, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Создание и удаление объектов Group, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Создание и удаление объектов LoginTenantBranding, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Создание и удаление объектов OAuth2PermissionGrants, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Создание и удаление объектов Policy, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Может предоставлять согласие от лица всех пользователей для всех ресурсов, включая Azure Active Directory (Azure AD Graph и Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Создание и удаление объектов ServicePrincipal, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Создание и удаление объектов Organization, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Создание и удаление объектов User, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Управление всеми аспектами выставления счетов Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Центре соответствия требованиям. |
| microsoft.aad.directorysync/AllEntities/AllActions | Выполнение любых действий в Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Управление всеми аспектами для службы защищенного хранилища. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Управление всеми аспектами для службы управления привилегированными пользователями. |
| microsoft.aad.reports/AllEntities/AllActions | Чтение и настройка отчетов Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.crm/AllEntities/AllActions | Управление всеми аспектами Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Управление всеми аспектами Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Управление всеми аспектами Information Protection. |
| microsoft.intune/AllEntities/AllActions | Управление всеми аспектами Intune. |
| microsoft.powerbi/AllEntities/AllActions | Управление всеми аспектами Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Управление Центром защиты Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Управление SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Управление Skype для бизнеса Online. |

### <a name="compliance-administrator"></a>Администратор соответствия требованиям
Может просматривать и изменять конфигурацию соответствия требованиям и отчеты в Azure AD и Office 365.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.compliance/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Центре соответствия требованиям. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.exchange/Compliance/AllActions | Управление соответствием в Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Управление соответствием в SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Управление соответствием в Skype для бизнеса Online. |

### <a name="conditional-access-administrator"></a>Администратор условного доступа
Может контролировать возможности условного доступа.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Создание объектов ConditionalAccessPolicy в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Удаление объектов ConditionalAccessPolicy в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Чтение стандартных свойств для объектов ConditionalAccessPolicy в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Чтение свойства ConditionalAccessPolicys.Owners в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Чтение свойства ConditionalAccessPolicys.PolicyAppliedTo в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Изменение стандартных свойств для объектов ConditionalAccessPolicy в Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Изменение свойства ConditionalAccessPolicys.Owners в Azure Active Directory. |

### <a name="device-administrators"></a>Администраторы устройств

Пользователи с этой ролью становятся администраторами локальных компьютеров на всех устройствах с Windows 10, присоединенных к Azure Active Directory. Они не могут управлять объектами устройств в Azure Active Directory.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Читатели каталогов
Может считывать сведения базового каталога. Предназначено для предоставления доступа к приложениям.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Чтение стандартных свойств для объектов AdministrativeUnit в Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Чтение свойства AdministrativeUnits.Members в Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Чтение стандартных свойств для объектов Application в Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Чтение свойства Applications.Owners в Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Чтение стандартных свойств для объектов CollaborationSpace в Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Чтение свойства CollaborationSpaces.Owners в Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Чтение стандартных свойств для объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Чтение свойства Contacts.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Чтение стандартных свойств для объектов Device в Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Чтение свойства Devices.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Чтение свойства Devices.RegisteredOwners в Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Чтение свойства Devices.RegisteredUsers в Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Чтение стандартных свойств для объектов DirectoryRole в Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Чтение свойства DirectoryRoles.EligibleMembers в Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Чтение свойства DirectoryRoles.Members в Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Чтение стандартных свойств для объектов DirectorySetting в Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Чтение стандартных свойств для объектов DirectorySettingTemplate в Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Чтение стандартных свойств для объектов Domain в Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Чтение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Чтение свойства Groups.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Чтение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Чтение свойства Groups.Owners в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Чтение свойства Groups.Settings в Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Чтение стандартных свойств для объектов OAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Чтение стандартных свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Чтение свойства ServicePrincipals.AppRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Чтение свойства ServicePrincipals.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Чтение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Чтение свойства ServicePrincipals.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Чтение свойства ServicePrincipals.OAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Чтение свойства ServicePrincipals.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Чтение свойства ServicePrincipals.OwnedObjects в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Чтение стандартных свойств для объектов Organization в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/Read | Чтение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Чтение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Чтение свойства Users.DirectReports в Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Чтение свойства Users.InvitedBy в Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Чтение свойства Users.InvitedUsers в Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Чтение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Чтение свойства Users.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Чтение свойства Users.OAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Чтение свойства Users.OwnedDevices в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Чтение свойства Users.OwnedObjects в Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Чтение свойства Users.RegisteredDevices в Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Учетные записи синхронизации службы каталогов
Используется только в службе Azure AD Connect.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Создание объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Удаление объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Чтение стандартных свойств для объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Чтение свойства Policies.Owners в Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Чтение свойства Policies.PolicyAppliedTo в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Изменение стандартных свойств для объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Изменение свойства Policies.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Создание объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Чтение стандартных свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Чтение свойства ServicePrincipals.AppRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Чтение свойства ServicePrincipals.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Чтение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Чтение свойства ServicePrincipals.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Чтение свойства ServicePrincipals.OAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Чтение свойства ServicePrincipals.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Чтение свойства ServicePrincipals.OwnedObjects в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Изменение стандартных свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Изменение свойства ServicePrincipals.AppRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Изменение свойства ServicePrincipals.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Изменение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Изменение свойства ServicePrincipals.Owners в Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Изменение свойства Organizations.DirSync в Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Выполнение любых действий в Azure AD Connect. |

### <a name="directory-writer"></a>Редактор каталогов
Может считывать и записывать сведения базового каталога. Предназначено для предоставления доступа к приложениям.

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Создание объектов DirectorySetting в Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Удаление объектов DirectorySetting в Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Изменение стандартных свойств для объектов DirectorySetting в Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Создание объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Создание объектов Group в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Изменение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Изменение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Изменение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Изменение свойства Groups.Owners в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Изменение свойства Groups.Settings в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Update | Изменение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Изменение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Изменение свойства Users.Manager в Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Администратор служб Dynamics 365
Может контролировать все аспекты продукта Dynamics 365.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.crm/AllEntities/AllActions | Управление всеми аспектами Dynamics 365. |

### <a name="exchange-service-administrator"></a>Администратор службы Exchange
Может контролировать все аспекты продукта Exchange.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.exchange/AllEntities/AllActions | Управление всеми аспектами Exchange Online. |

### <a name="guest-inviter"></a>Приглашающий гостей
Может приглашать гостей независимо от значения параметра **Участники могут приглашать гостей**.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли гостя.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Приглашение пользователей-гостей в Azure Active Directory. |
| microsoft.aad.directory/User/Read | Чтение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Чтение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Чтение свойства Users.DirectReports в Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Чтение свойства Users.InvitedBy в Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Чтение свойства Users.InvitedUsers в Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Чтение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Чтение свойства Users.MemberOf в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Чтение свойства Users.OAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Чтение свойства Users.OwnedDevices в Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Чтение свойства Users.OwnedObjects в Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Чтение свойства Users.RegisteredDevices в Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Администратор службы технической поддержки
Может сбрасывать пароли пользователей, не являющихся администраторами, и пароли администраторов службы поддержки.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Изменение паролей для администраторов с ограниченными правами и других администраторов службы технической поддержки в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |

### <a name="information-protection-administrator"></a>Администратор Azure Information Protection
Может контролировать все аспекты продукта Azure Information Protection.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Управление всеми аспектами Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="intune-service-administrator"></a>Администратор службы Intune
Может контролировать все аспекты продукта Intune.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Создание объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Удаление объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Изменение стандартных свойств для объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Создание объектов Device в Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Удаление объектов Device в Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Изменение стандартных свойств для объектов Device в Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Изменение свойства Devices.RegisteredOwners в Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Изменение свойства Devices.RegisteredUsers в Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Создание объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Создание объектов Group в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/Group/Delete | Удаление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Чтение свойства Groups.HiddenMembers в Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Восстановление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Изменение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Изменение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Изменение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Изменение свойства Groups.Owners в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Изменение свойства Groups.Settings в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/Update | Изменение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Изменение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Изменение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.intune/AllEntities/AllActions | Управление всеми аспектами Intune. |


### <a name="license-administrator"></a>Администратор лицензий
Может управлять лицензиями продуктов для пользователей и групп.
 
  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >
 
| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Изменение свойства Users.UsageLocation в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

### <a name="lync-service-administrator"></a>Администратор службы Lync
Может контролировать все аспекты продукта "Skype для бизнеса".

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Управление Skype для бизнеса Online. |

### <a name="message-center-reader"></a>Читатель Центра сообщений
Может читать сообщения и обновления для своей организации только в Центре сообщений Office 365. 

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Создание и удаление всех ресурсов и чтение и изменение стандартных свойств в Центре сообщений. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |

### <a name="partner-tier1-support"></a>Служба поддержка партнеров уровня 1
Не используйте (не предназначено для общего применения).

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Создание объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Удаление объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Изменение стандартных свойств для объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Создание объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Создание объектов Group в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Изменение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Изменение свойства Groups.Owners в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Удаление объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Update | Изменение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Изменение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Изменение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Обновление паролей для пользователей, не являющихся администраторами, в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="partner-tier2-support"></a>Служба поддержка партнеров уровня 2
Не используйте (не предназначено для общего применения).

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Создание объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Удаление объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Изменение стандартных свойств для объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Создание и удаление объектов Domain, а также чтение и изменение стандартных свойств в Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Создание объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Удаление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Восстановление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Изменение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Изменение стандартных свойств для объектов Organization в Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Изменение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Удаление объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Update | Изменение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Изменение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Изменение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |

### <a name="power-bi-service-administrator"></a>Администратор службы Power BI
Может контролировать все аспекты продукта Power BI.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.powerbi/AllEntities/AllActions | Управление всеми аспектами Power BI. |

### <a name="privileged-role-administrator"></a>Администратор привилегированных ролей
Может управлять назначением ролей в Azure AD.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Изменение стандартных свойств для объектов DirectoryRole в Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Управление всеми аспектами для службы управления привилегированными пользователями. |

### <a name="reports-reader"></a>Читатель отчетов
Может просматривать отчеты о входах и аудите.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Чтение отчетов Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.usagereports/AllEntities/Read | Чтение отчетов об использовании Office 365. |

### <a name="security-administrator"></a>Администратор безопасности
Может просматривать отчеты и сведения о безопасности.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Изменение свойства Applications.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Создание объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Удаление объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Изменение стандартных свойств для объектов Policy в Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Изменение свойства Policies.Owners в Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Изменение свойства ServicePrincipals.DefaultPolicy в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Чтение всех аспектов для управления привилегированными пользователями. |
| microsoft.protectioncenter/AllEntities/Read | Чтение всех аспектов Центра защиты Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Управление Центром защиты Office 365. |

### <a name="security-reader"></a>Чтение данных безопасности
Может просматривать отчеты и сведения о безопасности в Azure AD и Office 365.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Чтение всех аспектов для управления привилегированными пользователями. |
| microsoft.protectioncenter/AllEntities/Read | Чтение всех аспектов Центра защиты Office 365. |

### <a name="service-support-administrator"></a>Администратор службы поддержки
Может просматривать сведения о работоспособности служб и работать с запросами в службу поддержки.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |

### <a name="sharepoint-service-administrator"></a>Администратор службы SharePoint
Может контролировать все аспекты службы SharePoint.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.sharepoint/AllEntities/AllActions | Управление SharePoint Online. |

### <a name="user-account-administrator"></a>Администратор учетных записей
Может управлять всеми аспектами пользователей и групп

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у [роли пользователя](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Создание объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Удаление объектов AppRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Изменение стандартных свойств AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Создание объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Удаление объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Изменение стандартных свойств для объектов Contact в Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Создание объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Создание объектов Group в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/Group/Delete | Удаление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Чтение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Чтение свойства Groups.HiddenMembers в Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Восстановление объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Изменение стандартных свойств для объектов Group в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Изменение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Изменение свойства Groups.Members в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Изменение свойства Groups.Owners в Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Изменение свойства Groups.Settings в Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Чтение свойства Organizations.TrustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Create | Создание объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Удаление объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/User/Update | Изменение стандартных свойств для объектов User в Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Изменение свойства Users.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Изменение свойства Users.Manager в Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Изменение паролей для администраторов с ограниченными правами, администраторов службы технической поддержки и других администраторов учетных записей пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.accessservice/AllEntities/AllActions | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Чтение и настройка работоспособности служб Office 365. |

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об изменении администраторов для подписки Azure см. в статье [Добавление или изменение ролей администратора Azure](../../billing/billing-add-change-azure-subscription-administrator.md).
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в статье [Связь между подписками Azure и Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
