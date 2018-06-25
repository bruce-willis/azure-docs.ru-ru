---
title: Краткое руководство Azure по выполнению задания обучения CNTK в Batch AI с помощью Azure CLI | Документация Майкрософт
description: Научитесь быстро выполнять задание обучения CNTK в Batch AI, используя Azure CLI
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294079"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Выполнение задания обучения CNTK с помощью Azure CLI

Azure CLI 2.0 позволяет создать ресурсы Batch AI и управлять ими — создавать и удалять файловые серверы и кластеры Batch AI, а также отправлять, завершать, удалять и отслеживать задания обучения.

В этом кратком руководстве объясняется, как создать кластер GPU и выполнить задание обучения с помощью Microsoft Cognitive Toolkit (CNTK).

Скрипт обучения [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) доступен на странице GitHub для Batch AI. Этот скрипт обучает сверточную нейронную сеть, используя базу данных образцов рукописного написания цифр MNIST.

Официальный пример CNTK был изменен для приема расположения набора данных для обучения и расположения каталога выходных данных с использованием аргументов командной строки.

## <a name="quickstart-overview"></a>Общие сведения о кратком руководстве

* Создайте кластер GPU с одним узлом (с размером ВМ `Standard_NC6`) с именем `nc6`.
* Создайте учетную запись хранения для входных и выходных данных задания.
* Создайте общий файловый ресурс Azure с двумя папками (`logs` и `scripts`), чтобы хранить выходные данные задания и скрипты обучения.
* Создайте контейнер больших двоичных объектов Azure `data` для хранения данных для обучения.
* Разверните скрипт обучения и данные для обучения в созданной общей папке и контейнере.
* Настройте задание, чтобы подключить общий файловый ресурс и контейнер больших двоичных объектов Azure на узле кластера и сделать их доступными как обычные файловые системы в `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` и `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` является переменной среды, заданной для задания с помощью Batch AI.
* Отслеживайте выполнение задания путем потоковой передачи стандартного потока вывода.
* После завершения задания проверьте его выходные данные и созданные модели.
* В конце удалите все выделенные ресурсы.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Доступ к Azure CLI 2.0 с модулем Batch AI 0.3 или более поздней. Вы можете использовать интерфейс Azure CLI 2.0 в [Azure Cloud Shell](../cloud-shell/overview.md) или установить его локально, следуя [этим инструкциям](/cli/azure/install-azure-cli?view=azure-cli-latest).

  При использовании Cloud Shell измените рабочую папку на `/usr/$USER/clouddrive`, так как в вашем корневом каталоге нет свободного пространства:

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером для развертывания ресурсов Azure и управления ими. Следующая команда создает группу ресурсов `batchai.quickstart` в расположении "Восточная часть США":

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>Создание рабочей области Batch AI

Следующая команда создает рабочую область Batch AI в группе ресурсов. Рабочая область Batch AI — это коллекция всех типов ресурсов Batch AI верхнего уровня.

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>Создание кластера GPU

Следующая команда создает кластер GPU с одним узлом (с размером ВМ — Standard_NC6) в рабочей области, используя виртуальную машину для обработки и анализа данных (DSVM) под управлением Ubuntu как образ операционной системы:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

DSVM под управлением Ubuntu позволяет выполнять все задания обучения в контейнерах Docker и запускать наиболее популярные платформы глубокого обучения непосредственно на виртуальной машине.

Параметр `--generate-ssh-keys` запускает Azure CLI для создания открытых и закрытых ключей SSH, если у вас еще их нет. Вы можете получить доступ к узлам кластера, используя текущее имя пользователя и созданный ключ SSH.

Если вы используете Cloud Shell, мы рекомендуем создать резервную копию папки ~/.ssh в постоянном хранилище.

Выходные данные примера:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Следующая команда создает учетную запись хранения в той же группе ресурсов, в которой создан кластер Batch AI. Используйте учетную запись хранения для входных и выходных данных задания. Обновите команду, указав уникальное имя учетной записи хранения.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>Развертывание данных

### <a name="download-the-training-script-and-training-data"></a>Скачивание скрипта обучения и данных для обучения

* Скачайте и извлеките предварительно обработанную базу данных MNIST из [этого расположения](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) в текущую папку.

Для GNU/Linux или Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Обратите внимание: может потребоваться установить `unzip`, если этой программы нет в дистрибутиве GNU/Linux.

* Загрузите пример скрипта [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) в текущую папку:

Для GNU/Linux или Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>Создание общего файлового ресурса Azure и развертывание скрипта обучения

Следующие команды создают общие файловые ресурсы Azure `scripts` и `logs`, а также копируют скрипт обучения в папку `cntk` в общем ресурсе `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>Создание контейнера больших двоичных объектов и развертывание данных для обучения

Следующие команды создают контейнер больших двоичных объектов Azure с именем `data` и копируют данные для обучения в папку `mnist_cntk`:

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>Отправка задания обучения

### <a name="create-a-batch-ai-experiment"></a>Создание эксперимента Batch AI

Эксперимент — это логический контейнер для связанных заданий Batch AI. Чтобы создать эксперимент в рабочей области, используйте следующую команду:

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>Подготовка файла конфигурации задания

Создайте файл конфигурации задания обучения `job.json` со следующим содержимым. Укажите имя своей учетной записи хранения.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Этот файл конфигурации задает:

* `nodeCount` — количество узлов, необходимых для задания (1 для этого краткого руководства).
* `cntkSettings` — указывает путь скрипта обучения и аргументы командной строки. Аргументы командной строки включают путь к данным для обучения и конечный путь для сохранения созданных моделей. `AZ_BATCHAI_OUTPUT_MODEL` является переменной среды, заданной Batch AI в зависимости от конфигурации каталога выходных данных (см. ниже).
* `stdOutErrPathPrefix` — путь, где Batch AI создает каталоги, содержащие выходные данные задания и журналы.
* `outputDirectories` — коллекция каталогов выходных данных, создаваемая Batch AI. Для каждого каталога Batch AI создает переменную среды с именем `AZ_BATCHAI_OUTPUT_<id>`, где `<id>` — идентификатор каталога.
* `mountVolumes` — список файловых систем для подключения во время выполнения задания. Файловые системы подключаются в пути `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` является переменной среды, заданной с помощью Batch AI.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` — имя учетной записи хранения, которая указывается при отправке задания с помощью параметра `--storage-account-name parameter` или переменной среды `AZURE_BATCHAI_STORAGE_ACCOUNT` на компьютере.

### <a name="submit-the-job"></a>Отправка задания

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

Выходные данные примера:
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>Отслеживание выполнения задания

Скрипт обучения передает данные о ходе выполнения обучения в файл `stderr.txt` в каталоге стандартного потока вывода. Отслеживайте ход выполнения, используя следующую команду:

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

Выходные данные примера:
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

Потоковая передача прекращается в момент завершения задания (состояние "Успешно" или "Сбой").

## <a name="inspect-generated-model-files"></a>Проверка созданных файлов модели

Созданные файлы моделей для задания хранятся в каталоге выходных данных с атрибутом `id` со значением `MODEL`. Чтобы вывести список файлов моделей и получить URL-адреса скачивания, используйте следующую команду:

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

Выходные данные примера:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Вы также можете использовать портал Azure или Обозреватель службы хранилища Azure, чтобы изучить созданные файлы. Чтобы отличить выходные данные из разных заданий, Batch AI создает уникальную структуру папок для каждого из них. Найдите путь к папке, содержащей выходные данные, используя атрибут `jobOutputDirectoryPathSegment` для отправленного задания:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

Выходные данные примера:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов и все связанные ресурсы будут не нужны, удалите их с помощью следующей команды:

```azurecli
az group delete -n batchai.quickstart -y
```
