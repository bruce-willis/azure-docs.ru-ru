---
title: Устранение неполадок с защищенным протоколом LDAP (LDAPS) в доменных службах Azure AD | Документация Майкрософт
description: Устранение неполадок с защищенным протоколом LDAP (LDAPS) для управляемого домена доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9713a06bbf6a61b316e061cb851721a3554cd632
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503678"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Устранение неполадок с защищенным протоколом LDAP (LDAPS) для управляемого домена доменных служб Azure AD

## <a name="connection-issues"></a>Проблемы с подключением
При возникновении проблем с подключением к управляемому домену с использованием защищенного протокола LDAP сделайте следующее:

* Цепочка издателей сертификата защищенного протокола LDAP должна быть доверенной в клиенте. Для установления отношения доверия можно добавить корневой центр сертификации в хранилище доверенных корневых сертификатов на клиенте.
* Убедитесь, что клиент LDAP (например, ldp.exe) подключается к конечной точке защищенного протокола LDAP с помощью DNS-имени, а не IP-адреса.
* Проверьте имя DNS, к которому подключается клиент LDAP. Оно должно разрешаться в общедоступный IP-адрес для защищенного протокола LDAP в управляемом домене.
* Убедитесь, что сертификат защищенного протокола LDAP для управляемого домена содержит DNS-имя в атрибуте "Субъект" или "Альтернативное имя субъекта" (SAN).
* Параметры NSG для виртуальной сети должны разрешать поступление трафика из Интернета на порт 636. Это действие применяется, только если включен защищенный доступ по протоколу LDAP через Интернет.


## <a name="need-help"></a>Требуется помощь?
Если проблема с подключением к управляемому домену с использованием защищенного протокола LDAP сохранится, то [обратитесь за помощью к команде разработчиков](active-directory-ds-contact-us.md). Сообщите им приведенные ниже сведения, чтобы помочь с диагностикой проблемы.
* Снимок экрана, на котором ldp.exe пытается подключиться, но попытка завершается сбоем.
* Ваш идентификатор клиента Azure AD и доменное имя DNS вашего управляемого домена.
* Точное имя пользователя, которое вы пытаетесь привязать.


## <a name="related-content"></a>Связанная информация
* [Приступая к работе с доменными службами Azure AD](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP Query Basics](https://technet.microsoft.com/library/aa996205.aspx) (Основы запроса LDAP)
* [Administer Group Policy on an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-administer-group-policy.md) (Администрирование групповой политики в управляемом домене доменных служб Azure AD)
* [Группы безопасности сети](../virtual-network/security-overview.md)
* [Создание группы безопасности сети](../virtual-network/tutorial-filter-network-traffic.md)
