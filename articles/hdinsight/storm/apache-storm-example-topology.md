---
title: Примеры топологий Apache Storm в Azure HDInsight
description: Список примеров топологий Storm, созданных и протестированных с помощью Apache Storm в HDInsight, включая базовые топологии на C# и Java, а также работа с концентраторами событий.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a07e4ec9fe0d47853a7ccefe337f2df9d3f85a79
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617692"
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Примеры топологий и компонентов Storm для Apache Storm в HDInsight

Ниже приведен список примеров для Apache Storm, разработанных и созданных корпорацией Майкрософт в HDInsight. Эти примеры охватывают целый ряд тем: от создания простейших топологий на C# и Java до использования служб Azure, таких как концентраторы событий, Cosmos DB, база данных SQL, HBase в HDInsight и служба хранилища Azure. Некоторые примеры также демонстрируют использование средств, не относящихся к Azure, и даже технологий, не связанных с корпорацией Майкрософт, таких как SignalR и Socket.IO.

| ОПИСАНИЕ | Что демонстрирует | Язык или платформа |
|:--- |:--- |:--- |
| [Запись в хранилище озера данных Azure из Apache Storm](apache-storm-write-data-lake-store.md) |Запись в хранилище озера данных Azure |Java |
| [Источник воронки и сита концентратора событий](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Источник воронки и сита концентратора событий |Java |
| [Создание топологии Apache Storm на языке Java][5797064f] |Maven |Java |
| [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio][16fce2d1] |Средства HDInsight для Visual Studio |C#, Java |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)][844d1d81] |концентраторы событий; |C# и Java |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |концентраторы событий; |Java |
| [Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight][246ee964] |Концентраторы событий, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| 
  [Извлечение, преобразование и загрузка данных из Центров событий Azure в HBase с помощью Storm в HDInsight][b4b68194] |Концентраторы событий, HBase |C# |
| [Template C# Storm topology project for working with Azure services from Storm on HDInsight][ce0c02a2] (Шаблон проекта топологии на C# и Storm для работы со службами Azure из Storm в HDInsight) |Концентраторы событий, Cosmos DB, база данных SQL, HBase, SignalR |C#, Java |
| [EventHubs scalability example (Java & SCP.Net Hybrid)][d6c540e3] (Пример масштабируемости концентраторов событий (Java и SCP.Net Hybrid)) |Скорость обработки сообщений, концентраторы событий, база данных SQL |C#, Java |
| [Использование Python со Storm в HDInsight](apache-storm-develop-python-topology.md) |Компоненты Python с топологией Flux |Python |
| [Использование Kafka со Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm: чтение и запись в Apache Kafka | Java |

> [!WARNING]
> Примеры C# в этом списке были изначально созданы и протестированы с помощью HDInsight под управлением Windows и могут неправильно работать с кластерами HDInsight под управлением Linux. Кластеры под управлением Linux используют Mono для выполнения кода .NET. Могут возникнуть проблемы совместимости с платформами и пакетами, используемыми в примере.
>
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий.

### <a name="next-steps"></a>Дальнейшие действия

* [Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux][2b8c3488]
* [Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Разработка топологий Storm на языке Java с использованием Maven путем создания простой топологии для подсчета статистики."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Считывание данных из концентраторов событий Azure и запись их туда с использованием Storm в HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Несколько топологий, демонстрирующих пропускную способность при считывании данных из концентраторов событий Azure и их записи в базу данных SQL с использованием средств Apache Storm в HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Узнайте, как считать данные из Центров событий Azure, а затем вычислить, преобразовать и сохранить эти данные в HBase в HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Этот проект содержит шаблоны воронок, сит и топологий, обеспечивающих взаимодействие с различными службами Azure, такими как концентраторы событий, Cosmos DB и база данных SQL."

