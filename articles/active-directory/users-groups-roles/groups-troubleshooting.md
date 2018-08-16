---
title: Устранение неполадок динамического членства в группах | Документация Майкрософт
description: Советы по устранению неполадок динамического членства в группах в Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82a5c57ce874e77a7912945a6fca07acee339197
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444493"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Устранение неполадок, связанных с динамическим членством в группах

**Мной было настроено правило для группы, но обновление членства в группе не произошло.**<br/>Проверьте значения атрибутов пользователей в правиле — существуют ли пользователи, удовлетворяющие правилу? Если все правильно, подождите некоторое время, пока не заполнится группа. В зависимости от размера клиента, первоначальное заполнение группы или ее заполнение после изменения правила может занять до 24 часов.

**Мной было настроено правило, но теперь существующие участники правила удалены.**<br/>Это ожидаемое поведение. Существующие участники группы удаляются при включении или изменении правила. В группу добавляются пользователи, подобранные на основе оценки правила.

**Я не вижу мгновенного изменения членства, когда добавляю или изменяю правило. Почему?**<br/>Специальная оценка членства выполняется периодически в асинхронном фоновом процессе. На длительность процесса влияют количество пользователей в каталоге и размер группы, созданной в результате оценки правила. Обычно каталоги с небольшим количеством пользователей выявят изменения членства в группах уже через несколько минут. В случае каталогов с большим числом пользователей заполнение может занять 30 минут или больше.

**У меня возникла ошибка при обработке правила**<br/>В следующей таблице перечислены распространенные ошибки с правилом динамического членства и способы их исправления.

| Ошибка со средством синтаксического анализа правил | Неправильное использование | Правильное использование |
| --- | --- | --- |
| Ошибка: атрибут не поддерживается. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Убедитесь, что атрибут находится в [списке поддерживаемых свойств](groups-dynamic-membership.md#supported-properties). |
| Ошибка: не поддерживается оператор для атрибута. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/><br/>Используемый оператор не поддерживается для этого типа свойств (в данном примере -contains не может использоваться в логическом типе). Используйте правильные операторы для типа свойств. |
| Ошибка: ошибка компиляции запроса. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Отсутствует оператор. Используйте предикат соединения -and или -or.<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Ошибка в регулярном выражении, используемом с -match.<br>(user.userPrincipalName -match ".*@domain.ext")<br>или (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Дополнительная информация

В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](../active-directory-apps-index.md)
* [Что такое Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](../connect/active-directory-aadconnect.md)
