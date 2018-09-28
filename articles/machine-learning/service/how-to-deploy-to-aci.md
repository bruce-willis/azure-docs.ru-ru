---
title: Развертывание веб-служб в экземплярах контейнеров Azure — машинное обучение Azure
description: Сведения о развертывании обученной модели в качестве веб-службы в экземплярах контейнеров Azure с помощью службы машинного обучения Azure. В этой статье показаны три различных способа развертывания модели в экземплярах контейнеров Azure. Они различаются количеством строк кода и уровнем контроля над именованием компонентов развертывания.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 5a62d4b0b324d8b2536e408132210f07f08e8bb8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958702"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Развертывание веб-служб в экземплярах контейнеров Azure 

Обученную модель можно развернуть как веб-службу в [экземплярах контейнеров Azure](https://azure.microsoft.com/services/container-instances/), [службе Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) (AKS), на устройстве IoT Edge или в [программируемых пользователем вентильных матрицах (ППВМ)](concept-accelerate-with-fpgas.md). 

Развертывание в экземплярах контейнеров Azure, как правило, дешевле, чем в AKS, и его можно настроить с помощью 4–6 строк кода. Экземпляры контейнеров Azure — оптимальный вариант для тестирования развертываний. Позднее, когда вы будете готовы использовать свои модели и веб-службы для масштабного использования в рабочей среде, вы можете [развернуть их в AKS](how-to-deploy-to-aks.md).

В этой статье показаны три различных способа развертывания модели в экземплярах контейнеров Azure. Они различаются количеством строк кода и уровнем контроля над именованием компонентов развертывания. Ниже эти способы перечислены в порядке увеличения объема кода и повышения уровня контроля:

* развертывание из файла модели с помощью метода `Webservice.deploy()`; 
* развертывание из зарегистрированной модели с помощью метода `Webservice.deploy_from_model()`;
* развертывание зарегистрированной модели из образа с помощью метода `Webservice.deploy_from_image()`.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a name="prerequisites"></a>Предварительные требования

- Должны быть установлены рабочая область машинного обучения Azure и пакет SDK машинного обучения Azure для Python. Узнать, как получить эти компоненты, можно в [кратком руководстве по началу работы с машинным обучением Azure](quickstart-get-started.md).

- Объект рабочей области машинного обучения Azure.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Развертываемая модель. В примерах в этой статье используется модель, созданная при выполнении руководства по [обучению модели](tutorial-train-models-with-aml.md). Если вы используете другую модель, при выполнении инструкций ссылайтесь на ее имя.  Для выполнения вашей модели также необходимо написать собственный скрипт оценки.


## <a name="configure-an-image"></a>Настройка образа

Настройте образ Docker, служащий для хранения всех файлов модели.
1. Создайте скрипт оценки (score.py), [следуя этим инструкциям](tutorial-deploy-models-with-aml.md#create-scoring-script).

1. Создайте файл среды (myenv.yml), [следуя этим инструкциям](tutorial-deploy-models-with-aml.md#create-environment-file). 

1. Используйте эти два файла для настройки образа Docker на Python с помощью пакета SDK следующим образом:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Настройка контейнера в службе экземпляров контейнеров Azure

Настройте контейнер в службе экземпляров контейнеров Azure, указав необходимое количество ЦП и объем ОЗУ в гигабайтах. Для большинства моделей достаточно значений по умолчанию (одно ядро и 1 гигабайт ОЗУ). Если в дальнейшем вам потребуется больше ресурсов, создайте образ еще раз и повторно разверните службу.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Регистрация модели

> Если [развертывание производится из файла модели](#deploy-from-model-file) (с помощью метода`Webservice.deploy()`), этот шаг можно пропустить.

Зарегистрируйте модель так, чтобы она использовала [`Webservice.deploy_from_model`](#deploy-from-registered-model) или [`Webservice.deploy_from_image`](#deploy-from-image). Если у вас уже есть зарегистрированная модель, извлеките ее.

### <a name="retrieve-a-registered-model"></a>Извлечение зарегистрированной модели
Если для обучения модели используется машинное обучение Azure, модель может быть уже зарегистрирована в вашей рабочей области.  Например, модель была зарегистрирована в последнем шаге руководства по [обучению модели](tutorial-train-models-with-aml.md).  В этом случае необходимо извлечь зарегистрированную модель для развертывания.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Регистрация файла модели

Если модель была создана где-то еще, ее также можно зарегистрировать в рабочей области.  Чтобы модель можно было зарегистрировать, файл модели (в этом примере `sklearn_mnist_model.pkl`) должен находиться в текущей рабочей папке. Зарегистрируйте этот файл в рабочей области как модель `sklearn_mnist` с помощью метода `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```


## <a name="option-1-deploy-from-model-file"></a>Вариант 1. Развертывание из файла модели

При развертывании из файла модели требуется писать меньше всего кода, но и уровень контроля над именованием компонентов наиболее низкий. Такой подход предполагает получение файла модели и его регистрацию в рабочей области.  Однако вы не можете присвоить имя модели или связать с ней теги либо описание.  

В этом случае применяется метод SDK Webservice.deploy().  

**Примерное время**: развертывание занимает приблизительно 6–7 минут.

1. Файл модели должен находиться в вашей локальной рабочей папке.

1. Откройте файл модели score.py и измените содержимое раздела `init()` следующим образом:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Разверните файл модели.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Теперь можно [протестировать веб-службу](#test-web-service).

## <a name="option-2-deploy-from-registered-model"></a>Вариант 2. Развертывание из зарегистрированной модели

При развертывании зарегистрированного файла модели требуется немного больше строк кода, но обеспечивается некоторый контроль над именованием выходных компонентов. Это удобный способ для развертывания уже имеющейся зарегистрированной модели.  Однако присвоить имя образу Docker нельзя.  

В этом случае применяется метод SDK Webservice.deploy_from_model().

**Примерное время**: развертывание занимает приблизительно 8 минут.

1. Выполните код для настройки контейнера Docker и контейнера службы экземпляров контейнеров Azure и укажите зарегистрированную модель.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Теперь можно [протестировать веб-службу](#test-web-service).

## <a name="option-3-deploy-from-image"></a>Вариант 3. Развертывание из образа

Разверните зарегистрированную модель (`model`) с помощью метода `Webservice.deploy_from_image()`. Он позволяет создать образ Docker отдельно, а затем выполнить развертывание из него.

1. Выполните сборку образа Docker и зарегистрируйте его в рабочей области с помощью метода `ContainerImage.create()`.

    Создавая образ в отдельном шаге, вы можете лучше управлять им.  Зарегистрированная модель (`model`) включается в этот образ.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Примерное время**: приблизительно 3 минуты.

1. Разверните образ Docker как службу с помощью метода `Webservice.deploy_from_image()`.

    Теперь разверните образ в экземплярах контейнеров Azure.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Примерное время**: приблизительно 3 минуты.

Этот способ обеспечивает наибольший контроль над созданием и именованием компонентов развертывания.

Теперь можно протестировать веб-службу.

## <a name="test-the-web-service"></a>Тестирование веб-службы

Веб-служба будет одинаковой, независимо от применяемого способа.  Для получения прогнозов используйте метод `run` службы.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать эту веб-службу, удалите ее, чтобы не платить за ее использование.

```python
service.delete()
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как произвести масштабное [развертывание в службе Azure Kubernetes](how-to-deploy-to-aks.md). 
