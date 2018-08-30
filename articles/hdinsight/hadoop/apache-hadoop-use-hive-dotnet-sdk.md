---
title: Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET в Azure
description: Узнайте, как отправлять задания Hadoop в Azure HDInsight Hadoop с помощью пакета SDK HDInsight для .NET.
ms.reviewer: jasonh
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: ac02e67791cde4d67f126da46c86896fb80ee1b9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049922"
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как отправлять запросы Hive с помощью пакета SDK HDInsight для .NET. Напишите программу на языке C#, чтобы отправить запрос Hive для перечисления таблиц Hive и отобразить результаты.

> [!NOTE]
> Действия, описанные в этой статье, необходимо выполнять из клиента Windows. Чтобы получить сведения об использовании клиента Linux, OS X или Unix для работы с Hive, воспользуйтесь выбором вкладок в верхней части статьи.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер Hadoop в HDInsight**. См. статью [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > Начиная с 15 сентября 2017 г. пакет SDK для HDInsight .NET поддерживает возвращение результатов запроса Hive только из учетных записей хранения Azure. Если использовать этот пример с кластером HDInsight, который использует Azure Data Lake Store в качестве основного хранилища, вы не сможете получить результаты поиска с помощью пакета SDK для .NET.

* **Visual Studio 2013, Visual Studio 2015, Visual Studio 2017**.

## <a name="run-a-hive-query"></a>Выполнение запроса Hive
Пакет SDK для HDInsight .NET содержит клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из .NET. 

**Отправка заданий**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Используйте следующий код:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Нажмите клавишу **F5** для запуска приложения.

Выходные данные приложения должны иметь следующий вид:

![Выходные данные задания Hadoop Hive в HDInsight](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Управление кластерами Hadoop в HDInsight с помощью портала Azure](../hdinsight-administer-use-management-portal.md)
* [Справочник по пакетам SDK HDInsight для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование Sqoop с HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


