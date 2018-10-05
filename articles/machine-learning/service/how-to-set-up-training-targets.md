---
title: Настройка целевых объектов вычислений для обучения моделей с помощью службы машинного обучения Azure | Документы Майкрософт
description: Узнайте, как выбирать и настраивать среды обучения (целевые объекты вычислений), которые используются для обучения моделей машинного обучения. Служба машинного обучения Azure позволяет легко переключаться между средами обучения. Начните обучение на локальном компьютере, а затем, если вам потребуется увеличить масштаб, переключитесь на облачный целевой объект вычислений.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: e5b44ed2435986ffd500cade1f7c8ff8047d353d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452312"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Выбор и использование целевого объекта вычислений для обучения вашей модели

В службе машинного обучения Azure вы можете обучать свою модель в нескольких разных средах. Эти среды называются __целевыми объектами вычислений__ и могут быть локальными или облачными. Из этого документа вы узнаете о поддерживаемых целевых объектах вычислений и способах их использования.

Целевой объект вычислений — это ресурс, который запускает ваш сценарий обучения или размещает вашу модель, если она развертывается как веб-служба. Создавать такие объекты и управлять ими можно с помощью пакета SDK для машинного обучения Azure или интерфейса командной строки. Если у вас есть целевые объекты вычислений, созданные другим процессом (например, порталом Azure или Azure CLI), вы можете привязать их к рабочей области службы машинного обучения Azure.

Вы можете начать с работы на локальном компьютере, а затем увеличить масштаб и перейти в другие среды, такие как удаленные виртуальные машины обработки и анализа данных с поддержкой GPU или Azure Batch AI. 

## <a name="supported-compute-targets"></a>Поддерживаемые целевые объекты вычислений

Машинное обучение Azure поддерживает следующие целевые объекты вычислений:

|Целевой объект вычисления| Ускорение GPU | Автоматическая настройка гиперпараметров | Автоматический выбор модели | Возможность использования в конвейерах|
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](#local)| Возможно | &nbsp; | ✓ | &nbsp; |
|[Виртуальная машина для обработки и анализа данных](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[Экземпляры контейнеров Azure (ACI)](#aci)__  можно также использовать для обучения моделей. Это бессерверное облачное решение, которое стоит недорого и при этом легко создается и удобно в работе. ACI не поддерживает ускорение GPU, автоматическую настройку гиперпараметров или автоматический выбор модели. Кроме того, его нельзя использовать в конвейере.

Основные различия между целевыми объектами вычислений:
* __Ускорение GPU__: GPU предоставляются с виртуальной машины для обработки и анализа данных и Azure Batch AI. Возможность получения доступа к GPU на локальном компьютере зависит от установленного оборудования, драйверов и платформ.
* __Автоматическая настройка гиперпараметров__: автоматическая оптимизация гиперпараметров машинного обучения Azure помогает подобрать гиперпараметры, оптимальные для вашей модели.
* __Автоматический выбор модели__: машинное обучение Azure может рекомендовать выбор алгоритма и гиперпараметров при построении модели. Автоматический выбор модели позволяет получить высококачественную модель быстрее, чем если вы будете перебирать различные комбинации вручную. Дополнительную информацию см. в документе [Руководство. Автоматическое обучение модели классификации с помощью автоматического машинного обучения Azure](tutorial-auto-train-models.md).
* __Конвейеры__: машинное обучение Azure позволяет объединять различные задачи, такие как обучение и развертывание, в конвейер. Конвейеры могут запускаться параллельно или последовательно и представляют собой надежный механизм автоматизации. Дополнительные сведения см. в документе [Создание конвейеров машинного обучения с помощью службы машинного обучения Azure](concept-ml-pipelines.md).

Пакет SDK для машинного обучения Azure, Azure CLI или портал Azure можно использовать для создания целевых объектов вычислений. Кроме того, можно использовать уже существующие целевые объекты вычислений, добавив (присоединив) их к своей рабочей области.

> [!IMPORTANT]
> Существующий экземпляр контейнеров Azure подключить к рабочей области нельзя. Вместо этого необходимо создать новый экземпляр.
>
> Создать кластер Azure HDInsight в рабочей области нельзя. Вместо этого необходимо присоединить существующий кластер.

## <a name="workflow"></a>Рабочий процесс

Рабочий процесс для разработки и развертывания модели с помощью машинного обучения Azure включает следующие шаги:

1. Создание сценариев обучения машинного обучения на Python.
1. Создание и настройка целевого объекта вычислений либо подключение уже существующего объекта.
1. Отправка сценариев обучения в целевой объект вычислений.
1. Проверка результатов для подбора оптимальной модели.
1. Регистрация модели в реестре моделей.
1. Развертывание модели.

> [!IMPORTANT]
> Сценарий обучения не привязывается к определенному целевому объекту вычислений. Обучение можно начать на локальном компьютере, а затем переключить целевые объекты вычислений, не переписывая весь сценарий.

Для переключения с одного целевого объекта вычислений на другой необходимо создать [конфигурацию запуска](concept-azure-machine-learning-architecture.md#run-configuration). Конфигурация запуска определяет способ выполнения сценария в целевом объекте вычислений.

## <a name="training-scripts"></a>Сценарии обучения

Когда вы начинаете обучение, передается весь каталог, содержащий ваши сценарии обучения. Создается и отправляется в целевой объект обучения моментальный снимок. Дополнительные сведения см. в разделе о [моментальных снимках](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Локальный компьютер

В процессе локального обучения можно использовать для отправки операции обучения пакет SDK. Для обучения можно использовать среду, управляемую пользователем, или среду, управляемую системой.

### <a name="user-managed-environment"></a>Среда, управляемая пользователем

В среде, управляемой пользователем, вы сами следите за тем, чтобы все необходимые пакеты были доступны в среде Python, которую вы выбрали для выполнения сценариев. Следующий фрагмент кода представляет собой пример настройки обучения для среды, управляемой пользователем:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

Записную книжку Jupyter с примером обучения в управляемой пользователем среде см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).
  
### <a name="system-managed-environment"></a>Среда, управляемая системой

В средах, управляемых системой, зависимостями управляет Conda. Conda создает файл с именем `conda_dependencies.yml`, содержащий список зависимостей. После этого можно попросить систему создать новую среду Conda и выполнять сценарии в ней. Среды, управляемые системой, можно повторно использовать позднее при условии, что файлы `conda_dependencies.yml` останутся неизменны. 

Первоначальная настройка новой среды может потребовать несколько минут в зависимости от размера необходимых зависимостей. Следующий фрагмент кода демонстрирует создание среды, управляемой системой, которая зависит от scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Записную книжку Jupyter с примером обучения в управляемой системой среде см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).

## <a id="dsvm"></a>Виртуальная машина для обработки и анализа данных

На локальном компьютере может не оказаться вычислительных ресурсов или ресурсов GPU, необходимых для обучения модели. В этом случае вы можете масштабировать процесс обучения, добавив дополнительные целевые объекты вычислений, такие как виртуальные машины обработки и анализа данных (DSVM).

> [!WARNING]
> Машинное обучение Azure поддерживает только виртуальные машины под управлением Ubuntu. При создании виртуальной машины или выборе уже существующей необходимо выбрать виртуальную машину под управлением Ubuntu.

При выполнении последующих действий SDK позволит настроить виртуальную машину для обработки и анализа данных как целевого объекта обучения:

1. Создание или присоединение виртуальной машины
    
    * Прежде чем создавать DSVM, убедитесь, что у вас еще нет DSVM с таким же именем, и если это так, создайте новую виртуальную машину:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Чтобы присоединить имеющуюся виртуальную машину как целевой объект вычислений, необходимо указать ее полное доменное имя, имя входа и пароль.  В приведенном примере замените ```<fqdn>``` на общедоступное полное доменное имя виртуальной машины или общедоступный IP-адрес. Замените ```<username>``` и ```<password>``` на имя пользователя SSH и пароль для виртуальной машины:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Чтобы удалить вычислительные ресурсы, когда вы закончите, используйте следующий код:

    ```python
    dsvm_compute.delete()
    ```

Записную книжку Jupyter с примером обучения в среде "Виртуальная машина для обработки и анализа данных" см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb).

## <a id="batch"></a>Azure Batch AI

Если обучение модели занимает много времени, можно распространить обучение по кластеру вычислительных ресурсов в облаке с помощью Azure Batch AI. Batch AI можно также настроить на включение ресурса GPU.

Код в следующем примере ищет существующий кластер Batch AI по имени. Если кластера нет, он будет создан:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget

# choose a name for your cluster
batchai_cluster_name = ws.name + "cpu"

found = False
# see if this compute target already exists in the workspace
for ct in ws.compute_targets():
    print(ct.name, ct.type)
    if (ct.name == batchai_cluster_name and ct.type == 'BatchAI'):
        found = True
        print('found compute target. just use it.')
        compute_target = ct
        break
        
if not found:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", # for GPU, use "STANDARD_NC6"
                                                                #vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = True,
                                                                cluster_min_nodes = 1, 
                                                                cluster_max_nodes = 4)

    # create the cluster
    compute_target = ComputeTarget.create(ws,batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Чтобы подключить имеющийся кластер Batch AI как целевой объект вычислений, необходимо указать идентификатор ресурса Azure. Чтобы получить идентификатор ресурса, на портале Azure сделайте следующее:
1. Найдите службу `Batch AI` в разделе **Все службы**.
1. Щелкните имя рабочей области, к которой относится кластер.
1. Выберите кластер.
1. Нажмите **Свойства**.
1. Скопируйте **идентификатор**.

В следующем примере пакет SDK используется для присоединения кластера к рабочей области. Замените `<name>` в приведенном примере на любое имя для вычислений. Это имя не должно совпадать с именем кластера. Замените `<resource-id>` на идентификатор ресурса Azure, описанный выше:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Вы также можете проверить состояние кластера Batch AI и заданий с помощью следующих команд Azure CLI:

- Проверка состояния кластера: узнать количество запущенных узлов позволяет команда `az batchai cluster list`.
- Проверка состояния заданий: узнать количество выполняемых заданий позволяет команда `az batchai job list`.

Процесс создания кластера Batch AI занимает около 5 минут.

Записную книжку Jupyter Notebook с примером обучения в кластере Batch AI см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb).

## <a name='aci'></a>Экземпляр контейнера Azure (ACI)

Экземпляры контейнеров Azure — это изолированные контейнеры с более быстрым временем запуска, не требующие от пользователя управления виртуальными машинами. Служба машинного обучения Azure использует контейнеры Linux, доступные в регионах westus, eastus, westeurope, northeurope, westus2 и southeastasia. Дополнительные сведения см. в разделе о [доступности в регионах](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

В следующем примере показано, как создать целевой объект вычислений с помощью SDK и использовать его для обучения модели: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Создание целевого объекта вычислений ACI может занять от нескольких секунд до нескольких минут.

Записную книжку Jupyter с примером обучения в экземпляре контейнера Azure см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb).

## <a id="hdinsight"></a>Присоединение кластера HDInsight 

HDInsight — это популярная платформа для анализа больших данных. Она предоставляет Apache Spark, который можно использовать для обучения модели.

> [!IMPORTANT]
> Прежде чем использовать кластер HDInsight для обучения модели, его необходимо создать. Инструкции по созданию Spark в кластере HDInsight см. в документе [Создание кластера Spark в HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> При создании кластера необходимо указать имя пользователя SSH и пароль. Запишите эти значения — они вам потребуются при использовании HDInsight в качестве целевого объекта вычислений.
>
> Созданный кластер будет иметь полное доменное имя `<clustername>.azurehdinsight.net`, где `<clustername>` — это имя, которое вы указали для кластера. Этот адрес (или общедоступный IP-адрес кластера) нужно будет использовать как целевой объект вычислений.

Чтобы настроить HDInsight как целевой объект вычислений, необходимо указать полное доменное имя, а также имя входа и пароль для кластера HDInsight. В следующем примере пакет SDK используется для присоединения кластера к рабочей области. В приведенном примере замените `<fqdn>` на общедоступное полное доменное имя кластера или общедоступный IP-адрес. Замените `<username>` и `<password>` на имя пользователя SSH и пароль для кластера:

> [!NOTE]
> Чтобы узнать полное доменное имя для кластера, откройте портал Azure и выберите свой кластер HDInsight. В разделе __Обзор__ полное доменное имя будет частью записи __URL-адрес__. Просто удалите `https://` в начале значения.
>
> ![Снимок экрана с обзором кластера HDInsight и выделенной записью URL-адреса](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Выполнение обучающего прогона
    
Код для выполнения обучающего прогона будет одинаковым, независимо от целевого объекта вычислений:

* Создайте объект `ScriptRunConfig`, используя конфигурацию запуска для целевого объекта вычислений.
* Выполните прогон.
* Дождитесь завершения прогона.

В следующем примере используется конфигурация для локального целевого объекта вычислений, управляемого системой, который вы создали ранее в этом документе:

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

Записную книжку Jupyter с примером обучения с помощью Spark в HDInsight см. здесь: [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Просмотр и настройка вычислений с помощью портала Azure

Вы можете узнать, какие целевые объекты вычислений связаны с вашей рабочей областью, на портале Azure. Чтобы получить список этих объектов, выполните следующие действия:

1. Откройте [портал Azure](https://portal.azure.com) и перейдите к своей рабочей области.
2. Щелкните ссылку __Вычисление__ в разделе __Приложения__.

    ![Просмотр вкладки вычислений](./media/how-to-set-up-training-targets/compute_tab.png)

### <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Откройте список целевых объектов вычислений согласно приведенным выше инструкциям, а затем создайте целевой объект вычислений, выполнив следующие действия:

1. Щелкните значок __+__, чтобы добавить целевой объект вычислений.

    ![Добавить вычисление ](./media/how-to-set-up-training-targets/add_compute.png)

1. Введите имя для целевого объекта вычислений.
1. Выберите тип вычислений для присоединения к __обучению__. 
1. Выберите __Создать__ и заполните необходимую форму. 
1. Нажмите кнопку __Создать__
1. Чтобы увидеть состояние операции создания, выберите целевой объект вычислений в списке.

    ![Просмотр списка вычислений:](./media/how-to-set-up-training-targets/View_list.png) вы увидите сведения о выбранном вычислении.
    ![Подробнее](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Теперь вы можете выполнить прогон для этих целевых объектов, как описано выше.

### <a name="reuse-existing-compute-in-your-workspace"></a>Повторное использование существующих вычислений в рабочей области

Откройте список целевых объектов вычислений согласно приведенным выше инструкциям, а затем используйте существующий целевой объект вычислений еще раз, выполнив следующие действия:

1. Щелкните значок **+**, чтобы добавить целевой объект вычислений.
2. Введите имя для целевого объекта вычислений.
3. Выберите тип вычислений для присоединения к обучению. На портале для обучения сейчас поддерживаются Batch AI и виртуальные машины.
4. Выберите вариант "Использовать существующий".
    - При присоединении кластеров Batch AI выберите целевой объект вычислений из раскрывающегося списка, выберите рабочую область Batch AI и кластер Batch AI, а затем нажмите кнопку **Создать**.
    - При присоединении виртуальной машины введите IP-адрес, имя пользователя и пароль, закрытый и открытого ключи и порт и нажмите кнопку "Создать".

    > [!NOTE]
    > Майкрософт рекомендует использовать ключи SSH, поскольку они безопаснее, чем пароли. Пароли уязвимы для атак методом подбора, в то время как ключи SSH задействуют криптографические подписи. Сведения о создании ключей SSH для использования на виртуальных машинах Azure см. в следующих документах:
    >
    > * [Создание и использование ключей SSH в Linux или macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Создание и использование ключей SSH в Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Состояние подготовки можно узнать, выбрав целевой объект вычислений в списке вычислений.
6. Теперь вы можете выполнить прогон для этих целевых объектов.

## <a name="examples"></a>Примеры
Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`
* `01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb`
* `01.getting-started/03.train-on-aci/03.train-on-aci.ipynb`
* `01.getting-started/05.train-in-spark/05.train-in-spark.ipynb`
* `01.getting-started/07.hyperdrive-with-sklearn/07.hyperdrive-with-sklearn.ipynb`

Как получить эти записные книжки: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Справка по пакету SDK для машинного обучения Azure](http://aka.ms/aml-sdk)
* [Руководство. Обучение модели](tutorial-train-models-with-aml.md)
* [Где следует развертывать модели](how-to-deploy-and-where.md)
* [Создание конвейеров машинного обучения с помощью службы машинного обучения Azure](concept-ml-pipelines.md)
