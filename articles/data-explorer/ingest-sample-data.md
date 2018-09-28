---
title: Прием демонстрационных данных в обозреватель данных Azure
description: Узнайте, как принять (загрузить) демонстрационные данные о погоде в обозреватель данных Azure.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7eb0e48a5b66775ac97ed0cab751db0ef367f667
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964621"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Прием демонстрационных данных в обозреватель данных Azure

В этой статье показано, как принять (загрузить) демонстрационные данные в базу данных обозревателя данных Azure. Существует [несколько способов приема данных](ingest-data-overview.md). В этой статье описан базовый метод, который подходит для тестирования.

> [!NOTE]
> У вас уже есть эти данные, если вы выполнили [Краткое руководство. Прием данных с помощью библиотеки Python в обозревателе данных Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Предварительные требования

[Тестовый кластер и база данных](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Прием данных

Набор демонстрационных данных **StormEvents** содержит данные о погоде из [Национальных центров Соединенных Штатов по экологической информации](https://www.ncdc.noaa.gov/stormevents/).

1. Войдите на портал [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. В левом верхнем углу приложения выберите **Добавить кластер**.

1. В диалоговом окне **Добавить кластер** введите URL-адрес кластера в форму `https://<ClusterName>.<Region>.kusto.windows.net/`, а затем выберите **Добавить**.

1. Вставьте следующую команду и выберите **Выполнить**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. После завершения приема вставьте следующий запрос, выберите запрос в окне и нажмите **Выполнить**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Запрос возвращает следующие результаты из принятых демонстрационных данных.

    ![Результаты запроса](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Дополнительная информация

[Написание запросов](write-queries.md)

[Прием данных в обозреватель данных Azure](ingest-data-overview.md)