---
title: Рекомендации по обработке ошибок службы "Функции Azure" | Документация Майкрософт
description: Содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций, а также ссылки на разделы об ошибках, связанных с привязкой.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091847"
---
# <a name="azure-functions-error-handling"></a>Обработка ошибок службы "Функции Azure"

Этот раздел содержит общие рекомендации по обработке ошибок, возникающих в ходе выполнения функций. Он также содержит ссылки на разделы, в которых описываются ошибки привязки, которые могут возникнуть. 

## <a name="handing-errors-in-functions"></a>Обработка ошибок в функциях
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Коды ошибок привязки

При интеграции со службами Azure могут возникать ошибки, поступающие из интерфейсов API базовых служб. Ссылки на документацию по кодам ошибок для этих служб можно найти в подразделах **Исключения и коды возврата** следующих справочных разделов, посвященных триггерам и привязкам.

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Хранилище BLOB-объектов](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Центры событий](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Центры уведомлений](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Хранилище очередей](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Служебная шина](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Хранилище таблиц](functions-bindings-storage-table.md#exceptions-and-return-codes)
