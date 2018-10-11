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
ms.openlocfilehash: 07ee05128333df963c2d8ff3dd3f6442a3843d07
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48866896"
---
# <a name="delete-data-from-azure-data-explorer"></a>Удаление данных из обозревателя данных Azure

Обозреватель данных Azure поддерживает несколько методик массового удаления, которые рассматриваются в этой статье. Он не поддерживает удаление отдельных записей в режиме реального времени, так как оптимизирован для быстрого доступа на чтение.

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

Если вам нужна помощь в устранении проблем с удалением данных, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com).
