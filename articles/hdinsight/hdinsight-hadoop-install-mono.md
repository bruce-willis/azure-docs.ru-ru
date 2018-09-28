---
title: Установка или обновление Mono в HDInsight в Azure
description: Сведения об использовании конкретной версии Mono с кластером HDInsight. Mono используется для запуска приложений .NET в кластерах HDInsight под управлением Linux.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.custom: hdinsightactive
ms.openlocfilehash: 54e5a5b72627dc9cd2d842ccb24d10e2f9ab9dce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957182"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Установка или обновление Mono в HDInsight

Сведения об установке конкретной версии [Mono](https://www.mono-project.com) в HDInsight версии 3.4 или выше.

Mono устанавливается на HDInsight версии 3.4 и выше и используется для запуска приложений .NET. Сведения о версии Mono, которая входит в состав каждой версии HDInsight, см. в статье [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md). Чтобы установить разные версии в кластере, используйте действия скрипта, рассматриваемые в этом документе. 

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующий параметр.

* __Номер версии Mono.__ Версия Mono, которую необходимо установить. Эта версия должна быть доступна по адресу [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

С помощью скрипта можно установить следующие пакеты Mono:

* __mono-complete__;

* __ca-certificates-mono__.

## <a name="the-script"></a>Сценарий

__Расположение скрипта__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Требования__

* Скрипт должен применяться к __головным узлам__ и __рабочим узлам__.

## <a name="to-use-the-script"></a>Использование скрипта

Сведения об использовании этого скрипта с HDInsight см. в документе [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Вы можете использовать скрипт с помощью портала Azure, Azure PowerShell или классического интерфейса командной строки Azure.

При выполнении документа со сведениями о действиях скрипта используйте следующий универсальный код ресурса (URI):

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Чтобы указать версию Mono для установки, введите номер версии в поле __Параметры__. Например, введите `5.4`, чтобы установить Mono 5.4.

> [!NOTE]
> Чтобы настроить HDInsight с помощью этого скрипта, пометьте скрипт как __сохраненный__. Этот параметр позволяет HDInsight применять скрипт к рабочим узлам, добавленным с помощью операций масштабирования.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как обновить или установить конкретную версию Mono в HDInsight. Дополнительные сведения об использовании приложений .NET в HDInsight см. в следующих статьях:

* [Использование языка C# для потоковой передачи MapReduce в Hadoop в HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md);
* [Использование определяемых пользователем функций C# при потоковой передаче Hive и Pig в Hadoop HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md);
* [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md);
* [Перенос решений .NET из HDInsight под управлением Windows в HDInsight под управлением Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

Дополнительные сведения об использовании действий скрипта см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
