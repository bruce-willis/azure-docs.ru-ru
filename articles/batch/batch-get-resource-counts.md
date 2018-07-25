---
title: Подсчет состояний для задач и узлов — пакетная служба Azure | Документы Майкрософт
description: Подсчет состояний задач пакетной службы Azure и вычислительных узлов для управления решениями пакетной службы и их мониторинга.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011952"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Мониторинг решений пакетной службы путем подсчета задач и узлов с определенным состоянием

Чтобы отслеживать крупномасштабные решения пакетной службы Azure и управлять ими, нужно точно подсчитать количество ресурсов в различных состояниях. Пакетная служба Azure предоставляет эффективные операции для получения этих счетчиков для *задач* и *вычислительных узлов* пакетной службы. Используйте эти операции вместо потенциально времязатратных вызовов API, чтобы получить подробные сведения о больших коллекциях задач или узлов.

* Операция [Get Task Counts][rest_get_task_counts] (Получить количество задач) позволяет статистически подсчитать число активных, выполняющихся и завершенных задач в задании, а также успешно и неудачно выполненных задач. 

  Узнав количество задач в каждом из состояний, вы можете легко отобразить ход выполнения задания пользователю или обнаружить непредвиденные задержки или ошибки, которые могут повлиять на задание. Операция Get Task Counts доступна, начиная с версии 2017-06-01.5.1 API пакетной службы, а также связанных пакетов SDK и средств.

* Операция [List Pool Node Counts][rest_get_node_counts] (Перечислить число узлов пула) возвращает число выделенных и низкоприоритетных вычислительных узлов в каждом пуле, находящихся в различных состояниях: создание, простой, вне сети, замещение, перезагрузка, повторное создание образа, запуск и другие. 

  Подсчитав число узлов в каждом из состояний, можно определить, когда вы получите достаточно вычислительных ресурсов для выполнения заданий, а также выявлять потенциальные проблемы с пулами. Операция List Pool Node Counts доступна, начиная с версии 2018-03-01.6.1 API пакетной службы, а также связанных пакетов SDK и средств.

Если вы используете версию службы, которая не поддерживает операции подсчета задач или узлов, используйте для подсчета этих ресурсов запрос перечисления. Кроме того, используйте запрос перечисления для получения сведений о других ресурсах пакетной службы, таких как приложения, файлы и задания. Дополнительные сведения о применении фильтров к запросам перечисления см. в разделе [Эффективное создание запросов на вывод списка ресурсов пакетной службы](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Подсчет состояний задач

Операция Get Task Counts подсчитывает задачи по следующим состояниям:

- **Активна** — задача находится в очереди и может быть запущена, но еще не назначена вычислительному узлу. Задача также является `active`, если она [зависит от родительской задачи](batch-task-dependencies.md), которая еще не завершена. 
- **Выполняется** — задача назначена вычислительному узлу, но еще не завершена. Задача считается `running`, если она находится в состоянии `preparing` или `running`, как указывает операция [Получение информации о задаче][rest_get_task].
- **Завершенная** — задача, которую больше невозможно запустить, так как она завершились успешно или неудачно и исчерпала предел повторных попыток. 
- **Успешно выполненная** — задача, результатом выполнения которой является состояние `success`. Пакетная служба определяет, успешно ли выполнена задача, проверяя свойство `TaskExecutionResult` свойства [executionInfo][rest_get_exec_info].
- **Неудачно выполненная** — задача, результатом выполнения которой является состояние `failure`.

В следующем примере кода .NET показано, как получить число задач по состоянию: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

Чтобы получить количество задач для задания, можно использовать аналогичный шаблон для REST и других поддерживаемых языков. 
 

### <a name="consistency-checking-for-task-counts"></a>Проверка согласованности количества задач

Пакетная служба предоставляет дополнительную проверку количества по состоянию задач, выполняя проверку согласованности по нескольким системным компонентам. Маловероятно, что при проверке согласованности будут обнаружены ошибки. Пакетная служба исправляет результат операции "Получить количество задач" на основе результатов проверки согласованности.

Свойство `validationStatus` в ответе указывает, выполнила ли пакетная служба проверку согласованности. Если пакетная служба не сопоставила подсчитанное количество по состояниям с фактическими состояниями в системе, для свойства `validationStatus` задается значение `unvalidated`. Из соображений производительности пакетная служба не выполняет проверку согласованности, если задание включает более 200 000 задач. В этом случае для свойства `validationStatus` задается значение `unvalidated`. (В такой ситуации число задач не обязательно неправильное, так как даже потеря малого объема данных маловероятна.) 

При изменении состояния задачи конвейер агрегирования обрабатывает изменение в течение нескольких секунд. Операция "Получить количество задач" отражает обновленное количество задач за указанный период. Однако если конвейер агрегирования пропускает изменение состояния задачи, такое изменение не регистрируется до следующей проверки. В течение этого времени количество задач может быть немного неточным из-за пропущенного события, но оно будет исправлено при следующей проверке.

## <a name="node-state-counts"></a>Подсчет состояний узлов

Операция List Pool Node Counts подсчитывает число вычислительных узлов с указанными ниже состояниями в каждом пуле. Для выделенных узлов и низкоприоритетных узлов в каждом пуле предоставляются отдельные статистические счетчики.

- **Creating** — выделенная в Azure виртуальная машина, которая еще не запущена для присоединения к пулу.
- **Idle** — доступный вычислительный узел, который сейчас не выполняет никакую задачу.
- **LeavingPool** — узел, который покидает пул, так как пользователь явно удалил его либо выполняется изменение размера или автомасштабирование пула (в сторону уменьшения).
- **Offline** — узел, который пакетная служба не может использовать для планирования новых задач.
- **Preempted** — низкоприоритетный узел, который был удален из пула, так как служба Azure освободила виртуальную машину. Узел `preempted` можно повторно инициализировать, если доступна низкоприоритетная емкость виртуальной машины для замены.
- **Rebooting** — перезапускаемый узел.
- **Reimaging** — узел, на котором переустанавливается операционная система.
- **Running** — узел, на котором выполняется одна или несколько задач (кроме задачи запуска).
- **Starting** — узел, на котором запускается пакетная служба. 
- **StartTaskFailed** — узел, на котором [задача запуска][rest_start_task] завершилась неудачно и исчерпала все повторные попытки, а для задачи запуска задано `waitForSuccess`. Этот узел невозможно использовать для выполнения задач.
- **Unknown** — узел, который потерял связь с пакетной службой, и состояние которого неизвестно.
- **Unusable** — узел, который невозможно использовать для выполнения задачи из-за ошибок.
- **WaitingForStartTask** — узел, на котором начала выполняться задача запуска, однако задан `waitForSuccess` и задача запуска не была завершена.

В следующем фрагменте кода C# показано, как перечислить количество узлов для всех пулов в текущей учетной записи:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
В следующем фрагменте кода C# показано, как перечислить количество узлов для заданного пула в текущей учетной записи:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Чтобы получить количество узлов для пулов, можно использовать аналогичный шаблон для REST и других поддерживаемых языков.
 
## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об основных понятиях и функциях пакетной службы см. в статье [Разработка решений для крупномасштабных параллельных вычислений с использованием пакетной службы](batch-api-basics.md). В этой статье рассматриваются основные ресурсы пакетной службы, такие как пулы, вычислительные узлы, задания и задачи, а также приведены общие сведения о функциях этой службы.

* Сведения о применении фильтров к запросам, перечисляющим ресурсы пакетной службы, см. в разделе [Эффективное создание запросов на вывод списка ресурсов пакетной службы](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

