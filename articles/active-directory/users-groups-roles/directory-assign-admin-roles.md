---
title: Разрешения роли администратора в Azure Active Directory | Документация Майкрософт
description: Роль администратора позволяет создавать или изменять пользователей, назначать административные роли, сбрасывать пароли пользователей, управлять доменами и пользовательскими лицензиями.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 293d8376d83d729588aab0aeaa1040d9b3e5e0b5
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182286"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Разрешения роли администратора в Azure Active Directory

С помощью Azure Active Directory (Azure AD) можно назначить несколько администраторов, которые будут выполнять различные функции. На портале Azure AD можно назначить администраторов для выполнения задач, таких как добавление или изменение пользователей, назначение административных ролей, сброс паролей пользователей, управление лицензиями пользователей и управление доменными именами.

Глобальный администратор имеет доступ ко всем административным функциям. По умолчанию роль глобального администратора каталога назначается пользователю, зарегистрировавшему подписку Azure. Только глобальные администраторы могут делегировать административные роли.

## <a name="assign-or-remove-administrator-roles"></a>Назначение и удаление ролей администратора

Дополнительные сведения см. в руководстве по [просмотру и назначению ролей администратора в Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Доступные роли

Доступны следующие роли администратора.

* **[Администратор приложения](#application-administrator)**: пользователи с этой ролью могут создавать и контролировать все аспекты корпоративных приложений, регистрации приложений, а также параметры прокси приложения. Кроме того, эта роль позволяет соглашаться на делегированные разрешения и разрешения приложений, за исключением Microsoft Graph и Azure AD Graph. Члены этой роли не добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Разработчик приложения](#application-developer)**: пользователи с этой ролью могут создавать регистрации приложений, когда для параметра "Пользователи могут регистрировать приложения" задано значение "Нет". Кроме того, эта роль позволяет членам давать согласие от своего имени, когда для параметра "Пользователи могут разрешать приложениям доступ к корпоративным данным от своего имени" задано значение "Нет". Члены этой роли добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Администратор выставления счетов](#billing-administrator)**: совершает покупки, управляет подписками и запросами в службу поддержки, а также отслеживает работоспособность службы.

* **[Администратор облачных приложений](#cloud-application-administrator)**: пользователи с этой ролью имеют те же разрешения, что и для роли администратора приложений, за исключением возможности управления прокси приложения. Эта роль позволяет создавать и контролировать все аспекты корпоративных приложений и регистраций приложений. Кроме того, эта роль позволяет соглашаться на делегированные разрешения и разрешения приложений, за исключением Microsoft Graph и Azure AD Graph. Члены этой роли не добавляются в качестве владельцев при создании регистраций приложений или корпоративных приложений.

* **[Администратор облачных устройств](#cloud-device-administrator)**: пользователи с этой ролью могут включать, отключать и удалять устройства в Azure AD и считывать ключи BitLocker в Windows 10 (при наличии) на портале Azure. Роль не предоставляет разрешения на управление любыми другими свойствами устройства.

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

* **[Администратор паролей или администратор службы поддержки](#helpdesk-administrator)**: пользователи с этой ролью могут изменять пароли, аннулировать маркеры доступа, управлять запросами на обслуживание и отслеживать работоспособность служб. Администраторы службы поддержки могут сбрасывать пароли и аннулировать маркеры доступа только для непривилегированных пользователей и других администраторов службы поддержки. После аннулирования маркера обновления пользователь должен выполнить вход еще раз.

  > [!NOTE]
  > В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор службы поддержки". На [портале Azure](https://portal.azure.com/) это "Администратор паролей".
  >
  >
  
* **[Администратор службы Power BI](#power-bi-service-administrator)**: пользователи с этой ролью имеют глобальные разрешения в Microsoft Power BI при наличии этой службы, а также возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Дополнительные сведения см. в статье [Основные сведения о роли администратора Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Администратор привилегированных ролей](#privileged-role-administrator)**: пользователи с этой ролью могут управлять назначением ролей в Azure Active Directory, а также в Azure AD Privileged Identity Management. Кроме того, эта роль позволяет управлять всеми аспектами управления привилегированными пользователями.

* **[Читатель отчетов](#reports-reader)**: пользователи с этой ролью могут просматривать отчеты об использовании и панель мониторинга с отчетами в центре администрирования Office 365, а также пакет контекста внедрения в PowerBI. Кроме того, эта роль предоставляет доступ к отчетам о входах, сведениям о действиях в Azure AD и данным от API отчетов Microsoft Graph. Пользователь с ролью "Читатель отчетов" имеет доступ только к релевантным метрикам использования и внедрения. Он не приобретает полномочия администратора по настройке или использованию центров администрирования отдельных продуктов (например, Excahange). 

* **[Администратор безопасности](#security-administrator)**: пользователи с этой ролью имеют все разрешения только на чтение, доступные для роли "Чтение данных безопасности". Также они могут управлять конфигурацией служб, связанных с безопасностью, таких как "Защита идентификации Azure Active Directory", Azure Information Protection и Центр безопасности и соответствия требованиям Office 365. Дополнительные сведения о разрешениях в Office 365 см. в статье [Разрешения в Центре безопасности и соответствия требованиям Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
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

* **[Администратор службы Skype для бизнеса или администратор службы Lync](#skype-for-business-administrator)**: у пользователей с этой ролью есть глобальные разрешения в Microsoft Skype для бизнеса при наличии этой службы, а также возможность управлять определенными атрибутами пользователя Skype в Azure Active Directory. Кроме того, эта роль позволяет управлять запросами в службу поддержки и отслеживать работоспособность служб, а также предоставляет доступ к центру администрирования Teams и Skype для бизнеса. Учетная запись также должна иметь лицензию Teams, иначе она не сможет запускать командлеты PowerShell Teams. Дополнительные сведения см. в руководствах по [использованию роли администратора Skype для бизнеса](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) и [лицензировании дополнительных компонентов Skype для бизнеса и Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

  > [!NOTE]
  > В API Microsoft Graph, API Azure AD Graph и Azure AD PowerShell эта роль определяется как "Администратор службы Lync". На [портале Azure](https://portal.azure.com/) это "Администратор службы Skype для бизнеса".
  >
  >

* **[Администратор связи Teams](#teams-communications-administrator)**: пользователи с этой ролью могут управлять функциями рабочей нагрузки Microsoft Teams, связанными с голосовой связью и телефонией. Сюда входят средства управления для назначения номера телефона, политики голосовой связи и собраний, а также полный доступ к набору инструментов анализа вызовов.

* **[Инженер службы поддержки связи Teams](#teams-communications-support-engineer)**: пользователи с этой ролью могут устранять неполадки со связью в Microsoft Teams и Skype для бизнеса с помощью соответствующих средств для пользовательских вызовов в центре администрирования Microsoft Teams и Skype для бизнеса. Пользователи с этой ролью могут просматривать полные сведения о записях вызовов для всех задействованных участников.

* **[Специалист службы поддержки связи Teams](#teams-communications-support-specialist)**: пользователи с этой ролью могут устранять неполадки со связью в Microsoft Teams и Skype для бизнеса с помощью средств устранения неполадок пользовательских вызовов в центре администрирования Microsoft Teams и Skype для бизнеса. Пользователи с этой ролью могут просматривать сведения об определенном пользователе только при его вызове.

* **[Администратор службы Teams](#teams-service-administrator)**: пользователи с этой ролью могут управлять всеми функциями рабочей нагрузки Microsoft Teams с помощью центра администрирования Microsoft Teams и Skype для бизнеса и соответствующих модулей PowerShell. Сюда входят, помимо прочего, все средства управления, связанные с телефонией, обменом сообщениями, собраниями и самими командами Teams. Также эта роль позволяет управлять группами Office 365.

* **[Администратор учетных записей пользователей](#user-account-administrator)**: пользователи с этой ролью могут создавать аспекты пользователей и групп, а также управлять ими. Кроме того, эта роль включает в себя возможность управлять запросами в службу поддержки и отслеживать работоспособность служб. Существуют некоторые ограничения. Например, эта роль не позволяет удалить глобального администратора. Администраторы учетных записей могут изменять пароли и аннулировать маркеры обновления только для непривилегированных пользователей, администраторов службы технической поддержки и администраторов учетных записей. После аннулирования маркера обновления пользователь должен выполнить вход еще раз.

| Может | Не может |
| --- | --- |
| <p>Просмотр сведений о компании и пользователях</p><p>Управление запросами в службу поддержки Office</p><p>Изменять пароли только для непривилегированных пользователей, администраторов службы технической поддержки и администраторов учетных записей</p><p>Создание представлений пользователя и управление ими</p><p>Создавать, изменять, удалять пользователей и группы, а также управлять пользовательскими лицензиями без ограничений. Администраторы не могут удалить глобального администратора или создать других администраторов.</p> |<p>Выполнение операций выставления счетов и приобретения продуктов Office</p><p>Управление доменами</p><p>Управление информацией о компании</p><p>Делегирование административных ролей другим пользователям</p><p>Использование синхронизации каталогов</p><p>Включение или отключение Многофакторной идентификации</p><p>Просмотр журналов аудита</p> |

В следующих таблицах описываются разрешения в Azure Active Directory, предоставленные каждой роли. Некоторые роли могут иметь дополнительные разрешения в службах Майкрософт за пределами Azure Active Directory.

## <a name="adhoc-license-administrator"></a>Администратор отдельных лицензий
Может создавать любые аспекты регистрации приложений и работы с корпоративными приложениями и управлять ими.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/domains/default/read | Чтение базовых свойств доменов в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Чтение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Чтение базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Чтение свойства groups.memberOf в Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Чтение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Чтение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Чтение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Чтение базовых свойств для объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Обновление объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/organization/default/read | Чтение базовых свойств в организации в Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Чтение свойства оrganizations.trustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Чтение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Чтение базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Чтение свойства users.directReports в Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Чтение свойства users.invitedBy в Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Чтение свойства users.invitedUsers в Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Чтение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Чтение свойства users.memberOf в Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Чтение свойства users.оAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Чтение свойства users.ownedDevices в Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Чтение свойства users.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Чтение свойства users.registeredDevices в Azure Active Directory. |

## <a name="application-administrator"></a>Администратор приложений
Может создавать любые аспекты регистрации приложений и работы с корпоративными приложениями и управлять ими.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Изменение свойства applications.audience в Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Изменение свойства applications.authentication в Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Изменение базовых свойств приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/create | Создание приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Изменение свойства applications.credentials в Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Удаление приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Изменение свойства applications.owners в Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Изменение свойства applications.permissions в Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Создание объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Чтение объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Изменение объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Удаление объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Обновление свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Создание политик в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Обновление свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Создание объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Удаление объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Чтение отчетов Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="application-developer"></a>Разработчик приложения
Может создавать регистрации приложений независимо от значения параметра �Пользователи могут регистрировать приложения�.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Создание приложений в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Создание объектов appRoleAssignment в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Создание объектов oAuth2PermissionGrants в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Создание объектов servicePrincipal в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |

## <a name="billing-administrator"></a>администратора выставления счетов; 
Может выполнять основные задачи по выставлению счетов, например изменять платежную информацию.

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
| microsoft.aad.directory/organization/default/update | Обновление базовых свойств в организации в Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Обновление свойства оrganizations.trustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.commerce.billing/allEntities/allTasks | Управление всеми аспектами выставления счетов Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="cloud-application-administrator"></a>Администратор облачных приложений
Может задавать и контролировать любые аспекты регистрации приложений и работы с корпоративными приложениями, за исключением прокси приложения.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Изменение свойства applications.audience в Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Изменение свойства applications.authentication в Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Изменение базовых свойств приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/create | Создание приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Изменение свойства applications.credentials в Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Удаление приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Изменение свойства applications.owners в Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Изменение свойства applications.permissions в Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Создание объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Изменение объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Удаление объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Создание политик в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Обновление свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Обновление свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Чтение свойства applicationConfiguration в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Создание объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Удаление объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Чтение отчетов Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="cloud-device-administrator"></a>Администратор облачного устройства
Полный доступ для управления устройствами в Azure AD.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/devices/delete | Удаление устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Отключение устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Включение устройств в Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Чтение отчетов Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

## <a name="company-administrator"></a>Администратор организации
Может контролировать все аспекты служб Azure AD и Майкрософт, использующих удостоверения Azure AD.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли.
  >
  >

  > [!NOTE]
  > Эта роль имеет дополнительные разрешения за пределами Azure Active Directory. Дополнительные сведения см. в описании ролей выше.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Создание и удаление объектов administrativeUnit, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Создание и удаление приложений, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Создание и удаление объектов appRoleAssignment, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Создание и удаление контактов, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Создание и удаление контрактов, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Создание и удаление устройств, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Создание и удаление объектов directoryRole, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Создание и удаление объектов directoryRoleTemplate, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Создание и удаление доменов, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Создание и удаление групп, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Создание и удаление объектов groupSetting, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Создание и удаление объектов groupSettingTemplate, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Создание и удаление объектов loginTenantBranding, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Создание и удаление объектов oAuth2PermissionGrant, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Создание и удаление организаций, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Создание и удаление политик, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Создание и удаление объектов roleAssignment и чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Создание и удаление объектов roleDefinition и чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Создание и удаление объектов scopedRoleMembership и чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Выполнение действия службы Activateservice в Azure Active Directory. |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Выполнение действия службы Disabledirectoryfeature в Azure Active Directory. |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Выполнение действия службы Enabledirectoryfeature в Azure Active Directory. |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Выполнение действия службы Getavailableextentionproperties в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Создание и удаление объектов servicePrincipal, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Создание и удаление объектов subscribedSku и чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Создание и удаление пользователей, а также чтение и изменение всех свойств в Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Выполнение любых действий в Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Чтение всех ресурсов в microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Чтение и настройка отчетов Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.informationProtection/allEntities/allTasks | Управление всеми аспектами Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.commerce.billing/allEntities/allTasks | Управление всеми аспектами выставления счетов Office 365. |
| microsoft.intune/allEntities/allTasks | Управление всеми аспектами Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Управление всеми аспектами Office 365 Compliance Manager. |
| microsoft.office365.exchange/allEntities/allTasks | Управление всеми аспектами Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Управление всеми аспектами защищенного хранилища Office 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Управление всеми аспектами Power BI. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Управление всеми аспектами Центра защиты Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Управление всеми аспектами Skype для бизнеса Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Управление всеми аспектами Dynamics 365. |

## <a name="compliance-administrator"></a>Администратор соответствия требованиям
Может просматривать и изменять конфигурацию соответствия требованиям и отчеты в Azure AD и Office 365.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.complianceManager/allEntities/allTasks | Управление всеми аспектами Office 365 Compliance Manager. |
| microsoft.office365.exchange/allEntities/allTasks | Управление всеми аспектами Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Управление всеми аспектами Skype для бизнеса Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="conditional-access-administrator"></a>Администратор условного доступа
Может контролировать возможности условного доступа.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Чтение свойства policies.conditionalAccess в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Обновление свойства policies.conditionalAccess в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Создание политик в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Чтение свойства policies.conditionalAccess в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Обновление свойства policies.conditionalAccess в Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Чтение свойства policies.conditionalAccess в Azure Active Directory. |

## <a name="customer-lockbox-access-approver"></a>Лицо, утверждающее доступ клиентов к LockBox
Может утверждать запросы в службу поддержки Майкрософт для получения доступа к данным организации клиента.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.office365.lockbox/allEntities/allTasks | Управление всеми аспектами защищенного хранилища Office 365. |

## <a name="device-administrators"></a>Администраторы устройств
Члены этой роли добавляются в группу локальных администраторов на устройствах, присоединенных к Azure AD.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Чтение базовых свойств для объектов groupSettings в Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Чтение базовых свойств для объектов groupSettingTemplate в Azure Active Directory. |

## <a name="device-managers"></a>Диспетчеры устройств
Может утверждать запросы в службу поддержки Майкрософт для получения доступа к данным организации клиента.

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
| microsoft.aad.directory/devices/default/read | Чтение базовых свойств устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/default/update | Обновление базовых свойств устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Чтение свойства devices.memberOf в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Чтение свойства devices.registeredOwners в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Изменение свойства devices.registeredOwners в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Чтение свойства devices.registeredUsers в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Изменение свойства devices.registeredUsers в Azure Active Directory. |

## <a name="directory-readers"></a>Читатели каталогов
Может считывать сведения базового каталога. Предназначено для предоставления доступа к приложениям.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Чтение стандартных свойств для объектов administrativeUnit в Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Чтение свойства administrativeUnits.members в Azure Active Directory. |
Azure Active Directory; |
| microsoft.aad.directory/applications/default/read | Чтение стандартных свойств приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Чтение свойства applications.owners в Azure Active Directory. |
| microsoft.aad.directory/contacts/default/read | Чтение базовых свойств контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Чтение свойства contacts.memberOf в Azure Active Directory. |
| microsoft.aad.directory/contracts/default/read | Чтение базовых свойств контрактов в Azure Active Directory. |
| microsoft.aad.directory/devices/default/read | Чтение базовых свойств устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Чтение свойства devices.memberOf в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Чтение свойства devices.registeredOwners в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Чтение свойства devices.registeredUsers в Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/default/read | Чтение базовых свойств объектов DirectoryRole в Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Чтение свойства directoryroles.eligibleMembers в Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Чтение свойства directoryRoles.members в Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Чтение базовых свойств доменов в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Чтение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Чтение базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Чтение свойства groups.memberOf в Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Чтение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Чтение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Чтение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/read | Чтение базовых свойств для объектов groupSettings в Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Чтение базовых свойств для объектов groupSettingTemplate в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Чтение базовых свойств для объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/organization/default/read | Чтение базовых свойств в организации в Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Чтение свойства оrganizations.trustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Чтение свойства ervicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Чтение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Чтение базовых свойств для объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Чтение свойства servicePrincipals.memberOf в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Чтение свойства servicePrincipals.oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Чтение свойства servicePrincipals.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Чтение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Чтение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/default/read | Чтение базовых свойств объектов subscribedSku в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Чтение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Чтение базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Чтение свойства users.directReports в Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Чтение свойства users.invitedBy в Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Чтение свойства users.invitedUsers в Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Чтение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Чтение свойства users.memberOf в Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Чтение свойства users.оAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Чтение свойства users.ownedDevices в Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Чтение свойства users.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Чтение свойства users.registeredDevices в Azure Active Directory. |

## <a name="directory-synchronization-accounts"></a>Учетные записи синхронизации службы каталогов
Используется только в службе Azure AD Connect.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Изменение свойства organization.dirSync в Azure Active Directory. |
| microsoft.aad.directory/policies/create | Создание политик в Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/default/read | Чтение базовых свойств политик в Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Обновление базовых свойств политик в Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Чтение свойства policies.owners в Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Изменение свойства policies.owners в Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Чтение свойства policies.policyAppliedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Чтение свойства ervicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Чтение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Чтение базовых свойств для объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Создание объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Чтение свойства servicePrincipals.memberOf в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Чтение свойства servicePrincipals.oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Чтение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Чтение свойства servicePrincipals.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Чтение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Выполнение любых действий в Azure AD Connect. |

## <a name="directory-writers"></a>Создатели каталогов
Может считывать и записывать сведения базового каталога. Предназначено для предоставления доступа к приложениям.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/groups/create | Создание групп в Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Создание групп в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/update | Обновление базовых свойств для объектов groupSettings в Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Создание свойства groupSettings в Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Удаление объектов groupSetting в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Обновление базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Изменение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Изменение свойства users.userPrincipalName в Azure Active Directory. |

## <a name="dynamics-365-administrator"></a>Администратор Dynamics 365
Может контролировать все аспекты продукта Dynamics 365. Ранее был известен как администратор службы CRM.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Управление всеми аспектами Dynamics 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="exchange-service-administrator"></a>Администратор службы Exchange
Может контролировать все аспекты продукта Exchange.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.exchange/allEntities/allTasks | Управление всеми аспектами Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="guest"></a>Гость
Роль по умолчанию для гостевых пользователей. Может считывать ограниченный набор сведений о каталоге.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения от роли пользователя.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/applications/default/read | Чтение стандартных свойств приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Чтение свойства applications.owners в Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Чтение базовых свойств доменов в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Чтение свойства Groups.AppRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/read | Чтение базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Чтение свойства groups.memberOf в Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Чтение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Чтение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Чтение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/organization/basicProfile/read | Чтение базовых сведений о профиле организации в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Чтение свойства ervicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Чтение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Чтение базовых свойств для объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Чтение свойства servicePrincipals.memberOf в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/members/read | Чтение свойства servicePrincipals.members в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Чтение свойства servicePrincipals.oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Чтение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Чтение свойства servicePrincipals.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Чтение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/read | Чтение свойства users.basicProfile в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Чтение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Чтение базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Чтение свойства users.directReports в Azure Active Directory. |
| microsoft.aad.directory/users/eligibleMemberOf/read | Чтение свойства users.eligibleMemberOf в Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Чтение свойства users.invitedBy в Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Чтение свойства users.invitedUsers в Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Чтение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Чтение свойства users.memberOf в Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Чтение свойства users.оAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Чтение свойства users.ownedDevices в Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Чтение свойства users.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.directory/users/pendingMemberOf/read | Чтение свойства users.pendingMemberOf в Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Чтение свойства users.registeredDevices в Azure Active Directory. |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Чтение свойства users.scopedAdministratorOf в Azure Active Directory. |

## <a name="guest-inviter"></a>Приглашающий гостей
Может приглашать гостей независимо от значения параметра Members can invite guests (Участники могут приглашать гостей).

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения от роли пользователя.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Чтение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/default/read | Чтение базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Чтение свойства users.directReports в Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Чтение свойства users.invitedBy в Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Приглашение пользователей-гостей в Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Чтение свойства users.invitedUsers в Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Чтение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Чтение свойства users.memberOf в Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Чтение свойства users.оAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Чтение свойства users.ownedDevices в Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Чтение свойства users.ownedObjects в Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Чтение свойства users.registeredDevices в Azure Active Directory. |

## <a name="helpdesk-administrator"></a>Администратор службы технической поддержки
Может сбрасывать пароли пользователей, не являющихся администраторами, и пароли администраторов службы поддержки.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="information-protection-administrator"></a>Администратор Azure Information Protection
Может контролировать все аспекты продукта Azure Information Protection.

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
| microsoft.azure.informationProtection/allEntities/allTasks | Управление всеми аспектами Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="intune-service-administrator"></a>Администратор службы Intune
Может контролировать все аспекты продукта Intune.

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
| microsoft.aad.directory/contacts/default/update | Обновление базовых свойств контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Создание контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Удаление контактов в Azure Active Directory. |
| microsoft.aad.directory/devices/default/update | Обновление базовых свойств устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/create | Создание устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Удаление устройств в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Изменение свойства devices.registeredOwners в Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Изменение свойства devices.registeredUsers в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/create | Создание групп в Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Создание групп в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/groups/delete | Удаление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Чтение свойства roups.hiddenMembers в Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Восстановление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Обновление базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Изменение свойства users.manager в Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.intune/allEntities/allTasks | Управление всеми аспектами Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="license-administrator"></a>Администратор лицензий
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

## <a name="message-center-reader"></a>Читатель Центра сообщений
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
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Создание и удаление всех ресурсов и чтение и изменение стандартных свойств в Центре сообщений. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |

## <a name="partner-tier1-support"></a>Служба поддержка партнеров уровня 1
Не используйте (не предназначено для общего применения).

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
| microsoft.aad.directory/contacts/default/update | Обновление базовых свойств контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Создание контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Удаление контактов в Azure Active Directory. |
| microsoft.aad.directory/groups/create | Создание групп в Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Создание групп в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Обновление базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/delete | Удаление пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Изменение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.directory/users/restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Изменение свойства users.userPrincipalName в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="partner-tier2-support"></a>Служба поддержка партнеров уровня 2
Не используйте (не предназначено для общего применения).

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
| microsoft.aad.directory/contacts/default/update | Обновление базовых свойств контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Создание контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Удаление контактов в Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Создание и удаление доменов, а также чтение и изменение стандартных свойств в Azure Active Directory. |
| microsoft.aad.directory/groups/create | Создание групп в Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Удаление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Восстановление групп в Azure Active Directory. |
| microsoft.aad.directory/organization/default/update | Обновление базовых свойств в организации в Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Обновление свойства оrganizations.trustedCAsForPasswordlessAuth в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Обновление базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/delete | Удаление пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Изменение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.directory/users/restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Изменение свойства users.userPrincipalName в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="power-bi-service-administrator"></a>Администратор службы Power BI
Может контролировать все аспекты продукта Power BI.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Управление всеми аспектами Power BI. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="privileged-role-administrator"></a>Администратор привилегированных ролей
Может управлять назначением ролей в Azure AD.

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
| microsoft.aad.directory/directoryRoles/update | Обновление объектов directoryRole в Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.aad.privilegedIdentityManagement. |

## <a name="reports-reader"></a>Читатель отчетов
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
| microsoft.aad.reports/allEntities/read | Чтение отчетов Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.usageReports/allEntities/read | Чтение отчетов об использовании Office 365. |

## <a name="security-administrator"></a>Администратор безопасности
Может просматривать отчеты и сведения о безопасности.

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
| microsoft.aad.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Обновление базовых свойств политик в Azure Active Directory. |
| microsoft.aad.directory/policies/create | Создание политик в Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Изменение свойства policies.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Чтение всех ресурсов в microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Обновление всех ресурсов в microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Чтение всех ресурсов в microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.protectionCenter/allEntities/read | Чтение всех аспектов Центра защиты Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Обновление всех ресурсов в microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

## <a name="security-reader"></a>Чтение данных безопасности
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
| microsoft.aad.identityProtection/allEntities/read | Чтение всех ресурсов в microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Чтение всех ресурсов в microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.protectionCenter/allEntities/read | Чтение всех аспектов Центра защиты Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

## <a name="service-support-administrator"></a>Администратор службы поддержки
Может просматривать сведения о работоспособности служб и работать с запросами в службу поддержки.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="sharepoint-service-administrator"></a>Администратор службы SharePoint
Может контролировать все аспекты службы SharePoint.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Создание и удаление всех ресурсов, а также чтение и изменение стандартных свойств в microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="skype-for-business-administrator"></a>Администратор Skype для бизнеса
Может контролировать все аспекты продукта "Skype для бизнеса". Прежнее название — администратор службы Lync.

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
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Управление всеми аспектами Skype для бизнеса Online |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="teams-communications-administrator"></a>Администратор связи Teams
Может управлять функциями вызовов и собраний в пределах службы Microsoft Teams.

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
| microsoft.aad.directory/policies/basic/read | Чтение базовых свойств политик в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.office365.usageReports/allEntities/read | Чтение отчетов об использовании Office 365. |

## <a name="teams-communications-support-engineer"></a>Инженер службы поддержки связи Teams
Может устранять неполадки со связью в пределах Teams с помощью расширенных средств.

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
| microsoft.aad.directory/policies/basic/read | Чтение базовых свойств политик в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

## <a name="teams-communications-support-specialist"></a>Специалист службы поддержки связи Teams
Может устранять неполадки со связью в пределах Teams с помощью базовых средств.

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
| microsoft.aad.directory/policies/basic/read | Чтение базовых свойств политик в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |

## <a name="teams-service-administrator"></a>Администратор службы Teams
Может управлять службой Microsoft Teams.

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
| microsoft.aad.directory/groups/hiddenMembers/read | Чтение свойства roups.hiddenMembers в Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Чтение базовых свойств политик в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |
| microsoft.office365.usageReports/allEntities/read | Чтение отчетов об использовании Office 365. |

## <a name="user-account-administrator"></a>Администратор учетных записей
Может управлять всеми аспектами пользователей и групп

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Создание объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Удаление объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Изменение объектов appRoleAssignment в Azure Active Directory. |
| microsoft.aad.directory/contacts/default/update | Обновление базовых свойств контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Создание контактов в Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Удаление контактов в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/create | Создание групп в Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Создание групп в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/groups/delete | Удаление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Чтение свойства roups.hiddenMembers в Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Восстановление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Изменение свойства users.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Управление лицензиями для пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/default/update | Обновление базовых свойств пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/create | Создание пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/delete | Удаление пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Изменение свойства users.manager в Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Обновление паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.directory/users/restore | Восстановление удаленных пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Изменение свойства users.userPrincipalName в Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Управление всеми аспектами службы доступа Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Чтение и настройка Работоспособности служб Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Azure и управление ими. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Чтение и настройка работоспособности служб Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Создание запросов в службу поддержки Office 365 и управление ими. |

## <a name="user"></a>Пользователь
Роль по умолчанию для пользователей-участников. Может считывать все и записывать ограниченный набор сведений о каталоге.

  > [!NOTE]
  > Эта роль наследует дополнительные разрешения у роли читателя каталогов.
  >
  >

| **Действия** | **Описание** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Создание приложений в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/groups/default/read | Чтение базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Создание групп в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Создание объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Удаление объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Обновление объектов oAuth2PermissionGrants в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Создание объектов servicePrincipal в Azure Active Directory. Автор добавляется как первый владелец, а созданный объект учитывается в квоте автора на 250 созданных объектов. |
| microsoft.aad.directory/users/activateServicePlan | Активация объекта пользователя activateServicePlan в Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Приглашение пользователей-гостей в Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Изменение базовых свойств приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Удаление приложений в Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Изменение свойства applications.owners в Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Изменение свойства applications.permissions в Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Изменение свойства applications.policies в Azure Active Directory. |
| microsoft.aad.directory/applications/restore | Восстановление приложений в Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Отключение устройств в Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Изменение свойства groups.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/groups/default/update | Обновление базовых свойств в группах в Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Удаление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Изменение свойства groups.dynamicMembershipRule в Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Изменение свойства groups.members в Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Изменение свойства groups.owners в Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Восстановление групп в Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Изменение свойства groups.settings в Azure Active Directory. |
| microsoft.aad.directory/policies/default/update | Обновление базовых свойств политик в Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Удаление политик в Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Изменение свойства policies.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Изменение свойства servicePrincipals.appRoleAssignedTo в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Изменение свойства servicePrincipals.appRoleAssignments в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Обновление базовых свойств для объектов ServicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Удаление объектов servicePrincipal в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Изменение свойства servicePrincipals.owners в Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Изменение свойства servicePrincipals.policies в Azure Active Directory. |
| microsoft.aad.directory/users/changePassword | Изменение паролей для всех пользователей в Azure Active Directory. Дополнительные сведения см. в электронной документации. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Отмена всех маркеров обновления пользователей в Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/update | Обновление свойства users.basicProfile в Azure Active Directory. |
| microsoft.aad.directory/users/mobile/update | Обновление свойства users.mobile в Azure Active Directory. |
| microsoft.aad.directory/users/searchableDeviceKey/update | Обновление свойства users.searchableDeviceKey в Azure Active Directory. |

## <a name="deprecated-roles"></a>Устаревшие роли

Не рекомендуется использовать следующие роли. Они больше не поддерживаются и будут удалены из Azure AD в будущем.

* Администратор отдельных лицензий
* Device Join (Присоединение устройства)
* Диспетчеры устройств
* Device Users (Пользователи устройства)
* Создатель проверенного пользователя электронной почты
* администратор почтовых ящиков;
* Присоединение устройства к рабочей области

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о том, как назначить пользователя администратором подписки Azure, см. в руководстве по [управлению доступом с помощью RBAC и портала Azure](../../role-based-access-control/role-assignments-portal.md)
* Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Дополнительные сведения о связи Azure Active Directory с подпиской Azure см. в статье [Связь между подписками Azure и Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
