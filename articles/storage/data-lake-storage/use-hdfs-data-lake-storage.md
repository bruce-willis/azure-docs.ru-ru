---
title: Использование HDFS CLI в предварительной версии хранилища Azure Data Lake Storage Gen2
description: Общие сведения о HDFS CLI в хранилище Azure Data Lake Storage Gen2 (предварительная версия)
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c59331c772e140fccfefb89eef086a35837171e1
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576981"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Использование HDFS CLI в Data Lake Storage Gen2

Предварительная версия хранилища Azure Data Lake Storage Gen2 позволяет получать доступ к данным и управлять ими так же, как и в [распределенной файловой системе Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Если кластер HDInsight подключен или с помощью Azure Databricks выполняет задание Apache Spark по анализу данных, которые хранятся в Azure Data Lake Storage Gen2, вы можете использовать интерфейс командной строки (CLI) для извлечения и обработки загруженных данных. В остальной части статьи описываются параметры, которые вы можете использовать, пока [команда разработчиков службы хранилища Azure работает над добавлением поддержки Обозревателя службы хранилища Azure и портала Azure](https://azure.microsoft.com/roadmap/).

## <a name="hdfs-cli-with-hdinsight"></a>Использование HDFS CLI в HDInsight

HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Доступ к этой файловой системе можно получить с помощью оболочки, которая напрямую взаимодействует с HDFS и другими файловыми системами, поддерживаемыми Hadoop. Ниже приведен список стандартных команд и ссылки на полезные ресурсы.

>[!IMPORTANT]
>Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен (дополнительные сведения о том, [как выполнить удаление кластера](../../hdinsight/hdinsight-delete-cluster.md)). Тем не менее данные, размещенные в хранилище Azure Data Lake Storage Gen2, сохраняются даже после удаления кластера HDInsight.

Чтобы получить список файлов или папок:

    hdfs dfs -ls <args>
Чтобы создать каталог:

    hdfs dfs -mkdir [-p] <paths>
Чтобы удалить файлы или папки:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Теперь, в качестве примера, рассмотрим кластер HDInsight Hadoop в ОС Linux. Перед тем, как использовать HDFS CLI, необходимо установить [удаленный доступ к службам](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Если выбрать параметр [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), образец кода PowerShell будет выглядеть следующим образом.
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Строка подключения находится на портале Azure в колонке кластера HDInsight в разделе "SSH и вход в кластер". Учетные данные SSH были указаны во время создания кластера.

Дополнительные сведения о HDFS CLI можно узнать [на официальном сайте](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) или из [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Руководства по разрешениям в HDFS).

## <a name="hdfs-cli-with-azure-databricks"></a>Использование HDFS CLI в Azure Databricks

Интерфейс командной строки, предоставляемый Databricks, построен на базе Databricks REST API и его легко использовать. Проект с открытым исходным кодом размещается на сайте [GitHub](https://github.com/databricks/databricks-cli). Ниже приведен список стандартных команд.

Чтобы получить список файлов или папок:

    dbfs ls [-l]
Чтобы создать каталог:

    dbfs mkdirs
Чтобы удалить файл:

    dbfs rm [-r]

Одним из способом взаимодействия с Databricks является использование записных книжек. В то время как у записной книжки есть основной язык, у вас появляется возможность смешивать языки, указав magic-команду %language в начале ячейки. Команда %sh позволяет выполнять код оболочки в записной книжке. Похожий пример был приведен в этой статье ранее (в разделе о HDInsight).

Чтобы получить список файлов или папок:

    %sh ls <args>
Чтобы создать каталог:

    %sh mkdir [-p] <paths>
Чтобы удалить файлы или папки:

    %sh rm [-skipTrash] URI [URI ...]

Создайте новую записную книжку после запуска кластера Spark в Azure Databricks. Примеры сценария записной книжки приведены ниже.

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Дополнительные сведения о Databricks CLI можно узнать из [официальной документации](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Дополнительные сведения о записных книжках можно узнать из раздела [документации](https://docs.azuredatabricks.net/user-guide/notebooks/index.html).

## <a name="next-steps"></a>Дополнительная информация

- [Quickstart: Set up clusters in HDInsight](./quickstart-create-connect-hdi-cluster.md) (Краткое руководство по установке кластеров в HDInsight)
- [Краткое руководство. Запуск задания Spark в Azure Databricks с помощью портала Azure](./quickstart-create-databricks-account.md) 