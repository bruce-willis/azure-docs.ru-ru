---
title: Руководство. Обучение модели классификации изображений с помощью Машинного обучения Azure
description: Сведения о развертывании модели классификации изображений scikit-learn с помощью Jupyter Notebook для Python. Это руководство представляет собой первую часть серии, состоящей из двух частей.
author: hning86
ms.author: haining
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: bed4abcce3019607715416b5194a2ddecc89b76a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966617"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning"></a>Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure

В этом руководстве необходимо обучить модель машинного обучения как локально, так и на удаленных вычислительных ресурсах. Вы будете использовать рабочий процесс обучения и развертывания для службы "Машинное обучение Azure" в Jupyter Notebook для Python.  Затем можно использовать записную книжку как шаблон для обучения собственной модели машинного обучения со своими данными. Это руководство представляет собой **первую часть серии, состоящей из двух частей**.  

Это руководство обучает простую логистическую регрессию с помощью набора данных [MNIST](http://yann.lecun.com/exdb/mnist/) и [scikit-learn](http://scikit-learn.org) с Машинным обучением Azure.  MNIST — это популярный набор данных, состоящий из 70 000 изображений в оттенках серого. Каждый образ является рукописной цифрой 28 x 28 пикселей, представляющей собой число от 0 до 9. Целью является создание многоклассового классификатора для идентификации цифры, которую отображает данное изображение. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка среды разработки
> * Подключение и проверка данных
> * Локальное обучение простой логистической регрессии с помощью популярной библиотеки машинного обучениях scikit-learn 
> * Обучение нескольких моделей на удаленном кластере
> * Проверка результатов обучения и регистрация наилучшей модели

Во второй [части этого руководства](tutorial-deploy-models-with-aml.md) вы узнаете, как выбрать и развернуть модель. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="get-the-notebook"></a>Получение записной книжки

Для удобства это руководство доступно в виде Jupyter Notebook. Используйте любой из этих способов для запуска блокнота `tutorials/01.train-models.ipynb`.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Все настройки для работы по разработке можно сделать в записной книжке Python.  Программа установки включает:

* импорт пакетов Python;
* подключение к рабочей области для обеспечения связи между локальным компьютером и удаленными ресурсами;
* создание эксперимента, чтобы отслеживать все запуски;
* создание целевого объекта удаленных вычислений для использования в обучении.

### <a name="import-packages"></a>Импорт пакетов

Импортируйте пакеты Python, которые необходимы вам в этом сеансе. Также отобразите версию пакета SDK для Машинного обучения Azure.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>Подключение к рабочей области

В существующей рабочей области создайте объект. `Workspace.from_config()` считывает файл **config.json** и загружает данные в объект с именем `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Создание эксперимента

Чтобы отслеживать все сведения о потоковом выполнении в рабочей области, создайте эксперимент.  

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Создание удаленного целевого объекта вычислений

Azure Batch AI — это управляемая служба, которая позволяет специалистам по анализу данных обучать модели машинного обучения в кластерах виртуальных машин Azure, в том числе виртуальных машин с поддержкой GPU.  В этом руководстве вы научитесь создавать кластер Azure Batch AI. Этот код создаст кластер, если он еще не существует в вашей рабочей области. 

 **Создание кластера занимает около 5 минут.** Если кластер уже находится в рабочей области, этот код будет использовать его, пропуская процесс создания.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if compute_target is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Теперь у вас есть необходимые пакеты и вычислительные ресурсы для обучения модели в облаке. 

## <a name="explore-data"></a>Изучение данных

Прежде чем начинать обучение модели, необходимо понимать, какие данные используются для ее обучения.  Вам необходимо скопировать данные в облако, чтобы к ним можно было получить доступ в облачной среде обучения.  Из этого раздела вы узнаете, как выполнять следующие действия.

* Скачивание набора данных MNIST
* Отображение некоторых примеров изображений
* Отправка данных в облако

### <a name="download-the-mnist-dataset"></a>Скачивание набора данных MNIST

Скачайте набор данных MNIST и сохраните файлы в локальном каталоге `data`.  Загрузите изображения и метки для обучения и тестирования.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Отображение некоторых примеров изображений

Загрузите сжатые файлы в массивы `numpy`. Затем с помощью `matplotlib` постройте график 30 случайных изображений из набора данных с подписями над ними.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Случайный пример изображений приведен ниже.

![Случайный пример изображений](./media/tutorial-train-models-with-aml/digits.png)

Теперь у вас есть представление о том, как выглядят эти изображения, и прогноз ожидаемого результата.

### <a name="upload-data-to-the-cloud"></a>Отправка данных в облако

Теперь сделайте данные удаленно доступными путем отправки этих данных с локального компьютера в облако, чтобы они были доступны для удаленного обучения. Хранилище данных — это удобная конструкция, связанная с рабочей областью для отправки или скачивания данных и взаимодействия с ними из удаленных целевых объектов вычислений. 

MNIST-файлы передаются в корневой каталог хранилища данных с именем `mnist`.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Теперь у вас есть все необходимое для начала обучения модели. 

## <a name="train-a-model-locally"></a>Локальное обучение модели

Обучайте простую логистическую регрессию из библиотеки scikit-learn локально.

**Локальное обучение может занять одну или две минуты** в зависимости от конфигурации компьютера.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

Далее создайте прогнозы на основе проверочного набора и вычислений точности. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

Точность локальной модели изображена ниже.

`0.9202`

С помощью нескольких строк кода точность достигла 92 %.

## <a name="train-on-a-remote-cluster"></a>Обучение на удаленном кластере

Теперь вы можете расширить эту простую модель, построив модель с другой скоростью регуляризации. На этот раз будем обучать модель на удаленном ресурсе.  

Для выполнения этой задачи отправьте задание в кластер удаленного обучения, который мы настроили ранее.  Чтобы отправить задание, нужно:
* создать каталог;
* создать скрипт обучения;
* создать оценщика;
* отправить задание. 

### <a name="create-a-directory"></a>создать каталог;

Создайте каталог для доставки необходимого кода с компьютера на удаленный ресурс.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Создание сценария обучения

Чтобы отправить задание в кластер, необходимо сначала создать сценарий обучения. Для создания сценария обучения выполните следующий код, который называется `train.py`, в каталоге, который был только что создан. Это обучение добавляет регуляризацию алгоритма обучения, поэтому выдает несколько различных моделей в отличие от локальной версии.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_submitted_run()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Обратите внимание на то, как сценарий получает данные и сохраняет модели.

+ Сценарий обучения считывает аргумент, чтобы найти каталог, содержащий данные.  При отправке задания позже вы указываете хранилище данных для этого аргумента: `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`.

    
+ Сценарий обучения сохраняет модель в каталог с именем выходных данных. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Все записи в этом каталоге автоматически передаются в рабочую область. Далее в этом руководстве вы узнаете, как получить доступ к своей модели из этого каталога.

Файл `utils.py` — это ссылка из скрипта обучения для правильной загрузки набора данных.  Скопируйте этот скрипт в папку таким образом, чтобы он был доступен вместе со скриптом обучения на удаленном ресурсе.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Создание оценщика

Объект оценщика используется для отправки потокового выполнения.  Создайте оценщик, выполнив следующий код для определения.

* Назовите объект оценщика как `est`.
* Выберите каталог, который содержит скрипт. Все файлы в этом каталоге передаются в узел кластера для выполнения. 
* Целевой объект вычисления.  В этом случае будет использоваться созданный кластер Batch AI.
* Имя скрипта обучения — train.py.
* Параметры, требуемые от сценария обучения. 
* Пакеты Python, необходимые для обучения.

В этом руководстве целевой объект — это кластер Batch AI. Все файлы в этом каталоге передаются в узел кластера для выполнения. Data_folder настроен на использование хранилища данных (`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Отправка задания в кластер

Запустите эксперимент, выполняя отправку объекта оценщика.

```python
run = exp.submit(config=est)
run
```

Поскольку вызов является асинхронным, он возвращает состояние **запуска** сразу после запуска задания.

## <a name="monitor-a-remote-run"></a>Мониторинг удаленного выполнения

Обычно первый запуск занимает **около 10 минут**. Но для последующих запусков, пока не изменяются зависимости сценария и повторно используется одно и то же изображение, время запуска контейнера будет намного меньше.

Ниже приведены процессы, которые происходят во время ожидания.

- **Создание образа**. Создается образ Docker, соответствующий среде Python, определяемый оценщиком. Изображение загружается в рабочую область. Создание и отправка изображений занимает **около 5 минут**. 

  Этот этап происходит один раз для каждой среды Python, так как контейнер будет помещен в кэш при последующих запусках.  Во время создания образа журналы будут отправлены в журнал выполнения. Вы можете отслеживать ход выполнения создания образа с помощью этих журналов.

- **Масштабирование**. Если для удаленного кластера требуется больше узлов, чем доступно, дополнительные узлы добавятся автоматически. Масштабирование обычно занимает **около 5 минут.**

- **Запуск**. На этом этапе необходимые сценарии и файлы отправляются на целевой объект, затем хранилища данных монтируются или копируются, после чего запускается запись entry_script. Пока выполняется задание, stdout и каталог ./logs передаются в историю выполнения. Ход создания образа можно отслеживать с помощью этих журналов.

- **Постобработка**. Каталог выполнения ./outputs копируется в журнал выполнения в рабочей области, чтобы вы могли получить доступ к результатам.


Вы можете проверить ход выполнения запущенного задания несколькими способами. В этом руководстве используется мини-приложение Jupyter, а также метод `wait_for_completion`. 

### <a name="jupyter-widget"></a>Мини-приложение Jupyter

Отслеживайте ход выполнения с помощью мини-приложения Jupyter.  Подобно представлению запуска, мини-приложение является асинхронным и предоставляет обновления в реальном времени каждые 10–15 секунд до завершения задания.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Вот еще снимок мини-приложения, показанного в конце обучения.

![мини-приложение "Блокнот"](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Получение журнала результатов по завершении

Обучение и мониторинг модели происходит в фоновом режиме. Перед выполнением кода подождите, пока модель закончит обучение. Используйте `wait_for_completion`, чтобы показать, когда завершится обучение модели. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Отображение результатов потокового выполнения

Теперь у вас есть модель, которую обучили на удаленном кластере.  Извлеките точность модели, выполнив такую команду.

```python
print(run.get_metrics())
```
Выходные данные показывают, что удаленная модель имеет точность, немного превышающую локальную модель в связи с добавлением скорости регуляризации во время обучения.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

В руководстве развертывания эта модель будет изучена более подробно.

## <a name="register-model"></a>Регистрация модели

На последнем шаге сценария обучения файл `outputs/sklearn_mnist_model.pkl` был записан в каталог с именем `outputs` на виртуальной машине кластера, где выполнялось задание. `outputs` — это специальный каталог, в котором все содержимое автоматически отправляется в рабочую область.  Это содержимое отображается в записи о выполнении эксперимента в рабочей области. Таким образом, файл модели доступен также в рабочей области.

Вы увидите файлы, связанные с выполнением.

```python
print(run.get_file_names())
```

Зарегистрируйте модель в рабочей области, чтобы вы (или другие участники совместной работы) могли позже запросить, проверить и развернуть ее.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Управляемый вычислительный кластер Azure можно удалить. Тем не менее, поскольку включено автомасштабирование, а минимальное значение кластера — 0, за данный ресурс не будет взиматься дополнительная расчетная плата.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве по Машинному обучению Azure вы использовали Python для выполнения следующих задач.

> [!div class="checklist"]
> * Настройка среды разработки
> * Подключение и проверка данных
> * Локальное обучение простой логистической регрессии с помощью популярной библиотеки машинного обучениях scikit-learn
> * Обучение нескольких моделей на удаленном кластере
> * Проверка подробностей обучения и регистрация наилучшей модели

Развернуть эту зарегистрированную модель можно с помощью инструкций в следующей части серии руководств.

> [!div class="nextstepaction"]
> [Руководство 2. Развертывание моделей](tutorial-deploy-models-with-aml.md)