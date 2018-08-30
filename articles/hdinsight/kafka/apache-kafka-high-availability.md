---
title: Обеспечение высокого уровня доступности с помощью Apache Kafka в Azure HDInsight
description: Узнайте, как обеспечить высокий уровень доступности с помощью Apache Kafka в Azure HDInsight. Узнайте, как перераспределять реплики секций в Kafka, чтобы в случае сбоя они находились в разных доменах в регионе Azure, в котором размещена служба HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: bd3b02d54e0a65411e45f0422a0d245645d59096
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049977"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Обеспечение высокого уровня доступности данных с помощью Apache Kafka в HDInsight

Узнайте, как настроить реплики секций для разделов Kafka, чтобы воспользоваться преимуществами конфигураций базовых аппаратных стоек. Эта конфигурация обеспечивает доступность данных, хранящихся в Apache Kafka в HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Домены сбоя и обновления с Kafka

Домен сбоя — это логическое объединение базового оборудования в центре обработки данных Azure. Все домены сбоя используют общий источник питания и сетевой коммутатор. Виртуальные машины и управляемые диски, на которых реализуются узлы в кластере HDInsight, распределяются по этим доменам сбоя. Такая архитектура ограничивает потенциальное влияние сбоев физического оборудования.

В каждом регионе Azure есть определенное количество доменов сбоя. Список доменов и количество доменов сбоя в них см. в документации [о группах доступности](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> В Kafka нет сведений о доменах сбоя. При создании раздела в Kafka все реплики секций могут храниться в одном домене сбоя. Чтобы решить эту проблему, HDInsight предоставляет [средство перераспределения секций Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Когда следует перераспределять реплики секций?

Чтобы обеспечить максимально высокий уровень доступности данных Kafka, следует перераспределять реплики секций для раздела в следующих случаях:

* при создании раздела или секции;

* при масштабировании кластера.

## <a name="replication-factor"></a>Коэффициент репликации

> [!IMPORTANT]
> Мы рекомендуем использовать регион Azure с тремя доменами сбоя и коэффициент репликации 3.

Если необходимо указать регион с двумя доменами сбоя, используйте коэффициент репликации 4, чтобы равномерно распределить реплики на этих доменах.

Примеры создания разделов и настройки коэффициента репликации см. в статье [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Как перераспределять реплики секций?

Воспользуйтесь [средством перераспределения секций Kafka](https://github.com/hdinsight/hdinsight-kafka-tools), чтобы перераспределить выбранные разделы. Это средство следует запускать из сеанса SSH на главном узле кластера Kafka.

Дополнительные сведения о подключении к HDInsight с помощью SSH см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Дополнительная информация

* [Масштабируемость Kafka в HDInsight](apache-kafka-scalability.md)
* [Зеркальное отображение Kafka в HDInsight](apache-kafka-mirroring.md)
