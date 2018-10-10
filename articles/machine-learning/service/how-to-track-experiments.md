---
title: Отслеживание экспериментов и метрик обучения в службе "Машинное обучение Azure" | Документация Майкрософт
description: С помощью службы "Машинное обучение Azure" можно отслеживать свои эксперименты и метрики, чтобы улучшить процесс создания модели. Узнайте, как добавить ведение журнала в сценарий обучения, отправить эксперимент, проверить прогресс выполняемого задания и просмотреть результаты выполнения.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: ced10a54d569531b06ee47b646130f43cedd2963
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984608"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Отслеживание экспериментов и метрик обучения в службе "Машинное обучение Azure"

В службе "Машинное обучение Azure" можно отслеживать эксперименты и метрики, чтобы улучшить процесс создания модели. В этой статье вы узнаете о различных способах добавления ведения журнала в сценарий обучения, а также о том, как отправлять эксперимент с помощью команды **start_logging** и класса **ScriptRunConfig**, проверить прогресс выполняемого задания и просмотреть результаты выполнения. 

## <a name="list-of-training-metrics"></a>Список метрик обучения 

Следующие метрики можно добавить к выполнению во время обучения эксперимента. Чтобы просмотреть более подробный список того, что можно отслеживать в выполнении, ознакомьтесь со [справочной документацией по пакету SDK](https://docs.microsoft.com/python/api/overview/azure/azure-ml-sdk-overview?view=azure-ml-py).

|type| Функция Python | Примечания|
|----|:----:|:----:|
|Скалярные значения | `run.log(name, value, description='')`| Запишите значение метрики в выполнение с заданным именем. Запись метрики в выполнение приводит к тому, что метрика будет сохранена в записи о выполнении в эксперименте.  Одну и ту же метрику можно несколько раз записать в рамках выполнения. Результат будет считаться вектором этой метрики.|
|Списки| `run.log_list(name, value, description='')`|Запишите список значений метрик в выполнение с заданным именем.|
|Строка| `run.log_row(name, description=None, **kwargs)`|С помощью *log_row* создается метрика таблицы со столбцами, как описано в kwargs. Каждый именованный параметр создает столбец с указанным значением.  *log_row* можно вызвать один раз, чтобы записать произвольный кортеж, или несколько раз в цикле, чтобы создать полную таблицу.|
|Таблица| `run.log_table(name, value, description='')`| Запишите значение табличной метрики в выполнение с заданным именем. |
|Образы| `run.log_image(name, path=None, plot=None)`|Запишите метрику изображения в запись о выполнении. Используйте log_image, чтобы записать файл изображения или график matplotlib в выполнение.  Эти изображения будут видимы, и их можно сравнить в записи о выполнении.|
|Добавление тега к выполнению| `run.tag(key, value=None)`|Добавление тега к выполнению с использованием строкового ключа и дополнительного значения строки.|
|Отправка файла или каталога|`run.upload_file(name, path_or_stream)`|Отправка файла в запись о выполнении. Выполняет автоматическую запись файла в указанном каталоге вывода, который по умолчанию имеет значение "./outputs" для большинства типов выполнения.  Используйте upload_file только в том случае, если необходимо отправить дополнительные файлы или не указан выходной каталог. Мы советуем добавлять `outputs` к имени для того, чтобы файл отправлялся в выходной каталог. Вы можете перечислить все файлы, связанные с этой записью о выполнении, вызвав `run.get_file_names()`.|

> [!NOTE]
> Метрики для скалярных значений, списков, строк и таблиц могут быть типа: число с плавающей точкой, целое число или строка.

## <a name="log-metrics-for-experiments"></a>Запись метрик для экспериментов

Если вы хотите отслеживать свой эксперимент, необходимо добавить код, чтобы запустить ведение журнала при отправке выполнения. Ниже приведены способы активации отправки выполнений:
* __Run.start_logging__ добавляет функции регистрации в сценарий обучения и запускает интерактивный сеанс регистрации в определенном эксперименте. **start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.
* __ScriptRunConfig__ добавляет функции ведения журнала в сценарий обучения и загружает всю папку сценария с выполнением.  **ScriptRunConfig** является классом для настройки конфигурации выполнений в сценарии. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

## <a name="set-up-the-workspace-and-experiment"></a>Настройка рабочей области и эксперимента
Перед добавлением ведения журнала и отправки эксперимента необходимо настроить рабочую область и эксперимент.

1. Загрузите рабочую область. Дополнительные сведения о настройке конфигурации рабочей области см. в [этом кратком руководстве](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. Создайте эксперимент.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  exp = Experiment(workspace_object = ws, name = experiment_name)
  ```
  
## <a name="option-1-use-startlogging"></a>Вариант 1. Использование start_logging

**start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.

В следующем примере простая модель sklearn Ridge обучается локально в локальной записной книжке Jupyter. Дополнительные сведения об отправке экспериментов в различные среды см. в статье [Выбор и использование целевого объекта вычислений для обучения вашей модели](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Создайте сценарий обучения в локальной записной книжке Jupyter. 

  ``` python
  # load diabetes dataset, a well-known small dataset that comes with scikit-learn
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from sklearn.externals import joblib

  X, y = load_diabetes(return_X_y = True)
  columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
  }
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl');
  ```

2. Добавьте отслеживание эксперимента с помощью пакета SDK для службы "Машинное обучение Azure" и отправьте сохраненную модель в запись о выполнении для эксперимента. Следующий код добавляет теги, журналы и отправляет файл модели в выполнение эксперимента.

  ```python
  run = Run.start_logging(experiment = exp)
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

Сценарий оканчивается на ```run.complete()```, что помечает выполнение как завершенное.  Обычно используется в сценариях интерактивной записной книжки.

## <a name="option-2-use-scriptrunconfig"></a>Вариант 2. Использование ScriptRunConfig

**ScriptRunConfig** является классом для настройки конфигурации выполнений в сценарии. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

В этом примере расширяется указанная выше базовая модель sklearn Ridge. В нем выполняется простая замена параметров по альфа-значениям модели для записи метрик и обученных моделей в выполнениях в рамках эксперимента. Пример запускается локально в управляемой пользователем среде. 

1. Создайте сценарий обучения. Для записи в папку сценария кода обучения в виде файла ```train.py``` используется ```%%writefile%%```.

  ```python
  %%writefile $project_folder/train.py

  import os
  from sklearn.datasets import load_diabetes
  from sklearn.linear_model import Ridge
  from sklearn.metrics import mean_squared_error
  from sklearn.model_selection import train_test_split
  from azureml.core.run import Run
  from sklearn.externals import joblib

  import numpy as np

  #os.makedirs('./outputs', exist_ok = True)

  X, y = load_diabetes(return_X_y = True)

  run = Run.get_submitted_run()

  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
  data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

  # list of numbers from 0.0 to 1.0 with a 0.05 interval
  alphas = np.arange(0.0, 1.0, 0.05)

  for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
  ```

2. Сценарий ```train.py``` ссылается на ```mylib.py```. Этот файл позволяет получить список альфа-значений для использования в модели ridge.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Настройте управляемую пользователем локальную среду.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Отправьте сценарий ```train.py``` для выполнения в среде, управляемой пользователем. Вся папка сценария отправляется для обучения, в том числе файл ```mylib.py```.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_user_managed)
  run = exp.submit(src)
  ```
  
## <a name="view-run-details"></a>Просмотр сведений о выполнении

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Мониторинг выполнения с помощью мини-приложений записных книжек Jupyter
При использовании метода **ScriptRunConfig** для отправки выполнений можно просмотреть прогресс выполнения с помощью мини-приложения записной книжки Jupyter. Подобно представлению запуска, мини-приложение является асинхронным и предоставляет обновления в реальном времени каждые 10–15 секунд до завершения задания.

1. Просмотрите мини-приложения Jupyter во время ожидания завершения выполнения.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Снимок экрана. Мини-приложение записной книжки Jupyter](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Получение результатов записи по завершении

Обучение модели и мониторинг происходят в фоновом режиме, так что вы можете выполнять другие задачи во время ожидания. Также перед выполнением кода можно подождать, пока модель закончит обучение. При использовании **ScriptRunConfig** можно указать ```run.wait_for_completion(show_output = True)```, чтобы узнать, когда обучение модели будет завершено. Флаг ```show_output``` предоставляет подробные выходные данные. 
  
### <a name="query-run-metrics"></a>Запрос метрик выполнения

Метрики обученной модели можно просмотреть с помощью ```run.get_metrics()```. Теперь можно получить все метрики, которые были зарегистрированы в примере выше, чтобы определить оптимальную модель.

## <a name="view-the-experiment-in-the-azure-portal"></a>Просмотр эксперимента на портале Azure

После завершения эксперимента можно просмотреть записи о выполнении в эксперименте. Это можно сделать двумя способами.

* Получить URL-адрес для перехода к выполнению напрямую: ```print(run.get_portal_url())```.
* Просмотреть сведения о выполнении, отправив имя выполнения (в этом случае ```run```). В результате вы получаете имя эксперимента, идентификатор, тип, состояние, страницу сведений, ссылку на портал Azure и ссылку на документацию.

Ссылка для выполнения переведет вас на страницу сведений о выполнении на портале Azure. Здесь отображаются все свойства, отслеживаемые метрики, изображения и диаграммы, которые были зарегистрированы во время эксперимента. В этом случае мы регистрируем MSE и альфа-значения.

  ![Снимок экрана. Сведения о выполнении на портале Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

Вы также можете просмотреть любые выходные данные или журналы выполнения, а также скачать моментальный снимок отправленного эксперимента для предоставления общего доступа к папке эксперимента.

## <a name="example-notebooks"></a>Примеры записных книжек
Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:
* `01.getting-started/01.train-within-notebook/01.train-within-notebook.ipynb`
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`

Получите записные книжки: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

Попробуйте следующие шаги, чтобы узнать, как использовать пакет SDK службы "Машинное обучение Azure" для Python:

* Пример регистрации оптимальных моделей и их развертывания см. в руководстве [Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure](tutorial-train-models-with-aml.md).

* Дополнительные сведения о том, как обучать модели PyTorch с помощью службы "Машинное обучение Azure", см. в [этой статье](how-to-train-pytorch.md).
