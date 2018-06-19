---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664706"
---
### <a name="sql-servers"></a>Серверы SQL Server

|  |  |
|---------|---------|
| [Проверка назначения роли администратора Azure Active Directory](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Выполняется проверка, назначена ли роль администратора Azure Active Directory в настройках сервера SQL. |
| [Аудит параметра обнаружения угроз на уровне сервера](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка параметров аудита SQL Server](../articles/azure-policy/scripts/sql-server-audit.md) | Проверяет SQL Server в зависимости от включения параметров аудита. |
| [Аудит параметра аудита уровня SQL Server](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Проверяет, соответствуют ли параметры аудита SQL Server указанному значению. Задайте значение, указывающее, должны ли быть включены параметры аудита. |
| [Требование наличия SQL Server версии 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Требует серверы SQL Server версии 12.0.  |