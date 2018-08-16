---
title: Привязка к управляемому домену доменных служб Azure AD с помощью защищенного протокола LDAP (LDAPS) | Документация Майкрософт
description: Привязка к управляемому домену доменных служб Azure AD с помощью защищенного протокола LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9728d42710ce44226363ea4954d83fcc3efbfb75
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502958"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Привязка к управляемому домену доменных служб Azure AD с помощью защищенного протокола LDAP (LDAPS)

## <a name="before-you-begin"></a>Перед началом работы
Выполните [задачу 4 — настройка DNS для доступа к управляемому домену через Интернет](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Задача 5. Привязка к управляемому домену по протоколу LDAP с помощью LDP.exe
Средство LDP.exe включено в пакет средств удаленного администрирования сервера, и вы можете использовать его для выполнения привязки и поиска по протоколу LDAP.

Сначала откройте LDP и подключитесь к управляемому домену. Щелкните **Подключение** и выберите в меню пункт **Подключиться**. Укажите имя домена DNS для управляемого домена. Укажите порт, который нужно использовать для подключений. Для подключений по протоколу LDAP используйте порт 389. Для подключений по протоколу LDAPS используйте порт 636. Нажмите кнопку **ОК**, чтобы подключиться к управляемому домену.

Затем выполните привязку к управляемому домену. Щелкните **Подключение** и выберите в меню пункт **Привязка**. Укажите данные для входа в учетную запись пользователя, принадлежащего к группе "Администраторы контроллера домена AAD".

Щелкните **Представление**, а затем выберите в меню пункт **Дерево**. Оставьте поле "Базовое DN" пустым и нажмите кнопку "OК". Перейдите в контейнер, в котором нужно выполнить поиск, щелкните его правой кнопкой мыши и выберите пункт "Поиск".

> [!TIP]
> - Сведения о пользователях и группах, синхронизированные с Azure AD, хранятся в контейнере **AADDC Users** (Пользователи контроллера домена AAD). Путь поиска для этого контейнера выглядит следующим образом: ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - Учетные записи компьютеров, присоединенных к управляемому домену, хранятся в контейнере **AADDC Computers** (Компьютеры контроллера домена AAD). Путь поиска для этого контейнера выглядит следующим образом: ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

Дополнительные сведения см. в статье [LDAP Query Basics](https://technet.microsoft.com/library/aa996205.aspx) (Основы запроса LDAP).


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Задача 6. Блокировка доступа по защищенному протоколу LDAP к управляемому домену через Интернет
> [!NOTE]
> Если вы не включали доступ к управляемому домену по протоколу LDAPS через Интернет, то пропустите эту задачу.
>
>

Прежде чем приступать к этой задаче, выполните шаги, описанные в [задаче 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Предоставление доступа к управляемому домену по протоколу LDAPS через Интернет представляет угрозу безопасности. Управляемый домен доступен через Интернет на том порту, который используется для защищенного протокола LDAP (то есть порт 636). Вы можете ограничить доступ к управляемому домену для определенных известных IP-адресов. Создайте группу безопасности сети (NSG) и свяжите ее с подсетью, для которой включены доменные службы Azure AD.

Пример группы NSG в следующей таблице блокирует доступ по защищенному протоколу LDAP через Интернет. Правила в NSG разрешают входящий доступ по защищенному протоколу LDAP через TCP-порт 636 только для указанного набора IP-адресов. Правило "DenyAll" по умолчанию применяется ко всему входящему трафику из Интернета. Правило группы безопасности сети, которое разрешает доступ по протоколу LDAPS через Интернет только с указанных IP-адресов, имеет более высокий приоритет по сравнению с правилом группы безопасности сети "DenyAll".

![Пример группы безопасности сети для доступа по защищенному протоколу LDAPS через Интернет](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Дополнительные сведения см. в статье** - [Фильтрация сетевого трафика с помощью групп безопасности сети](../virtual-network/security-overview.md).


## <a name="related-content"></a>Связанная информация
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP Query Basics](https://technet.microsoft.com/library/aa996205.aspx) (Основы запроса LDAP)
* [Administer Group Policy on an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-administer-group-policy.md) (Администрирование групповой политики в управляемом домене доменных служб Azure AD)
* [Группы безопасности сети](../virtual-network/security-overview.md)
* [Создание группы безопасности сети](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Дальнейшие действия
[Руководство по устранению неполадок с защищенным протоколом LDAP в управляемом домене](active-directory-ds-ldaps-troubleshoot.md)
