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
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003954"
---
## <a name="storage"></a>служба хранилища.

|  |  |
|---------|---------|
| [Допустимые номера SKU для учетных записей хранения и виртуальных машин](../articles/governance/policy/samples/allowed-skus-storage.md) | Требует, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU. Для обеспечения утвержденных номеров SKU используются встроенные политики. Вы можете указать массив утвержденных номеров SKU виртуальных машин и учетных записей хранения. |
| [Допустимые SKU учетной записи хранения](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | Требует, чтобы учетные записи хранения использовали допустимые номера SKU. Нужно указать массив утвержденных номеров SKU. |
| [Запрет на распределение по холодным уровням доступа для учетных записей хранения](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Запрещает распределение по холодным уровням доступа для учетных записей хранения больших двоичных объектов.  |
| [Использование HTTPS-трафика для учетной записи хранения](../articles/governance/policy/samples/ensure-https-stor-acct.md) | Требует, чтобы учетные записи хранения использовали HTTPS-трафик.  |
| [Обеспечение шифрования файлов для хранилища](../articles/governance/policy/samples/ensure-store-file-enc.md) | Требует, чтобы для учетных записей хранения было включено шифрование файлов.  |
| [Требование шифрования учетной записи хранения](../articles/governance/policy/samples/req-store-acct-enc.md) | Требует, чтобы учетная запись хранения использовала шифрование больших двоичных объектов.  |