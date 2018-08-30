---
title: Миграция в средства Azure Resource Manager для HDInsight
description: Процесс перехода к средствам разработки на основе Azure Resource Manager для кластеров HDInsight
services: hdinsight
ms.reviewer: jasonh
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 1988593fa7cb0d84baffc4264147d350962bb6bc
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093073"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight

Средства для HDInsight на основе диспетчера служб Azure (ASM) устарели. Если для работы с кластерами HDInsight вы используете Azure PowerShell, интерфейс командной строки Azure или пакет SDK для HDInsight .NET, рекомендуем перейти на версии PowerShell, интерфейса командной строки и пакета SDK для .NET на основе Azure Resource Manager. В этой статье рассказывается, как перейти на средства, основанные на Azure Resource Manager. В этом документе везде, где это применимо, подчеркиваются различия между подходами ASM и Resource Manager для HDInsight.

> [!IMPORTANT]
> Поддержка PowerShell, интерфейса командной строки и пакета SDK для .NET на основе ASM будет прекращена **1 января 2017 года**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Переход с Azure CLI на диспетчер ресурсов Azure

> [!IMPORTANT]
> Azure CLI 2.0 не обеспечивает поддержку работы с кластерами HDInsight. Хотя Azure CLI 1.0 устарел, его все еще можно использовать для HDInsight.

Ниже приведены основные команды для работы с HDInsight посредством Azure CLI 1.0:

* `azure hdinsight cluster create` — создает кластер HDInsight;
* `azure hdinsight cluster delete` — удаляет кластер HDInsight;
* `azure hdinsight cluster show` — отображает информацию о существующем кластере;
* `azure hdinsight cluster list` — выдает список кластеров HDInsight для подписки Azure.

Проверить, какие параметры и переключатели доступны для каждой команды, позволяет переключатель `-h` .

### <a name="new-commands"></a>Новые команды
В диспетчере ресурсов Azure появились следующие команды:

* `azure hdinsight cluster resize` — динамически изменяет количество рабочих узлов в кластере;
* `azure hdinsight cluster enable-http-access` — обеспечивает HTTPs-доступ к кластеру (по умолчанию включен);
* `azure hdinsight cluster disable-http-access` — отключает HTTPs-доступ к кластеру;
* `azure hdinsight script-action` — предоставляет команды для создания действий сценариев в кластере и управления этими действиями;
* `azure hdinsight config` — предоставляет команды для создания файла конфигурации, который можно использовать с командой `hdinsight cluster create` для предоставления сведений о конфигурации.

### <a name="deprecated-commands"></a>Устаревшие команды
При использовании команд `azure hdinsight job` для отправки заданий в кластер HDInsight эти команды недоступны в списке команд диспетчера ресурсов. Если задания из сценариев необходимо отправлять в HDInsight программными средствами, используйте API REST, предоставляемый в HDInsight. Дополнительные сведения об отправке заданий с использованием API REST см. в следующих документах:

* [Выполнение заданий MapReduce с помощью cURL с использованием Hadoop в HDInsight](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Выполнение запросов Hive с помощью cURL с использованием Hadoop в HDInsight](hadoop/apache-hadoop-use-hive-curl.md)
* [Выполнение запросов Pig с помощью cURL с использованием Hadoop в HDInsight](hadoop/apache-hadoop-use-pig-curl.md)

Сведения о других способах интерактивного запуска MapReduce, Hive и Pig см. в статьях [Использование MapReduce в Hadoop в HDInsight](hadoop/hdinsight-use-mapreduce.md), [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md) и [Использование Pig с Hadoop в HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Примеры
**Создание кластера**

* Старая команда (ASM) — `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Новая команда — `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Удаление кластера**

* Старая команда (ASM) — `azure hdinsight cluster delete myhdicluster`
* Новая команда — `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Получение списка кластеров**

* Старая команда (ASM) — `azure hdinsight cluster list`
* Новая команда — `azure hdinsight cluster list`

> [!NOTE]
> Для команды списка при добавлении `-g` к группе ресурсов возвращаются только кластеры, входящие в указанную группу ресурсов.
> 
> 

**Отображение данных кластера**

* Старая команда (ASM) — `azure hdinsight cluster show myhdicluster`
* Новая команда — `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Переход с Azure PowerShell на диспетчер ресурсов Azure
Общие сведения об Azure PowerShell в режиме Azure Resource Manager (ARM) см. в статье [Управление ресурсами с помощью Azure PowerShell](../powershell-azure-resource-manager.md).

Командлеты диспетчера ресурсов Azure PowerShell могут устанавливаться параллельно с командлетами ASM. Командлеты двух режимов можно различать по именам.  Имена командлетов в режиме диспетчера ресурсов содержат *AzureRmHDInsight*, а в режиме ASM — *AzureHDInsight*.  Например, *New-AzureRmHDInsightCluster* или *New-AzureHDInsightCluster*. Параметры и переключатели могут иметь новые имена, а при использовании диспетчера ресурсов появляется доступ к множеству новых параметров.  Например, для некоторых командлетов требуется новый переключатель *-ResourceGroupName*. 

Перед использованием командлетов HDInsight необходимо подключиться к учетной записи Azure и создать новую группу ресурсов:

* Connect-AzureRmAccount или [Select-AzureRmProfile](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/select-azurermprofile?view=azuresmps-4.0.0). См. статью о [проверке подлинности субъекта-службы в Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Переименованные командлеты
Получение списка командлетов HDInsight ASM в консоли Windows PowerShell:

    help *azurermhdinsight*

В следующей таблице перечислены командлеты ASM и их имена в режиме диспетчера ресурсов:

| Командлеты ASM | Командлеты диспетчера ресурсов |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightconfigvalues?view=azurermps-6.6.0) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightmetastore?view=azurermps-6.6.0) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction?view=azurermps-6.6.0) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightstorage?view=azurermps-6.6.0) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjob?view=azurermps-6.6.0) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjoboutput?view=azurermps-6.6.0) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightproperties?view=azurermps-6.6.0) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/invoke-azurermhdinsighthivejob?view=azurermps-6.6.0) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightclusterconfig?view=azurermps-6.6.0) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsighthivejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightmapreducejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightpigjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightsqoopjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightstreamingmapreducejobdefinition?view=azurermps-6.6.0) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/remove-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize?view=azurermps-6.6.0) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightdefaultstorage?view=azurermps-6.6.0) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/start-azurermhdinsightjob?view=azurermps-6.6.0) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/stop-azurermhdinsightjob?view=azurermps-6.6.0) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/use-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/wait-azurermhdinsightjob?view=azurermps-6.6.0) |

### <a name="new-cmdlets"></a>Новые командлеты
Ниже перечислены новые командлеты, доступные только в режиме диспетчера ресурсов. 

**Командлеты, связанные с действиями сценариев:**

* **Get AzureRmHDInsightPersistedScriptAction**: возвращает список сохраняемых действий сценария для кластера, упорядоченный в хронологическом порядке, или получает сведения об указанном сохраняемом действии сценария. 
* **Get AzureRmHDInsightScriptActionHistory**: возвращает журнал действий сценария для кластера, перечисленных в обратном хронологическом порядке, или получает сведения о действии сценария, выполненном ранее. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: удаляет из кластера HDInsight сохраняемое действие сценария.
* **Set-AzureRmHDInsightPersistedScriptAction**: назначает выполненное ранее действие сценария сохраняемым действием сценария.
* **Submit-AzureRmHDInsightScriptAction**: отправляет новое действие сценария в кластер Azure HDInsight. 

Дополнительные сведения об использовании см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

**Командлеты, связанные с идентификатором кластера:**

* **Add-AzureRmHDInsightClusterIdentity**: добавляет идентификатор кластера в объект конфигурации кластера, чтобы кластер HDInsight мог получать доступ к Azure Data Lake Store. См. статью [Создание кластера HDInsight с хранилищем озера данных с помощью Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Примеры
**Создать кластер**

Старая команда (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Новая команда:

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Удалить кластер**

Старая команда (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Новая команда:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Список кластеров**

Старая команда (ASM):

    Get-AzureHDInsightCluster

Новая команда:

    Get-AzureRmHDInsightCluster 

**Отображение кластеров**

Старая команда (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Новая команда:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Другие примеры
* [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Отправка заданий Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Отправка заданий Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Отправка заданий Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Переход на новый пакет SDK для HDInsight .NET
[Пакет SDK для HDInsight .NET (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) на основе управления службами Azure устарел. Настоятельно рекомендуем использовать [пакет SDK для HDInsight .NET на основе диспетчера ресурсов](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) на базе Azure Resource Management. Следующие пакеты HDInsight на основе ASM устарели:

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Этот раздел содержит ссылки на дополнительные сведения о том, как выполнять определенные задачи, используя пакет SDK на основе диспетчера ресурсов.

| Инструкции по выполнению операций с помощью пакета SDK HDInsight на базе диспетчера ресурсов | Ссылки |
| --- | --- |
| Создание кластеров HDInsight с помощью пакета SDK для .NET |См. статью [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md). |
| Настройка кластера с помощью действия сценария и пакета SDK для .NET |См. статью [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action). |
| Интерактивная проверка подлинности приложений с Azure Active Directory c использованием пакета SDK для .NET |См. статью [Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Во фрагменте кода, представленном в этой статье, используется метод интерактивной проверки подлинности. |
| Неинтерактивная проверка подлинности приложений с Azure Active Directory c использованием пакета SDK для .NET |См. статью [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](hdinsight-create-non-interactive-authentication-dotnet-applications.md). |
| Отправка задания Hive с помощью пакета SDK для .NET |См. раздел [Отправка запросов Hive с помощью пакета SDK HDInsight для .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). |
| Отправка задания Pig с помощью пакета SDK для .NET |См. статью [Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight](hadoop/apache-hadoop-use-pig-dotnet-sdk.md). |
| Отправка задания Sqoop с помощью пакета SDK для .NET |См. статью [Выполнение заданий Sqoop с помощью пакета SDK для .NET для Hadoop в HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md). |
| Получение списка кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Получение списка кластеров](hdinsight-administer-use-dotnet-sdk.md#list-clusters). |
| Масштабирование кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Масштабирование кластеров](hdinsight-administer-use-dotnet-sdk.md#scale-clusters). |
| Предоставление и отмена доступа к кластерам HDInsight с помощью пакета SDK для .NET |См. раздел [Предоставление и отмена доступа](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access). |
| Обновление учетных данных пользователя HTTP для кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Обновление учетных данных пользователя HTTP](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials). |
| Поиск учетной записи хранения по умолчанию для кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Поиск учетной записи хранения по умолчанию](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account). |
| Удаление кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Удаление кластеров](hdinsight-administer-use-dotnet-sdk.md#delete-clusters). |

### <a name="examples"></a>Примеры
Ниже представлены некоторые примеры выполнения операций с использованием пакета SDK на основе ASM и фрагмент аналогичного кода для пакета SDK на основе диспетчера ресурсов.

**Создание CRUD-клиента кластера**

* Старая команда (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Новая команда (авторизация субъекта-службы)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Новая команда (авторизация пользователя)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Создание кластера**

* Старая команда (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Новая команда
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Включение доступа по протоколу HTTP**

* Старая команда (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Новая команда
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Удаление кластера**

* Старая команда (ASM)
  
        client.DeleteCluster(dnsName);
* Новая команда
  
        client.Clusters.Delete(resourceGroup, dnsname);

