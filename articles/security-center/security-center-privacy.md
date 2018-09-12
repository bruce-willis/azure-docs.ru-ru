---
title: Управление данными пользователя в центре безопасности Azure | Документация Майкрософт
description: " Сведения об управлении данными пользователя в центре безопасности Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: fcaac3d248b676e4b7b1fe0344b54e52ce1fb558
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302679"
---
# <a name="manage-user-data-in-azure-security-center"></a>Управление данными пользователя в центре безопасности Azure
В этой статье приводятся сведения об управлении данными пользователя в центре безопасности Azure. Управление данными пользователя включает в себя возможность доступа, удаления и экспорта данных.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к данным клиента в рамках этого центра. Дополнительные сведения о ролях читателя, владельца и участника см. в статье [Встроенные роли управления доступом на основе ролей в Azure](../role-based-access-control/built-in-roles.md). Дополнительные сведения о роли администратора учетной записи см. в статье [Добавление или изменение администраторов подписки Azure](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Поиск персональных данных и их идентификация
Пользователи Центра безопасности Azure могут просматривать свои персональные данные через портал Azure. В Центре безопасности Azure хранятся только сведения о контактных лицах по вопросам безопасности, такие как адреса электронной почты и номера телефонов. Дополнительные сведения см. в статье [Предоставление сведений о контактных лицах по вопросам безопасности в центре безопасности Azure](security-center-provide-security-contact-details.md).

На портале Azure пользователь может просмотреть разрешенные IP-конфигурации с помощью функции JIT-доступа к виртуальным машинам в Центре безопасности Azure. Дополнительные сведения см. в статье об [управлении доступом к виртуальным машинам с помощью JIT](security-center-just-in-time.md).

На портале Azure пользователь может просмотреть оповещения безопасности, предоставляемые Центром безопасности Azure, включая сведения об IP-адресах и злоумышленниках. Дополнительные сведения см. в статье [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Классификация персональных данных
Вам не нужно классифицировать персональные данные, сохраненные в функции для контактного лица по вопросам безопасности в Центре безопасности Azure. Это адрес электронной почты (или несколько адресов) и номер телефона. [Контактные данные](security-center-provide-security-contact-details.md) проверяются Центром безопасности Azure.

Вам не нужно классифицировать IP-адреса и номера портов, сохраненные с помощью функции [JIT](security-center-just-in-time.md) в Центре безопасности Azure.

Только пользователь, которому назначена роль администратора, может классифицировать персональные данные, перейдя в окно [просмотра оповещений](security-center-managing-and-responding-alerts.md) в Центре безопасности Azure.

## <a name="securing-and-controlling-access-to-personal-data"></a>Защита доступа к персональным данным и управлением им
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к [данным контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md).

Пользователи Центра безопасности Azure, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут получить доступ к своим политикам [JIT](security-center-just-in-time.md).

Пользователи Центра безопасности Azure, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут просматривать [оповещения](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Обновление персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может обновить [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) на портале Azure.

Пользователи Центра безопасности Azure, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут обновить свои политики [JIT](security-center-just-in-time.md).

Администратор учетной записи не может изменить инциденты с оповещениями. [Инцидент с оповещением](security-center-managing-and-responding-alerts.md) относится к данным безопасности и предназначен только для чтения.

## <a name="deleting-personal-data"></a>Удаление персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может удалить [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) через портал Azure.

Пользователь Центра безопасности Azure, которому назначена роль владельца, участника или администратора учетной записи, может удалить политики [JIT](security-center-just-in-time.md) на портале Azure.

Пользователь Центра безопасности Azure не может удалить инциденты с оповещениями. Из-за требований безопасности [инцидент с оповещением](security-center-managing-and-responding-alerts.md) предназначен только для чтения.

## <a name="exporting-personal-data"></a>Экспорт персональных данных
Пользователь Центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может экспортировать [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md) следующим образом:

- выполнив действие копирования на портале Azure;
- вызвав Azure REST API и используя метод GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

Пользователь Центра безопасности Azure, которому назначена роль администратора учетной записи, может экспортировать [политики JIT](security-center-just-in-time.md), содержащие IP-адрес, следующим образом:

- выполнив действие копирования на портале Azure;
- вызвав Azure REST API и используя метод GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Администратор учетной записи может экспортировать сведения об оповещении следующим образом:

- выполнив действие копирования на портале Azure;
- вызвав Azure REST API и используя метод GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Дополнительные сведения см. в статье о [получении оповещений безопасности](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ограничение использования персональных данных для профилирования или маркетинга без согласия
Пользователь Центра безопасности Azure может отказаться, удалив [данные контактного лица по вопросам безопасности](security-center-provide-security-contact-details.md).

[Данные JIT](security-center-just-in-time.md) считаются не идентифицируемыми данными и хранятся в течение 30 дней.

[Данные об оповещении](security-center-managing-and-responding-alerts.md) считаются данными безопасности и хранятся в течение двух лет.

## <a name="auditing-and-reporting"></a>Аудит и создание отчетов
Журналы аудита контактного лица по вопросам безопасности, данные JIT и обновления оповещений хранятся в [журналах действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об управлении данными пользователя см. в статье об [управлении данными пользователя, обнаруженными в исследовании центра безопасности Azure](security-center-investigation-user-data.md).
