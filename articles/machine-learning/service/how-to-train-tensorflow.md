---
title: Обучение моделей TensorFlow с помощью службы машинного обучения Azure
description: Сведения о проведении одноузлового и распределенного обучения моделей TensorFlow с помощью средства оценки TensorFlow
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: ba43593e90b78aaa0083faf4f8162a7663c0ad47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974227"
---
# <a name="how-to-train-tensorflow-models"></a>Обучение моделей TensorFlow

Для глубокого обучения нейронных сетей (DNN) с помощью TensorFlow служба машинного обучения Azure предоставляет пользовательский класс TensorFlow средства оценки. Средство оценки TensorFlow в пакете Azure SDK (не следует путать с классом [`tf.estimator.Estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator)) позволяет легко отправлять задания обучения TensorFlow для одноузловых и распределенных запусков в вычислительных ресурсах Azure.

## <a name="single-node-training"></a>Одноузловое обучение
Обучение с помощью средства оценки TensorFlow похоже на использование [базового средства оценки](how-to-train-ml-models.md), поэтому сначала прочтите статью с практическим руководством и изучите изложенные концепции.
  
Чтобы выполнить задание TensorFlow, следует создать объект `TensorFlow`. У вас уже должен быть создан объект `compute_target` [целевого вычислительного ресурса](how-to-set-up-training-targets.md#batch).

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'],
                    use_gpu=True)
```

Укажем следующие параметры в конструкторе TensorFlow.
* `source_directory`: локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленный вычислительный ресурс.
* `script_params`: словарь, указывающий аргументы командной строки для сценария обучения `entry_script` в виде пар <аргумент командной строки, значение>.
* `compute_target`: удаленный вычислительный ресурс (в этом случае — кластер [Batch AI](how-to-set-up-training-targets.md#batch)), на котором будет выполняться сценарий обучения.
* `entry_script`: путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. В этой папке должны быть расположены этот файл и дополнительные файлы, от которых он зависит.
* `conda_packages`: необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda. В этом случае сценарий обучения использует `sklearn` для загрузки данных, поэтому необходимо указать этот пакет для установки.  
Параметр `pip_packages` конструктора можно использовать для любых необходимых пакетов pip.
* `use_gpu`: присвойте этому флагу значение `True`, чтобы использовать GPU для обучения. По умолчанию равен `False`.

Так как вы работаете со средством оценки TensorFlow, контейнер, используемый для обучения, по умолчанию будет содержать пакет TensorFlow и связанные зависимости, необходимые для обучения в ЦП и GPU.

Затем отправьте задание TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Распределенное обучение
Средство оценки TensorFlow также позволяет обучать модели в кластерах ЦП и GPU виртуальных машин Azure. Распределенное обучение TensorFlow проводится с помощью нескольких вызовов API, при этом служба машинного обучения Azure в фоновом режиме будет управлять инфраструктурой и функциями оркестрации, необходимыми для выполнения этих рабочих нагрузок.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.
1. Распределенное обучение на основе MPI с использованием платформы [Horovod](https://github.com/uber/horovod).
2. Собственное [распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed) с использованием метода сервера параметров.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) — это поддерживающая алгоритм ring-allreduce платформа на основе открытого исходного кода для распределенного обучения, разработанная Uber.

Чтобы запустить распределенное обучение TensorFlow с помощью платформы Horovod, создайте объект TensorFlow следующим образом:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

В приведенном выше коде показаны следующие новые параметры в конструкторе TensorFlow.
* `node_count`: количество узлов, которые будут использоваться для задания обучения. Этот аргумент по умолчанию имеет значение `1`.
* `process_count_per_node`: количество процессов (или рабочих ролей), запускаемых на каждом узле. Этот аргумент по умолчанию имеет значение `1`.
* `distributed_backend`: серверная часть для запуска распределенного обучения, предлагаемая средством оценки с помощью MPI. Этот аргумент по умолчанию имеет значение `None`. Чтобы выполнять параллельное или распределенное обучение (например, `node_count`> 1 или `process_count_per_node`> 1, или оба варианта) с помощью MPI (и Horovod), задайте `distributed_backend='mpi'`. Служба машинного обучения использует реализацию MPI [Open MPI](https://www.open-mpi.org/).

В приведенном выше примере будет выполняться распределенное обучение с двумя рабочими ролями — по одной рабочей роли для каждого узла.

Horovod и его зависимости будут установлены автоматически, поэтому их можно просто импортировать в сценарий обучения `train.py` следующим образом:
```Python
import tensorflow as tf
import horovod
```

И, наконец, отправьте задание TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Сервер параметров
Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Создайте объект TensorFlow:
```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Обратите внимание на следующие параметры в конструкторе TensorFlow в приведенном выше коде.
* `worker_count`: количество рабочих процессов. Этот аргумент по умолчанию имеет значение `1`.
* `parameter_server_count`: количество серверов параметров. Этот аргумент по умолчанию имеет значение `1`.
* `distributed_backend`: серверная часть, которая будет использоваться для распределенного обучения. Этот аргумент по умолчанию имеет значение `None`. Чтобы провести распределенное обучение с помощью сервера параметров, потребуется задать `distributed_backend='ps'`.

#### <a name="note-on-tfconfig"></a>Примечания по `TF_CONFIG`
Вам также потребуются сетевые адреса и порты кластера для [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), поэтому служба машинного обучения Azure автоматически задает переменную среды `TF_CONFIG`.

Переменная среды `TF_CONFIG` представляет собой строку JSON. Ниже приведен пример переменной для сервера параметров.
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Если вы используете высокоуровневый API [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow, TensorFlow проанализирует эту переменную `TF_CONFIG` и сформирует спецификацию кластера. 

Если для обучения вы используете API более низкого уровня, вам необходимо самостоятельно проанализировать переменную`TF_CONFIG` и создать `tf.train.ClusterSpec` в коде обучения. В [этом примере](https://aka.ms/aml-notebook-tf-ps) эти действия выполняются в **сценарии обучения** следующим образом:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Завершив написание сценария обучения и создание объекта TensorFlow, отправьте задание обучения:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Примеры
Руководство по одноузловому обучению TensorFlow:
* `training/03.train-tune-deploy-tensorflow/03.train-tune-deploy-tensorflow.ipynb`

Руководство по распределенному обучению TensorFlow с использованием Horovod:
* `training/04.distributed-tensorflow-with-horovod/04.distributed-tensorflow-with-horovod.ipynb`

Руководство по собственному распределенному обучению TensorFlow:
* `training/05.distributed-tensorflow-with-parameter-server/05.distributed-tensorflow-with-parameter-server.ipynb`

Получение записных книжек:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация
* [Отслеживание метрик выполнения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
