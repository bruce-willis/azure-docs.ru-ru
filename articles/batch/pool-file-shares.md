---
title: Общая папка Azure для пулов пакетной службы Azure | Документация Майкрософт
description: Как подключить общую папку с помощью функции "Файлы Azure" к вычислительным узлам в пуле Linux или Windows пакетной службы Azure
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811792"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Использование общей папки Azure с пулом пакетной службы

Функция [Файлы Azure](../storage/files/storage-files-introduction.md) предоставляет полностью управляемые общие папки в облаке, доступ к которым можно получить с помощью протокола SMB. В этой статье содержатся сведения и примеры кода для подключения общей папки Azure к вычислительным узлам пула и ее использования. В примерах кода используются пакеты SDK .NET и Python пакетной службы. Но вы можете выполнять аналогичные операции с помощью других средств и пакетов SDK этой службы.

Пакетная служба предоставляет поддержку собственного API для чтения и записи данных с помощью больших двоичных объектов хранилища Azure. Но иногда может потребоваться доступ к общей папке Azure с вычислительных узлов пула. Например, у вас есть устаревшая рабочая нагрузка, которая зависит от общей папки SMB. Или же для выполнения ваших задач требуется доступ к общим данным либо создание общих выходных данных. 

## <a name="considerations-for-use-with-batch"></a>Рекомендации по использованию с пакетной службой

* Рассмотрите возможность использования общей папки Azure при наличии пулов, на которых выполняется относительно небольшое количество параллельных задач. Просмотрите [целевые показатели производительности и масштаба](../storage/files/storage-files-scale-targets.md), чтобы определить, следует ли использовать функцию "Файлы Azure" (которая использует учетную запись хранилища Azure), при заданном ожидаемом размере пула и количестве файловых активов. 

* Общие папки Azure — [экономичное решение](https://azure.microsoft.com/pricing/details/storage/files/). Кроме того, для них можно настроить репликацию данных в другой регион, чтобы обеспечить глобальную избыточность. 

* Вы можете параллельно подключить общую папку Azure c локального компьютера.

* Также см. [рекомендации по планированию](../storage/files/storage-files-planning.md) для общих папок Azure.


## <a name="create-a-file-share"></a>Создание общей папки

[Создайте общую папку](../storage/files/storage-how-to-create-file-share.md) в учетной записи хранения, связанной с вашей учетной записью пакетной службы, или в отдельной учетной записи хранения.

## <a name="mount-a-share-on-a-windows-pool"></a>Подключение общей папки в пуле Windows

В этом разделе приведены пошаговые инструкции и примеры кода для подключения и использования общей папки Azure в пуле узлов Windows. Дополнительные базовые сведения см. в [документации](../storage/files/storage-how-to-use-files-windows.md) по подключению общей папки Azure в Windows. 

В пакетной службе нужно подключать общую папку при каждом запуске задачи на узле Windows. Сейчас нет возможности установить постоянное сетевое подключение между задачами на узлах Windows.

Например, включите команду `net use`, чтобы подключить общую папку как часть командной строки каждой задачи. Для подключения общей папки требуются следующие учетные данные:

* **имя пользователя**: AZURE\\\<имя_учетной_записи_хранения\>, например AZURE\\*mystorageaccountname*;
* **пароль**: <ключ_учетной_записи_хранения==>, например *XXXXXXXXXXXXXXXXXXXXX==*.

Следующая команда позволяет подключить общую папку *myfileshare* в учетной записи хранения *mystorageaccountname* как диск *S:*

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Для упрощения в приведенных здесь примерах учетные данные передаются непосредственно в тексте. На практике мы настоятельно рекомендуем управлять учетными данными с помощью переменных среды, сертификатов или такого решения, как Azure Key Vault.

Чтобы упростить операцию подключения, при необходимости сохраняйте учетные данные на узлах. Тогда можно будет подключать общую папку без учетных данных. Выполните следующие два действия:

1. Запустите программу командной строки `cmdkey` с помощью задачи запуска в конфигурации пула. Благодаря этому учетные данные будут сохраняться на каждом узле Windows. Командная строка задачи запуска выглядит следующим образом:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. Подключите общую папку на каждом узле в рамках каждой задачи с использованием `net use`. Например, приведенная ниже командная строка задачи позволяет подключить общую папку как диск *S:*. Далее будет следовать команда или скрипт со ссылкой на общую папку. При вызове `net use` используются кэшированные учетные данные. На этом шаге предполагается, что вы используете для задач то же удостоверение пользователя, которое применялось для задачи запуска в пуле, что подходит не для всех сценариев.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>Пример C#
В следующем примере C# показано, как сохранить учетные данные в пуле Windows с помощью задачи запуска. Имя службы хранения файлов и учетные данные хранилища передаются в виде определенных констант. Здесь задача запуска выполняется с использованием учетной записи обычного автоматического пользователя (без прав администратора) с пулом в качестве области действия.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Сохранив учетные данные, используйте командные строки задач для подключения общей папки и добавления ссылки на нее в операциях чтения или записи. В качестве простого примера в командной строке задач в следующем фрагменте используется команда `dir`. Она позволяет просмотреть список файлов в общей папке. Обязательно выполняйте каждую задачу с использованием того же [удостоверения пользователя](batch-user-accounts.md), которое применялось для выполнения задачи запуска в пуле. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Подключение общей папки в пуле Linux

Файловые ресурсы Azure можно подключить в дистрибутивах Linux с помощью [CIFS-клиента в ядре](https://wiki.samba.org/index.php/LinuxCIFS). В приведенном ниже примере показано, как подключить общую папку в пуле вычислительных узлов Ubuntu 16.04 LTS. Если используется другой дистрибутив Linux, действия в целом аналогичны. Но следует использовать соответствующий конкретному дистрибутиву диспетчер пакетов. Подробные сведения и дополнительные примеры см. в статье об [использовании функции "Файлы Azure" в Linux](../storage/files/storage-how-to-use-files-linux.md).

Сначала, используя удостоверение администратора, установите пакет `cifs-utils` и создайте точку подключения (например, */mnt/MyAzureFileShare*) в локальной файловой системе. Папку для точки подключения можно создать в любом расположении в файловой системе, но общепринято создавать ее в папке `/mnt`. Помните, что точку подключения нельзя создавать непосредственно в `/mnt` (в Ubuntu) или `/mnt/resource` (в других дистрибутивах).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Затем выполните команду `mount` для подключения общей папки, указав следующие учетные данные:

* **имя пользователя**: \<имя_учетной_записи_хранения\>, например *mystorageaccountname*;
* **пароль**: <ключ_учетной_записи_хранения==>, например *XXXXXXXXXXXXXXXXXXXXX==*.

Следующая команда позволяет подключить общую папку *myfileshare* в учетной записи хранения *mystorageaccountname* по адресу */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Для упрощения в приведенных здесь примерах учетные данные передаются непосредственно в тексте. На практике мы настоятельно рекомендуем управлять учетными данными с помощью переменных среды, сертификатов или такого решения, как Azure Key Vault.

В пуле Linux можно объединить все эти действия в единую задачу запуска или выполнить их в скрипте. Выполните задачу запуска от имени администратора в пуле. Настройте задачу запуска для ожидания успешного завершения перед выполнением в пуле последующих задач, которые ссылаются на общую папку.

### <a name="python-example"></a>Пример на Python

В приведенном ниже примере на Python показано, как настроить пул Ubuntu для подключения общей папки в задаче запуска. Сведения о точке подключения, конечной точке общей папки и учетные данные хранилища передаются в виде определенных констант. Задача запуска выполняется с использованием учетной записи автоматического администратора с пулом в качестве области действия.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Подключив общую папку и определив задание, используйте общую папку в командных строках задач. Например, в приведенной ниже простой команде используется `ls` для перечисления файлов в общей папке.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Дополнительная информация

* Другие варианты чтения и записи данных в пакетной службе см. в [обзоре возможностей пакетной службы](batch-api-basics.md) и статье [Сохранение пакетных заданий и выходных данных задач](batch-task-output.md).

* Также ознакомьтесь с набором инструментов [Batch Shipyard](https://github.com/Azure/batch-shipyard) и [инструкциями Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) по развертыванию файловых систем для контейнерных рабочих нагрузок пакетной службы.