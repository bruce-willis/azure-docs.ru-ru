---
title: Удаление данных из обозревателя данных Azure
description: В этой статье описаны сценарии массового удаления данных в обозревателе данных Azure, включая очистку и удаление на основе срока хранения.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046898"
---
# <a name="delete-data-from-azure-data-explorer"></a>Удаление данных из обозревателя данных Azure

Обозреватель данных Azure поддерживает несколько методик массового удаления, которые рассматриваются в этой статье. Он не поддерживает удаление отдельных записей в режиме реального времени, так как оптимизирован для быстрого доступа на чтение.

* Если база данных больше не нужна, удалите ее с помощью команды drop database.

    ```Kusto
    .drop database <DatabaseName>
    ```

* Если не нужна одна или несколько таблиц, удалите их с помощью команды drop table или drop tables.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Если хранить старые данные больше не требуется, удалите их изменив срок хранения на уровне базы данных или таблицы.

    Рассмотрим базу данных или таблицу со сроком хранения в 90 дней. Потребности компании изменились, так что теперь требуется хранить данные только в течение 60 дней. В этом случае можно удалить устаревшие данные одним из следующих способов.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Дополнительные сведения см. в статье [Retention policy](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy) (Политика хранения).

* Можно удалять отдельные записи с помощью операции *purge* в зависимости от предиката, например `where CustomerName == 'contoso'`. С другой стороны, purge — это операция массового удаления, она не предназначена для удаления в режиме реального времени. Ниже приведен пример использования purge.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Если вам нужна помощь в устранении проблем с удалением данных, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com).