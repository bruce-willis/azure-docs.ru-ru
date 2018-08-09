---
title: Архивация манифестов дисков для импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как настроить автоматическую архивацию манифестов диска для службы импорта и экспорта Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 933c0121a4f718ff812fc921bd6e04983fc69931
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520543"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Архивация манифестов дисков для заданий импорта и экспорта Azure

Манифесты диска можно архивировать автоматически в большие двоичные объекты, задав значение `true` для свойства `BackupDriveManifest` при операции REST API [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) или [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). По умолчанию манифесты диска не архивируются. Резервные копии манифестов диска хранятся в виде блочных BLOB-объектов в контейнере, расположенном в учетной записи хранения, которая связана с заданием. По умолчанию контейнер называется `waimportexport`, но при вызове операции `Put Job` или `Update Job Properties` в свойстве `DiagnosticsPath` можно указать другое имя. Имя большого двоичного объекта с резервными копиями манифестов имеет следующий формат: `waies/jobname_driveid_timestamp_manifest.xml`.

 Универсальный код ресурса (URI) резервных копий манифестов диска для задания можно получить, вызвав операцию [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Универсальный код ресурса (URI) большого двоичного объекта возвращается в свойство `ManifestUri` для каждого диска.

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
