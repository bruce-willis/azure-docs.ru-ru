---
title: Отмена и удаление задания импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как отменять и удалять задания службы импорта и экспорта Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e11cf974a5f33020c889844dd34f51612e13036e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521019"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Отмена и удаление заданий службы импорта и экспорта Azure

 Вы можете запросить отмену задания до того, как оно перейдет в состояние `Packaging`, вызвав операцию [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) и задав для элемента `CancelRequested` значение `true`. Задание будет отменено по мере возможности. Если диски используются для передачи данных, то передача данных может продолжаться даже после запроса отмены.

 Отмененное задание примет состояние `Completed` и будет храниться в течение 90 дней, после чего будет удалено.

 Чтобы удалить задание, вызовите операцию [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) перед доставкой задания (т. е. пока задание находится в состоянии `Creating`). Кроме того, задание можно удалить, когда оно находится в состоянии `Completed`. После удаления задания его данные и состояние станут недоступны через REST API или портал Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
