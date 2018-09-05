---
title: Отправка большого количества задач в пакетной службе Azure | Документация Майкрософт
description: Сведения об эффективной отправке большого количества задач в одном задании пакетной службы
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3c683b24db2899ee680988c7bedc760d6bb8ec73
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052825"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Отправка большого количества задач в пакетное задание

При выполнении крупномасштабных рабочих нагрузок пакетной службы Azure вам может понадобиться отправить десятки тысяч, сотни тысяч или даже больше задач в одно задание. 

В этой статье приводятся рекомендации и некоторые примеры кода для отправки большого количества задач в одно пакетное задание с существенно увеличенной пропускной способностью. Отправленные задачи поступают в очередь пакетной службы Azure для обработки в пуле, указанном для задания.

## <a name="use-task-collections"></a>Использование коллекций задач

Помимо добавления одной задачи за раз API пакетной службы предоставляет методы для эффективного добавления задач в задание как *коллекции*. При добавлении большого количества задач нужно использовать соответствующие методы или перегрузки для добавления задач в виде коллекции. Как правило, вы создаете коллекцию задач, определяя задачи по мере выполнения итерации набора входных файлов или параметров для своего задания.

Максимальный размер коллекции задач, которую вы можете добавить в одном вызове, зависит от используемого API пакетной службы:

* Следующие API пакетной службы ограничивают коллекцию до **100 задач**. Предел может быть меньше в зависимости от размера задач. Например, если задачи включают большое количество файлов ресурсов или переменных среды.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#azure_batch_operations_TaskOperations_add_collection)
    * [API Node.js](/javascript/api/azure-batch/task?view=azure-node-latest#addcollection)

  При использовании этих API-интерфейсов вам необходимо предоставить логику, чтобы разделить количество задач для соответствия ограничению коллекции, а также обработать ошибки и повторения в случае сбоя при добавлении задач. Если коллекция задач слишком велика для добавления, запрос сгенерирует ошибку. После этого нужно повторить попытку с меньшим количеством задач.

* Следующие API-интерфейсы поддерживают гораздо большие коллекции задач и ограничиваются только доступностью ОЗУ в клиенте, выполняющем отправку. Эти API прозрачно обрабатывают разделение коллекции задач на "блоки" для API нижнего уровня и повторы при сбое добавления задач:

    * [API для .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API Java](/java/api/com.microsoft.azure.batch.protocol._tasks.addcollectionasync?view=azure-java-stable)
    * [расширение CLI пакетной службы Azure](batch-cli-templates.md) с шаблонами CLI пакетной службы;
    * [расширение пакета SDK для Python](https://pypi.org/project/azure-batch-extensions/).

## <a name="increase-throughput-of-task-submission"></a>Увеличение пропускной способности отправки задачи

Может потребоваться некоторое время, чтобы добавить большую коллекцию задач в задание, например до 1 минуты, чтобы добавить 20 000 задач через API .NET. В зависимости от API пакетной службы и рабочей нагрузки вы можете повысить пропускную способность задачи, изменив один или несколько компонентов ниже.

* **Размер задачи**. Добавление больших задач занимает больше времени, чем добавление небольших. Чтобы уменьшить размер каждой задачи в коллекции, вы можете упростить командную строку задачи, уменьшить количество переменных среды или более эффективно выполнить требования к выполнению задачи. Например, вместо использования большого количества файлов ресурсов установите зависимости задачи с помощью [задачи запуска](batch-api-basics.md#start-task) в пуле или используйте [пакет приложения](batch-application-packages.md) либо [контейнер Docker](batch-docker-container-workloads.md).

* **Количество параллельных операций**. В зависимости от API пакетной службы повысьте пропускную способность, увеличив максимальное количество параллельных операций, выполняемых клиентом пакетной службы. Настройте этот параметр с помощью свойства [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) в API .NET или параметра `threads` такого метода, как [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection), в расширении пакета SDK для Python в пакетной службе. (Это свойство недоступно в исходном пакете SDK для Python в пакетной службе.) По умолчанию для этого свойства установлено значение 1, но установите большее значение, чтобы повысить пропускную способность операций. Вы компенсируете увеличенную пропускную способность за счет использования пропускной способности сети и производительности ЦП. Пропускная способность задачи увеличивается в 100 раз по сравнению с `MaxDegreeOfParallelism` или `threads`. На практике вы должны установить количество параллельных операций ниже 100. 
 
  Расширение CLI пакетной службы Azure с шаблонами пакетной службы автоматически увеличивает количество параллельных операций на основе количества доступных ядер, но это свойство не настраивается в CLI. 

* **Ограничения подключения HTTP**. Количество параллельных подключений HTTP может регулировать производительность клиента пакетной службы, когда он добавляет большое количество задач. Для определенных API количество подключений HTTP ограничено. Например, при разработке с помощью API .NET свойству [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) по умолчанию задается значение 2. Мы рекомендуем увеличить значение до числа, близкого к числу параллельных операций или более высокого.

## <a name="example-batch-net"></a>Пример: .NET пакетной службы

В следующих фрагментах кода C# показаны параметры, которые нужно настроить при добавлении большого количества задач с помощью API .NET пакетной службы.

Чтобы увеличить пропускную способность задачи, увеличьте значение свойства [MaxDegreeofParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) в [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Например: 

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Добавьте коллекцию задач в задание, используя соответствующую перегрузку метода [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) или [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
). Например: 

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Пример: расширение CLI пакетной службы

Используя расширения CLI пакетной службы Azure с [шаблонами CLI пакетной службы](batch-cli-templates.md), создайте JSON-файл шаблона задания, который включает в себя [фабрику задач](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Фабрика задач настраивает коллекцию связанных задач для задания из одного определения задачи.  

Ниже приведен пример шаблона задания для одномерного задания параметрического анализа с большим количеством задач. В данном случае — 250 000. Командная строка задачи — это простая команда `echo`.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Сведения о запуске задания с помощью шаблона см. в статье [Использование шаблонов интерфейса командной строки для пакетной службы Azure и передачи файлов](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Пример: расширение пакета SDK для Python в пакетной службе

Чтобы использовать расширение пакета SDK для Python в пакетной службе Azure, сначала установите пакет SDK для Python и расширение:

```
pip install azure-batch
pip install azure-batch-extensions
```

Настройте клиент `BatchExtensionsClient`, который использует расширение пакета SDK.

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Создайте коллекцию задач для добавления в задание. Например: 


```python
tasks=list()
# Populate the list with your tasks
...

```

Добавьте коллекцию задач с помощью [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection). Настройте параметр `threads`, чтобы увеличить количество параллельных операций:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Расширение пакета SDK для Python в пакетной службе также поддерживает добавление параметров задачи в задание с помощью спецификации JSON для фабрики задач. Например, настройте параметры задания для параметрического анализа, аналогичного анализу в предыдущем примере [шаблона CLI пакетной службы](#example-batch-cli-template):

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Добавьте параметры задания в задание. Настройте параметр `threads`, чтобы увеличить количество параллельных операций:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о расширении CLI пакетной службы Azure см. в статье [Использование шаблонов интерфейса командной строки для пакетной службы Azure и передачи файлов](batch-cli-templates.md).
* Ознакомьтесь с дополнительными сведениями о [расширении пакета SDK для Python в пакетной службе](https://pypi.org/project/azure-batch-extensions/).