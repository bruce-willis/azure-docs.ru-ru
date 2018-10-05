---
title: Пакет SDK Go для Azure HDInsight
description: Справочник по пакету SDK Go для HDInsight
services: hdinsight
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 9/21/2018
ms.author: tyfox
ms.openlocfilehash: bb7cdbc340c6e9763277d5cdacc0cfb510fdc0db
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047047"
---
# <a name="hdinsight-go-management-sdk-preview"></a>Предварительная версия пакета SDK Go для HDInsight

## <a name="overview"></a>Обзор
Пакет SDK Go для HDInsight предоставляет классы и функции для управления кластерами HDInsight. Пакет также поддерживает операции создания, удаления, обновления, получения списков, масштабирования, выполнения скриптов, мониторинга, получения свойства кластеров HDInsight и др.

> [!NOTE]
>Справочные материалы GoDoc для этого пакета SDK также можно найти [здесь](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure. Если у вас ее нет, [получите бесплатную пробную версию](https://azure.microsoft.com/free/).
* [GO](https://golang.org/dl/)

## <a name="sdk-installation"></a>Установка пакета SDK

Из расположения GOPATH запустите следующий файл: `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Для использования пакета SDK нужно выполнить аутентификацию с помощью подписки Azure.  Ниже описано, как создать субъект-службу и использовать его для аутентификации. После этого вы получите экземпляр `ClustersClient`, в котором доступны различные функции (описанные далее) операций управления.

> [!NOTE]
> Кроме описанного выше, есть и другие методы аутентификации, которые могут оказаться удобнее для вас. Дополнительные сведения о функциях см. в статье [Методы аутентификации в пакете Azure SDK для Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Пример аутентификации с помощью субъекта-службы

Сначала войдите в [Azure Cloud Shell](https://shell.azure.com/bash). Убедитесь, что вы используете подписку, в которой будет создан субъект-служба. 

```azurecli-interactive
az account show
```

Сведения о подписке отображаются в формате JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Если вы вошли не в ту подписку, выполните такую команду, чтобы выбрать правильную подписку: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]
> Если вы еще не зарегистрировали поставщик ресурсов HDInsight с помощью другой функции (например, создав кластер HDInsight на портале Azure), вам необходимо это сделать, прежде чем выполнять аутентификацию. Это можно сделать с помощью [Azure Cloud Shell](https://shell.azure.com/bash), выполнив такую команду:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Создайте субъект-службу, выбрав имя и выполнив такую команду:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Сведения о субъекте-службе отображаются в виде JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Скопируйте фрагмент кода ниже и задайте в качестве значений `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` и `SUBSCRIPTION_ID` строки из JSON-файла, возвращенного после выполнения команды для создания субъекта-службы.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"    
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Управление кластерами

> [!NOTE]
> В этом разделе предполагается, что вы уже выполнили аутентификацию, создали экземпляр `ClusterClient` и сохранили его в переменной с именем `client`. Инструкции по выполнению аутентификации и получению `ClusterClient` см. в разделе "Аутентификация" выше.

### <a name="create-a-cluster"></a>Создание кластера

Кластер можно создать, вызвав `client.Create()`. 

#### <a name="example"></a>Пример

В этом примере показано, как создать кластер Spark с двумя головными узлами и одним рабочим.

> [!NOTE]
> Сначала необходимо создать группу ресурсов и учетную запись хранения, как описано далее. Если они уже созданы, следующие шаги можно пропустить.

##### <a name="creating-a-resource-group"></a>Создание группы ресурсов

Группу ресурсов можно создать с помощью [Azure Cloud Shell](https://shell.azure.com/bash), выполнив такую команду:
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Создание учетной записи хранения

Учетную запись хранения можно создать с помощью [Azure Cloud Shell](https://shell.azure.com/bash), выполнив такую команду:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Теперь выполните такую команду, чтобы получить ключ для учетной записи хранения (он потребуется для создания кластера):
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
Показанный ниже фрагмент кода Go создает кластер Spark с двумя головными узлами и одним рабочим. Задайте переменные, как описано в комментариях, и при необходимости измените другие параметры.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Получение сведений о кластере

Чтобы получить сведения о свойствах кластера, выполните такую команду:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Пример

Чтобы убедиться в том, что вы создали кластер, можно использовать `get`.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Выходные данные должны выглядеть так:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Получение списка кластеров

#### <a name="list-clusters-under-the-subscription"></a>Получение списка кластеров в пределах подписки
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Получение списка кластеров в пределах в группы ресурсов
```golang
client.ListByResourceGroup("<Resource Group Name>")
```
> [!NOTE]
> `List()` и `ListByResourceGroup()` возвращают структуры `ClusterListResultPage`. Чтобы перейти на следующую страницу, можно вызвать `Next()`. Команду можно повторять, пока для экземпляра `ClusterListResultPage.NotDone()` не будет возвращено значение `false`, как показано ниже.

#### <a name="example"></a>Пример
В следующем примере выводятся свойства всех кластеров в пределах текущей подписки:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Удаление кластера

Чтобы удалить кластер, выполните такую команду:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Обновление тегов кластера

Вы можете обновить теги кластера так:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Пример

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="scale-cluster"></a>Изменение масштаба кластера

Вы можете масштабировать кластер, изменяя количество его рабочих узлов так:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Мониторинг кластера

Пакет Azure Management SDK для HDInsight также позволяет управлять мониторингом кластеров с помощью Operations Management Suite (OMS).

Создайте экземпляр `ExtensionClient`, используемый в операциях мониторинга, используя такую же процедуру, как и при создании экземпляра `ClusterClient`, применяемого в операциях управления. После выполнения действий из раздела "Аутентификация" создайте экземпляр `ExtensionClient` следующим образом:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> В приведенных ниже примерах мониторинга предполагается, что вы уже инициализировали экземпляр `ExtensionClient` с именем `extClient` и присвоили ему значение `Authorizer`, как показано выше.

### <a name="enable-oms-monitoring"></a>Включение мониторинга OMS

> [!NOTE]
> Чтобы включить мониторинг OMS, требуется рабочая область Log Analytics. Если вы не создавали такую рабочую область, см. статью [Создание рабочей области Log Analytics на портале Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Чтобы включить мониторинг OMS в кластере, выполните такую команду:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Просмотр состояния мониторинга OMS

Чтобы узнать состояние мониторинга OMS в кластере, выполните такую команду:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Отключение мониторинга OMS

Чтобы отключить мониторинг OMS в кластере, выполните такую команду:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Действия сценария

В кластерах HDInsight поддерживается функция конфигурации с использованием действий сценариев, которая вызывает пользовательские сценарии настройки кластера.
> [!NOTE]
> Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

### <a name="execute-script-actions"></a>Выполнение действий сценариев

Вы можете выполнить действия скриптов в кластере так:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Для операций "Удаление действий сценариев" и "Получение списка сохраненных действий сценариев" необходимо создать экземпляр `ScriptActionsClient` точно так же, как вы создавали экземпляр `ClusterClient`, применяемый в операциях управления. После выполнения действий из раздела "Аутентификация" создайте экземпляр `ScriptActionsClient` следующим образом:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> В приведенных ниже примерах действия сценария предполагается, что вы уже инициализировали экземпляр `ScriptActionsClient` с именем `scriptActionsClient` и присвоили ему значение `Authorizer`, как показано выше.

### <a name="delete-script-action"></a>Удаление действий сценариев

Чтобы удалить сохраненные действия скриптов в кластере, выполните такую команду:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Получение списка сохраненных действий сценариев

> [!NOTE]
> Метод `ListByCluster()` возвращает структуру `ScriptActionsListPage`. Чтобы перейти на следующую страницу, можно вызвать `Next()`. Команду можно повторять, пока для экземпляра `ClusterListResultPage.NotDone()` не будет возвращено значение `false`, как показано ниже.

Чтобы получить список всех сохраненных действий скриптов в кластере, выполните такую команду:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Пример

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }    
}
```

### <a name="list-all-scripts-execution-history"></a>Просмотр журнала выполнения всех сценариев

Для этой операции необходимо создать экземпляр `ScriptExecutionHistoryClient`, используя ту же процедуру, с помощью которой вы создали экземпляр `ClusterClient`, применяемый в операциях управления. После выполнения действий из раздела "Аутентификация" создайте экземпляр `ScriptActionsClient` следующим образом:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> В приведенных ниже примерах предполагается, что вы уже инициализировали экземпляр `ScriptExecutionHistoryClient` с именем `scriptExecutionHistoryClient` и присвоили ему значение `Authorizer`, как показано выше.

Чтобы получить журнал выполнения всех скриптов в кластере, выполните такую команду:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Пример

В этом примере выводятся сведения обо всех выполнявшихся скриптах.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }       
}
```

## <a name="next-steps"></a>Дополнительная информация

* Просмотрите [справочные материалы GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs предоставляет справочную документацию по всем функциям в пакете SDK.
