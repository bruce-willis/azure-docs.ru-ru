---
title: Рабочие нагрузки контейнера в пакетной службе Azure | Документация Майкрософт
description: Выполнение приложения из образов контейнеров в пакетной службе Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 8ef9d5a8e5212f6715769eecf4fde92a6d0b9d44
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060523"
---
# <a name="run-container-applications-on-azure-batch"></a>Выполнение контейнерных приложений в пакетной службе Azure

Пакетная служба Azure позволяет выполнять и масштабировать большие количества заданий в пакетной обработке данных в Azure. Такие задачи могут выполняться напрямую на виртуальных машинах (узлах) в пуле пакетной службы. Кроме того, пул пакетной службы можно настроить для выполнения задач в совместимых с Docker контейнерах на узлах. В этой статье показано, как создать пул вычислительных узлов, которые поддерживают выполнение задач контейнера, и как выполнять эти задачи в пуле. 

Вам нужно иметь представление о контейнере, а также знать, как создать пул пакетной службы и задание. В примерах кода используются пакеты SDK пакетной службы для .NET и Python. Также для создания пулов пакетной службы с включенными контейнерами и выполнения задач в контейнере можно использовать другие пакеты SDK пакетной службы и средства, включая портал Azure.

## <a name="why-use-containers"></a>Зачем нужны контейнеры?

Использование контейнеров предоставляет простой способ запуска задач пакетной службы без необходимости управлять средой и зависимостями. В контейнерах приложения развертываются в качестве упрощенных переносимых самодостаточных единиц, которые могут выполняться в нескольких разных средах. Например, можно создать и протестировать контейнер локально, а затем отправить образ контейнера в реестр в Azure или в другое расположение. Модель развертывания контейнера гарантирует, что среда выполнения приложения всегда правильно установлена и настроена, независимо от того, где размещено приложение. Для решения контейнерных задач в пакетной службе также можно воспользоваться преимуществами функций неконтейнерных задач, включая пакеты приложений и управление файлами ресурсов и выходными файлами. 

## <a name="prerequisites"></a>предварительным требованиям

* **Версии SDK**. Пакеты SDK для пакетной службы поддерживают образы контейнеров в таких версиях:
    * REST API пакетной службы версии 2017-09-01.6.0;
    * пакет SDK для .NET пакетной службы версии 8.0.0;
    * пакет SDK для Python пакетной службы версии 4.0;
    * пакет SDK для Java пакетной службы версии 3.0;
    * пакет SDK для Node.js пакетной службы версии 3.0.

* **Учетные записи**. В своей подписке Azure необходимо создать учетную запись пакетной службы и при необходимости создать учетную запись службы хранилища Azure.

* **Поддерживаемый образ виртуальной машины**. Контейнеры поддерживаются только в пулах, созданных с использованием конфигурации виртуальной машины из образов, описанных в следующем разделе: "Поддерживаемые образы виртуальных машин". Если вы решили предоставить пользовательский образ, ознакомьтесь с рекомендациями в следующем разделе и требованиями в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md). 

### <a name="limitations"></a>Ограничения

* Пакетная служба обеспечивает поддержку RDMA только для контейнеров, выполняемых в пулах Linux.

## <a name="supported-virtual-machine-images"></a>Поддерживаемые образы виртуальных машин

Чтобы создать пул вычислительных узлов виртуальной машины для рабочих нагрузок контейнера, используйте один из поддерживаемых образов Windows или Linux, указанных ниже. Дополнительные сведения о том, какие образы из Marketplace совместимы с пакетной службой, см. в [списке образов виртуальных машин](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Образы Windows

Для рабочих нагрузок контейнера Windows пакетная служба сейчас поддерживает образ **Windows Server Datacenter 2016 с контейнерами** из Azure Marketplace. В Windows поддерживаются только образы контейнеров Docker.

Можно также создавать пользовательские образы на основе виртуальных машин, на которых запущено средство Docker в Windows.

### <a name="linux-images"></a>Образы Linux

Для рабочих нагрузок контейнера Linux пакетная служба сейчас поддерживает следующие образы Linux, опубликованные разработчиками пакетной службы Microsoft Azure на платформе Azure Marketplace:

* **CentOS для контейнерных пулов пакетной службы Azure**;

* **CentOS (с драйверами RDMA) для контейнерных пулов пакетной службы Azure**;

* **Ubuntu Server для контейнерных пулов пакетной службы Azure**;

* **Ubuntu Server (с драйверами RDMA) для контейнерных пулов пакетной службы Azure**.

Эти образы можно использовать только в пулах пакетной службы Azure. Они содержат следующее:

* предустановленную среду выполнения [Moby](https://github.com/moby/moby) для контейнера; 

* предустановленные драйверы GPU NVIDIA для упрощения развертывания на виртуальных машинах Azure серии N;

* образы с предустановленными драйверами RDMA или без них; эти драйверы позволяют узлам пула получить доступ к сети Azure RDMA при развертывании на виртуальных машинах, размер которых поддерживает RDMA.  

Вы также можете создавать пользовательские образы на основе виртуальных машин, на которых работает Docker, используя один из дистрибутивов Linux, совместимый с пакетной службой. Если вы решите предоставить собственный пользовательский образ Linux, ознакомьтесь с инструкциями в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md).

Для поддержки Docker в пользовательском образе установите [Docker Community Edition (CE)](https://www.docker.com/community-edition) или [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Дополнительные рекомендации по использованию пользовательского образа Linux:

* Чтобы воспользоваться высокой производительностью GPU виртуальных машин Azure серии N для пользовательского образа, предварительно установите драйверы NVIDIA. Кроме того, необходимо установить служебную программу подсистемы Docker для GPU NVIDIA — [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Для доступа к сети Azure RDMA используйте размер виртуальной машины с поддержкой RDMA. Необходимые драйверы RDMA установлены в образах CentOS HPC и Ubuntu, поддерживаемых пакетной службой. Для выполнения рабочих нагрузок MPI может потребоваться дополнительная настройка. Дополнительные сведения см. в статье [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Конфигурация контейнера для пула пакетной службы

Чтобы включить пул пакетной службы для выполнения рабочих нагрузок контейнера, необходимо указать параметр [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) в пуле объекта [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration). (Эта статья содержит ссылки на справочники по API пакетной службы для .NET. Соответствующие параметры можно найти в статье об [API пакетной службы для Python](/python/api/azure.batch).)

Можно создать пул с включенным контейнером с предварительно загруженными образами контейнеров или без них, как показано в следующих примерах. Процесс извлечения (или предварительной загрузки) позволяет предварительно загрузить образы контейнеров из центра Docker или другого реестра контейнеров в Интернете. Чтобы обеспечить высокую производительность, используйте [реестр контейнеров Azure](../container-registry/container-registry-intro.md) в одном регионе с учетной записью пакетной службы.

Преимущество предварительного получения образов контейнеров заключается в том, что при первом запуске задач не нужно ожидать скачивания образа контейнера. Конфигурация контейнера извлекает образы контейнеров на виртуальную машину при создании пула. Задачи, выполняющиеся в пуле, могут ссылаться на список образов контейнеров и параметры выполнения контейнера.


### <a name="pool-without-prefetched-container-images"></a>Пул без предварительно полученных образов контейнеров

Чтобы настроить пул с включенным контейнером без предварительно полученных образов контейнеров, определите объекты `ContainerConfiguration` и `VirtualMachineConfiguration`, как показано в приведенном ниже примере на Python. В этом примере используется образ ОС сервера Ubuntu для контейнерных пулов пакетной службы Azure из Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Предварительное получение образов для конфигурации контейнера

Чтобы выполнить предварительную загрузку образов контейнеров в пуле, добавьте список образов контейнеров (`container_image_names` в Python) в `ContainerConfiguration`. 

В следующем простом примере на Python показано, как предварительно загрузить стандартный образ контейнера Ubuntu из [центра Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


В следующем примере на C# предполагается, что вы намерены предварительно загрузить образ TensorFlow из [центра Docker](https://hub.docker.com). Данный пример включает задачу запуска, выполняемую на узле виртуальной машины на узлах пула. Можно запустить стартовую задачу в узле, например, для вставки файлового сервера, доступ к которому можно получить из контейнеров.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Предварительно полученные образы из закрытого реестра контейнеров

Можно также выполнять предварительное получение образов контейнеров путем проверки подлинности на сервере закрытого реестра контейнеров. В следующем примере объекты `ContainerConfiguration` и `VirtualMachineConfiguration` предварительно загружают частный образ TensorFlow из частного реестра контейнеров Azure. Здесь используется такая же ссылка на образ, как и в предыдущем примере.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```


## <a name="container-settings-for-the-task"></a>Параметры контейнера для задачи

Для запуска задач контейнера на вычислительных узлах необходимо указать параметры для конкретного контейнера, такие как варианты выполнения задачи, используемые образы и реестр.

Используйте свойство `ContainerSettings` в классах задач для настройки параметров конкретного контейнера. Эти параметры определяются классом [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

При запуске задач на образах контейнеров для [задач облака](/dotnet/api/microsoft.azure.batch.cloudtask) и [диспетчера заданий](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) требуются параметры контейнера. Тем не менее [задача запуска](/dotnet/api/microsoft.azure.batch.starttask), [задача подготовки задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) и [задача снятия задания](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) не требуют параметров контейнера (то есть их можно выполнить в контексте контейнера или напрямую на узле).

Командная строка для контейнерных задач в пакетной службе Azure выполняется в рабочем каталоге в контейнере, который очень похож на среду, которую пакетная служба настраивает для регулярных (не связанных с контейнерами) задач:

* Все каталоги, следующие в обратном порядке после `AZ_BATCH_NODE_ROOT_DIR` (корень каталогов пакетной службы Azure на узле), сопоставляются в контейнере.
* Все переменные среды задач сопоставляются в контейнере.
* Рабочий каталог приложения настраивается так же, как и для обычных задач, чтобы можно было использовать такие функции, как пакеты приложений и файлы ресурсов.

Так как пакетная служба изменяет используемый по умолчанию каталог в контейнере, задача выполняется в расположении, отличающемся от обычной точки входа контейнера (например, в контейнере Windows по умолчанию используется `c:\`, а в контейнере Linux — `/`). Убедитесь, что в командной строке задачи или точке входа контейнера указан абсолютный путь, если он еще не настроен.

В приведенном ниже фрагменте кода Python показана обычная командная строка, которая запущена в контейнере Ubuntu, извлеченном из центра Docker. Параметры запуска контейнера представляют собой дополнительные аргументы для команды `docker create`, выполняемой в этой задаче. Здесь параметр `--rm` предназначен для удаления контейнера после выполнения задачи.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

В следующем примере на C# показаны основные параметры контейнера для задачи, выполняемой в облаке:

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь также с документацией по набору средств [Batch Shipyard](https://github.com/Azure/batch-shipyard), чтобы легко развертывать рабочие нагрузки контейнера в пакетной службе Azure, следуя инструкциям [Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Дополнительные сведения об установке и использовании Docker CE в Linux см. в документации [Docker](https://docs.docker.com/engine/installation/).

* Дополнительные сведения об использовании пользовательских образов см. в статье [Использование управляемого пользовательского образа для создания пула виртуальных машин](batch-custom-images.md).

* Узнайте больше о [проекте Moby](https://mobyproject.org/) — платформе для создания контейнерных систем.