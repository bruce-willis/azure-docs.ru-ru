---
title: включение файла
description: включение файла
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765544"
---
| ИМЯ | Коммерческий URL-адрес | Государственный URL-адрес | ОПИСАНИЕ |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Используется для управления доступом и идентификаторами с помощью AAD |
| Azure Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Используется для передачи данных репликации и координации |
| Репликация | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Используется для операций управления репликацией и координации |
| Служба хранилища | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Используется для доступа к учетной записи хранения, в которой хранятся реплицируемые данные |
| Телеметрия (необязательно) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Используется для телеметрии |

``time.nist.gov`` и ``time.windows.com`` используются для проверки синхронизации времени системы с глобальным временем во всех развертываниях.


