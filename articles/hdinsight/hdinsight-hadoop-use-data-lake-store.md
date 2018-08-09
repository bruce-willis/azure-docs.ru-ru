---
title: Использование Data Lake Store с Hadoop в Azure HDInsight
description: Узнайте, как запрашивать данные из Azure Data Lake Store и сохранять результаты анализа.
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 3f72e18fbf0f3796d85b4acfb74223b6bea24c6e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591017"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Использование Data Lake Store с кластерами Azure HDInsight

Для анализа данных в кластере HDInsight можно сохранить данные в [службе хранилища Azure](../storage/common/storage-introduction.md), в [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) или в обоих этих хранилищах. Оба варианта хранилища позволяют безопасно и без потери пользовательских данных удалять используемые для расчетов кластеры HDInsight.

Из этой статьи вы узнаете, как Data Lake Store работает с кластерами HDInsight. Дополнительные сведения о работе службы хранилища Azure с кластерами HDInsight см. в разделе [Использование службы хранилища Azure с кластерами Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Дополнительные сведения о создании кластера HDInsight см. в статье [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> Доступ к Data Lake Store всегда осуществляется по безопасному каналу, поэтому никогда не применяется имя схемы файловой системы `adls`. Всегда используется `adl`.
> 


## <a name="availability-for-hdinsight-clusters"></a>Сведения о доступности кластеров HDInsight

В Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает использование центра сертификации и схемы по умолчанию. Она также может использоваться для разрешения относительных путей. При создании кластера HDInsight в качестве используемой по умолчанию файловой системы можно указать контейнер больших двоичных объектов в службе хранилища Azure, а в случае использования HDInsight 3.5 и более поздних версий — выбрать службу хранилища Azure или Azure Data Lake Store с некоторыми исключениями. 

Есть два способа использования Data Lake Store с кластерами HDInsight:

* в качестве хранилища по умолчанию;
* в качестве дополнительного хранилища (при этом в качестве хранилища по умолчанию используется Azure Storage Blob).

Сейчас только некоторые типы и версии кластеров HDInsight поддерживают использование Data Lake Store в качестве хранилища по умолчанию и для дополнительных учетных записей хранения:

| Тип кластера HDInsight | Data Lake Store как хранилище по умолчанию | Data Lake Store как дополнительное хранилище| Примечания |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight версии 3.6 | Yes | Yes | |
| HDInsight версии 3.5 | Yes | Yes | За исключением HBase|
| HDInsight версия 3.4 | Нет  | Yes | |
| HDInsight версии 3.3 | Нет  | Нет  | |
| HDInsight версии 3.2 | Нет  | Yes | |
| Storm | | |Data Lake Store можно использовать для записи данных из топологии Storm. Data Lake Store также может использоваться для хранения эталонных данных, которые затем можно будет считать с помощью топологии Storm.|

Использование Data Lake Store в качестве дополнительной учетной записи хранения не влияет на производительность либо возможность выполнять чтение или запись в хранилище Azure из кластера.


## <a name="use-data-lake-store-as-default-storage"></a>Использование Data Lake Store в качестве хранилища по умолчанию

При развертывании HDInsight с Data Lake Store в качестве хранилища по умолчанию относящиеся к кластеру файлы сохраняются в хранилище Data Lake Store в следующем расположении:

    adl://mydatalakestore/<cluster_root_path>/

где `<cluster_root_path>` — это имя папки, которую вы создаете в Data Lake Store. Указав корневую папку для каждого кластера, вы можете использовать одну и ту же учетную запись Data Lake Store для нескольких кластеров. Таким образом, у вас могут получиться такие настройки:

* Cluster1 использует путь `adl://mydatalakestore/cluster1storage`;
* Cluster2 использует путь `adl://mydatalakestore/cluster2storage`.

Обратите внимание, что оба кластера используют одну и ту же учетную запись Data Lake Store — **mydatalakestore**. Каждый кластер имеет доступ к собственный корневой файловой системе в Data Lake Store. В частности, при развертывании на портале Azure в качестве корневого пути предлагается использовать имя папки, например **/clusters/\<clustername>**.

Чтобы использовать Data Lake Store как хранилище по умолчанию, необходимо предоставить субъекту-службе доступ к таким объектам:

- Корень учетной записи Data Lake Store.  Например: adl://mydatalakestore/.
- Папка для всех папок кластера.  Например: adl://mydatalakestore/clusters.
- Папка для кластера.  Например: adl://mydatalakestore/clusters/cluster1storage.

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе [Настройка доступа к Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Использование Data Lake Store в качестве дополнительного хранилища

Data Lake Store также можно использовать в качестве дополнительного хранилища кластера. В таких случаях хранилищем кластера по умолчанию может быть Azure Storage Blob или учетная запись Data Lake Store. При выполнении заданий HDInsight с применением данных, хранящихся в Data Lake Store как дополнительном хранилище, необходимо использовать полный путь к файлам. Например: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Обратите внимание, что в этом URL-адресе нет **cluster_root_path**. Это объясняется тем, что в данном случае Data Lake Store не является хранилищем по умолчанию, поэтому требуется просто указать путь к файлам.

Чтобы использовать Data Lake Store как дополнительное хранилище, достаточно предоставить субъекту-службе доступ к расположениям, в которых хранятся файлы.  Например: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе [Настройка доступа к Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Использование нескольких учетных записей Data Lake Store

Можно добавить учетную запись Data Lake Store в качестве дополнительного хранилища и несколько учетных записей Data Lake Store, предоставив кластеру HDInsight разрешение на доступ к данным в одной или нескольких учетных записях Data Lake Store. См. раздел [Настройка доступа к Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Настройка доступа к Data Lake Store

Чтобы настроить доступ к Data Lake Store из кластера HDInsight, требуется субъект-служба Azure Active Directory (AAD). Создать субъект-службу может только администратор Azure AD. Для создания субъекта-службы необходим сертификат. Дополнительные сведения см. в разделах [Краткое руководство. Настройка кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) и [Создание субъекта-службы с самозаверяющим сертификатом](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Если вы собираетесь добавить Azure Data Lake Store в качестве дополнительного хранилища для кластера HDInsight, настоятельно рекомендуется сделать это во время создания кластера, как описано в этой статье. Сценарий с добавлением Azure Data Lake Store в качестве дополнительного хранилища для существующего кластера HDInsight не поддерживается.
>

## <a name="access-files-from-the-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Store из кластера HDInsight.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Используя сокращенный формат пути**. При таком подходе путь до корня кластера заменяется на adl:///. Таким образом, в приведенном выше примере `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` можно заменить на `adl:///`.

        adl:///<file path>

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ. Например, если полный путь к файлу —

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    то получить доступ к этому же файлу sample.log можно с помощью относительного пути:

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Создание кластеров HDInsight с доступом к Data Lake Store

Перейдите по указанным ниже ссылкам, чтобы просмотреть подробные инструкции о создании кластеров HDInsight с доступом к Data Lake Store.

* [Использование портала](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Создание кластера HDInsight с Data Lake Store (как хранилище по умолчанию) с помощью Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Создание кластера HDInsight с Data Lake Store (как дополнительное хранилище) с помощью Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Создание кластера HDInsight с Data Lake Store с помощью шаблона Azure Resource Manager](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Обновление сертификата HDInsight для получения доступа к Data Lake Store

Следующий пример кода PowerShell считывает локальный файл сертификата и обновляет кластер HDInsight с помощью нового сертификата для доступа к Azure Data Lake Store. Укажите собственные имена кластера HDInsight и группы ресурсов, идентификатор подписки и приложения, а также локальный путь к сертификату. Когда отобразится соответствующий запрос, введите пароль.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId
Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как использовать HDFS-совместимую службу Azure Data Lake Store с HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования, а также использовать HDInsight для разблокирования информации внутри хранимых структурированных и неструктурированных данных.

Дополнительные сведения можно найти в разделе 

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Краткое руководство по установке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
