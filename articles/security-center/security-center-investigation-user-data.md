---
title: Управление данными пользователя, находящимися в анализе центра безопасности Azure | Документация Майкрософт
description: " Сведения об управлении данными пользователя, находящимися в анализе центра безопасности Azure. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: d3b5f0ee4aa656fa66d577aa40f66fc98dff148d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301656"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Управление данными пользователя, находящимися в анализе центра безопасности Azure
В этой статье содержатся сведения об управлении данными пользователя, находящимися в анализе центра безопасности Azure. Данные анализа хранятся в [Azure Log Analytics](../log-analytics/log-analytics-overview.md) и отображаются в центре безопасности. Управление данными пользователя включает в себя возможность удаления и экспорта данных.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Поиск персональных данных и их идентификация
На портале Azure можно использовать [функцию анализа](../security-center/security-center-investigation.md) центра безопасности, чтобы осуществлять поиск персональных данных. Функция анализа доступна в разделе **Оповещения системы безопасности**.

Все сущности, сведения о пользователе и данные отображаются на вкладке **Сущности**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Защита и контроль доступа к персональным данным
Пользователь центра безопасности Azure, которому назначена роль читателя, владельца, участника или администратора учетной записи, может получить доступ к данным клиента в рамках этого центра.

Дополнительные сведения о ролях читателя, владельца и участника см. в статье [Встроенные роли управления доступом на основе ролей в Azure](../role-based-access-control/built-in-roles.md). Дополнительные сведения о роли администратора учетной записи см. в статье [Добавление или изменение администраторов подписки Azure](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="deleting-personal-data"></a>Удаление персональных данных
Пользователи центра безопасности, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут удалять сведения об анализе.

Чтобы удалить анализ, можно отправить запрос `DELETE` в REST API Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Входные данные `incidentName` можно найти, получив список всех инцидентов с помощью запроса `GET`.

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Экспорт персональных данных
Пользователи центра безопасности, которым назначены роли читателя, владельца, участника или администратора учетной записи, могут экспортировать сведения об анализе. Чтобы экспортировать сведения об анализе, перейдите на вкладку **Сущности**, где можно скопировать и вставить соответствующие сведения.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об управлении данными пользователя в центре безопасности Azure см. в [этой статье](security-center-privacy.md).
