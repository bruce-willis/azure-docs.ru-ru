---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003913"
---
### <a name="sql-servers"></a>Серверы SQL Server

|  |  |
|---------|---------|
| [Проверка назначения роли администратора Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Выполняется проверка, назначена ли роль администратора Azure Active Directory в настройках сервера SQL. |
| [Аудит параметра обнаружения угроз на уровне сервера](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка параметров аудита SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Проверяет SQL Server в зависимости от включения параметров аудита. |
| [Аудит параметра аудита уровня SQL Server](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Проверяет, соответствуют ли параметры аудита SQL Server указанному значению. Задайте значение, указывающее, должны ли быть включены параметры аудита. |
| [Требование наличия SQL Server версии 12.0](../articles/governance/policy/samples/req-sql-12.md) | Требует серверы SQL Server версии 12.0.  |