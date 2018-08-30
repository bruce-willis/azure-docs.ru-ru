---
title: Использование Pig с Hadoop в HDInsight с помощью PowerShell в Azure
description: Узнайте, как отправлять задания Pig в кластер Hadoop в HDInsight с помощью Azure PowerShell.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: ff08e632c1bfd8eb4040e4e746ce08335eba8b08
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047380"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Использование Azure PowerShell для выполнения заданий Pig в HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования Azure PowerShell для отправки заданий Pig в Hadoop в кластере HDInsight. Pig позволяет написать задания MapReduce с использованием языка (Pig Latin), который моделирует преобразования данных, а не функции сопоставления и приведения.

> [!NOTE]
> В этом документе не приводится подробное описание процессов, которые выполняют операторы Pig Latin, используемые в примерах. Информацию об операторах Pig Latin, используемых в данном примере, см. в статье [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Предварительные требования

* **Кластер Azure HDInsight**.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Рабочая станция с Azure PowerShell**.

## <a id="powershell"></a>Выполнение задания Pig

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания Pig в HDInsight. Во внутренних процессах PowerShell использует вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat), выполняющемуся на кластере HDInsight.

При выполнении заданий Pig на удаленном кластере HDInsight используются следующие командлеты:

* **Connect-AzureRmAccount** — выполняет аутентификацию Azure PowerShell для подписки Azure.
* **New-AzureRmHDInsightPigJobDefinition** — создает *определение задания* с использованием заданных операторов Pig Latin.
* **Start-AzureRmHDInsightJob** — отправляет определение задания в HDInsight и запускает задание. Будет возвращен объект *задания*.
* **Wait-AzureRmHDInsightJob**— использует объект-задание для проверки состояния задания. Он ждет завершения задания или превышения времени ожидания.
* **Get-AzureRmHDInsightJobOutput** — используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight.

1. С помощью редактора сохраните следующий код как **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Откройте командную строку Windows PowerShell. Перейдите к расположению файла **pigjob.ps1** , а затем используйте следующую команду для запуска сценария:

        .\pigjob.ps1

    Вам будет предложено войти в свою подписку Azure. Затем вам будет предложено ввести данные HTTPS, имя и пароль учетной записи администратора для кластера HDInsight.

2. После завершения задания должна быть возвращена информация следующего вида.

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются при завершении задания, просмотрите журналы ошибок. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **pigjob.ps1** , сохраните его, а затем запустите снова.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Этот командлет возвращает информацию, которая записывалась в STDERR на сервере при обработке задания.

## <a id="summary"></a>Сводка
Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания Pig в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
