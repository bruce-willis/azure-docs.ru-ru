---
title: Обучение моделей PyTorch с помощью службы "Машинное обучение Azure"
description: Сведения о проведении одноузлового и распределенного обучения моделей PyTorch с помощью средства оценки PyTorch.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977038"
---
# <a name="how-to-train-pytorch-models"></a>Способы обучения моделей PyTorch

Для глубокого обучения нейронных сетей (DNN) с помощью PyTorch служба "Машинное обучение Azure" предоставляет пользовательский класс PyTorch средства оценки. Средство оценки PyTorch в пакете Azure SDK позволяет легко отправлять задания обучения PyTorch для одноузловых и распределенных запусков в вычислительных ресурсах Azure.

## <a name="single-node-training"></a>Одноузловое обучение
Обучение с помощью средства оценки PyTorch похоже на использование [базового средства оценки](how-to-train-ml-models.md), поэтому сначала прочтите статью с практическим руководством и изучите изложенные концепции.
  
Чтобы выполнить задание PyTorch, следует создать объект `PyTorch`. У вас уже должен быть создан объект `compute_target` [целевого вычислительного ресурса](how-to-set-up-training-targets.md#batch) и объект `ds` [хранилища данных](how-to-access-data.md).

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Укажем следующие параметры в конструкторе PyTorch.
* `source_directory`. Локальный каталог, содержащий весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленный вычислительный ресурс.
* `script_params`. Словарь, указывающий аргументы командной строки для сценария обучения `entry_script` в виде пар <аргумент командной строки, значение>.
* `compute_target`. Удаленный вычислительный ресурс (в этом случае — кластер [Batch AI](how-to-set-up-training-targets.md#batch)), на котором будет выполняться сценарий обучения.
* `entry_script`. Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. В этой папке должны быть расположены этот файл и дополнительные файлы, от которых он зависит.
* `conda_packages`. Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda.
Параметр `pip_packages` конструктора можно использовать для всех необходимых пакетов pip.
* `use_gpu`. Присвойте этому флагу значение `True`, чтобы использовать GPU для обучения. По умолчанию — `False`.

Так как вы работаете со средством оценки PyTorch, контейнер, используемый для обучения, по умолчанию будет содержать пакет PyTorch и связанные зависимости, необходимые для обучения в ЦП и GPU.

Затем отправьте задание PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Распределенное обучение
Средство оценки PyTorch также позволяет обучать модели в кластерах ЦП и GPU виртуальных машин Azure. Распределенное обучение PyTorch проводится с помощью нескольких вызовов API, при этом служба машинного обучения Azure в фоновом режиме будет управлять инфраструктурой и функциями оркестрации, необходимыми для выполнения этих рабочих нагрузок.

Сейчас служба "Машинное обучение Azure" поддерживает распределенное обучение PyTorch на основе MPI с использованием платформы Horovod.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) — это поддерживающая алгоритм ring-allreduce платформа на основе открытого исходного кода для распределенного обучения, разработанная Uber.

Чтобы запустить распределенное обучение PyTorch с помощью платформы Horovod, создайте объект PyTorch следующим образом:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

В приведенном выше коде показаны следующие новые параметры конструктора PyTorch:
* `node_count`. Количество узлов, которые будут использоваться для задания обучения. Этот аргумент по умолчанию имеет значение `1`.
* `process_count_per_node`. Количество процессов (или рабочих ролей), запускаемых на каждом узле. Этот аргумент по умолчанию имеет значение `1`.
* `distributed_backend`. Серверная часть для запуска распределенного обучения, предлагаемая средством оценки с помощью MPI. Этот аргумент по умолчанию имеет значение `None`. Чтобы выполнять параллельное или распределенное обучение (например, `node_count`> 1 или `process_count_per_node`> 1, или оба варианта) с помощью MPI (и Horovod), задайте `distributed_backend='mpi'`. Служба "Машинное обучение Azure" использует реализацию MPI [Open MPI](https://www.open-mpi.org/).

В приведенном выше примере будет выполняться распределенное обучение с двумя рабочими ролями — по одной рабочей роли для каждого узла.

Horovod и его зависимости будут установлены автоматически, поэтому их можно просто импортировать в сценарий обучения `train.py` следующим образом:
```Python
import torch
import horovod
```

Наконец, отправьте распределенное задание PyTorch:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Примеры
Руководство по одноузловому обучению PyTorch см. здесь:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Руководство по распределенному обучению PyTorch с использованием Horovod см. здесь:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Получите записные книжки:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
