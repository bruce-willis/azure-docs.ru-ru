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
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003933"
---
### <a name="sql-databases"></a>Базы данных SQL

|  |  |
|---------|---------|
| [Допустимые номера SKU базы данных SQL](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Требует, чтобы базы данных SQL использовали утвержденные номера SKU. Вы можете указать массив допустимых идентификаторов или названий SKU. |
| [Аудит параметра обнаружения угроз на уровне базы данных](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка шифрования базы данных SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Проверяет, включено ли прозрачное шифрование данных для базы данных SQL. |
| [Аудит параметров аудита уровня базы данных SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Проверяет, соответствуют ли заданной настройке параметры аудита базы данных SQL. Задайте значение, указывающее, должны ли быть включены параметры аудита.  |
| [Проверка состояния прозрачного шифрования данных](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Проверяет, включено ли прозрачное шифрование данных для базы данных SQL.  |