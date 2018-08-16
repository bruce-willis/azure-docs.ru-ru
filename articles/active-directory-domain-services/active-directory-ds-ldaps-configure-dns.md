---
title: Настройка DNS для доступа к управляемому домену через Интернет по протоколу LDAPS | Документация Майкрософт
description: Настройка DNS для доступа к управляемому домену доменных служб Azure AD через Интернет по протоколу LDAPS
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 669e0392cb77434c372c9af3c4d467d19cff8abd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501740"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Настройка DNS для доступа к управляемому домену доменных служб Azure AD по защищенному протоколу LDAP (LDAPS)

## <a name="before-you-begin"></a>Перед началом работы
Выполните действия, описанные в статье [Настройка защищенного протокола LDAP (LDAPS) для управляемого домена доменных служб Azure AD](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Задача 4. Настройка DNS для доступа к управляемому домену через Интернет
> [!TIP]
> **Необязательная задача**. Пропустите эту задачу настройки, если вы не планируете получать доступ к управляемому домену по протоколу LDAPS через Интернет.
>
>

Прежде чем приступать к этой задаче, выполните шаги, описанные в [задаче 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

После настройки доступа к управляемому домену через Интернет по защищенному протоколу LDAP нужно обновить DNS, чтобы клиентские компьютеры могли найти этот управляемый домен. На вкладке **Свойства** в поле **Внешний IP-адрес для доступа LDAPS** отображается соответствующий IP-адрес.

Настройте внешний поставщик DNS, чтобы DNS-имя управляемого домена (например, ldaps.contoso100.com) указывало на этот внешний IP-адрес. Например, создайте следующую запись DNS:

    ldaps.contoso100.com  -> 52.165.38.113

Вот и все! Теперь вы можете подключиться к управляемому домену через Интернет по защищенному протоколу LDAP.

> [!WARNING]
> Помните, что клиентские компьютеры должны доверять издателю сертификата LDAPS, чтобы иметь возможность подключиться к управляемому домену по протоколу LDAPS. Если вы используете общедоступный доверенный центр сертификации, то никаких действий не потребуются, так как клиентские компьютеры доверяют этим издателям сертификатов. Если вы используете самозаверяющий сертификат, то на клиентском компьютере в хранилище доверенных сертификатов понадобится установить открытую часть самозаверяющего сертификата.
>
>

## <a name="next-step"></a>Дальнейшие действия
[Задача 5. Привязка к управляемому домену и блокировка доступа по защищенному протоколу LDAP](active-directory-ds-ldaps-bind-lockdown.md)
