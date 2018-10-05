---
title: ONNX и машинное обучение Azure | Создание и развертывание моделей
description: Дополнительные сведения об ONNX и использовании машинного обучения Azure для создания и развертывания моделей ONNX
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: d4ce2dc67b0d9229ac2605ab317594ea345c19b2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434081"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX и машинное обучение Azure: создание и развертывание совместимых моделей AI

Формат [Open Neural Network Exchange](http://onnx.ai) (ONNX) — это открытый стандарт для представления моделей машинного обучения. ONNX поддерживается [сообществом партнеров](http://onnx.ai/supported-tools), включая корпорацию Майкрософт, которое создает совместимые платформы и средства. Корпорация Майкрософт стремится к созданию открытого, совместимого AI, чтобы ученые и разработчики могли делать следующее:

+ использовать для создания и обучения моделей платформу по своему выбору;
+ развертывать кроссплатформенные модели с минимальной работой над интеграцией.

Чтобы помочь вам добиться этих целей, корпорация Майкрософт реализовала поддержку ONNX во всех своих продуктах, включая Azure и Windows.  

## <a name="why-choose-onnx"></a>Для чего нужен ONNX?
ONNX дает совместимость, которая позволяет быстрее запускать хорошие идеи в производство. Благодаря ONNX специалисты по обработке и анализу данных могут выбирать для своей работы предпочитаемую платформу. А разработчикам он позволяет тратить меньше времени на подготовку моделей к производству и развертывание в облаке и Edge.  

Модели ONNX можно экспортировать из самых разных платформ, включая PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet и ML.Net. Для таких платформ, как TensorFlow, Keras, SciKit-Learn и многие другие существуют конвертеры.

Также имеется экосистема средств для визуализации и ускорения моделей ONNX. Для распространенных сценариев доступен также ряд предварительно обученных моделей ONNX.

[Модели ONNX можно развертывать](#deploy) в облако с помощью машинного обучения Azure и среды выполнения ONNX. С помощью [Windows ML](https://docs.microsoft.com/windows/ai/) их можно также развертывать на устройствах Windows 10. В сообществе ONNX существуют также конвертеры для развертывания на другие платформы. 

[ ![Схема ONNX, демонстрирующая обучение, конвертеры и развертывание](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

## <a name="create-onnx-models-in-azure"></a>Создание моделей ONNX в Azure

Модели ONNX можно создавать различными способами:
+ Обучение модели в службе машинного обучения Azure и преобразование или экспорт этой модели в ONNX (см. пример в далее в этой статье).

+ Получение предварительно обученной модели ONNX из [коллекции моделей ONNX](https://github.com/onnx/models).

+ Создание настраиваемых моделей ONNX в [пользовательской службе визуального распознавания Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="exportconvert-your-models-to-onnx"></a>Экспорт/преобразование моделей в ONNX

Существующие модели можно преобразовывать в ONNX.

|Платформа модели|Пример или средство преобразования|
|-----|-------|
|PyTorch|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[Преобразователь tensorflow-onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost и libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Актуальный список поддерживаемых платформ и преобразователей см. на [сайте с руководствами ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Развертывание моделей ONNX в Azure

С помощью службы машинного обучения Azure можно развертывать модели ONNX, управлять ими и осуществлять мониторинг. Используя стандартный [рабочий процесс развертывания](concept-model-management-and-deployment.md) и среду выполнения ONNX, можно создать конечную точку REST, размещенную в облаке. Полный пример записной книжки Jupyter см. в конце этой статьи — опробуйте его на практике. 

### <a name="install-and-configure-the-onnx-runtime"></a>Установка и настройка среды выполнения ONNX

Среда выполнения ONNX — это высокоэффективный механизм логической обработки для моделей ONNX. Она включает API Python и обеспечивает аппаратное ускорение ЦП и GPU. Сейчас она поддерживает модели ONNX 1.2 и выполняется в системе Ubuntu 16.04 Linux. Пакеты [ЦП](https://pypi.org/project/onnxruntime) и [GPU](https://pypi.org/project/onnxruntime-gpu) доступны на сайте [PyPi.org](https://pypi.org).

Для установки среды выполнения ONNX используйте следующую команду:
```python
pip install onnxruntime
```

Для вызова среды выполнения ONNX в сценарии Python используйте следующую команду:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

Входные и выходные данные для использования модели см. в документации к соответствующей модели. Для просмотра модели можно также использовать такое средство визуализации, как [Netron](https://github.com/lutzroeder/Netron). Среда выполнения ONNX также позволяет запрашивать метаданные модели и ее входные и выходные данные:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Для вывода модели добавьте параметр `run` и передайте список выходных данных, которые хотите получить (не заполняйте, если вам нужны все), а также карту входных значений. Результатом будет список выходных данных.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Полный справочник по API см. в [документации по среде выполнения ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Пример этапов развертывания

Приведем пример развертывания модели ONNX:

1. Инициализируйте рабочую область машинного обучения Azure. Если ее пока нет, узнайте, как создать рабочую область, в [этом кратком руководстве](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Зарегистрируйте модель в машинном обучении Azure.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Создание образа с моделью и зависимыми компонентами.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Файл `score.py` содержит логику оценки и должен быть добавлен в образ. Этот файл используется для запуска модели в образе. Инструкции по созданию сценария оценки см. в этом [руководстве](tutorial-deploy-models-with-aml.md#create-scoring-script). Пример файла для модели ONNX см. ниже:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Файл `myenv.yml` описывает необходимые для образа зависимости. Инструкции по созданию файла среды, такого как в следующем примере, см. в [этом руководстве](tutorial-deploy-models-with-aml.md#create-environment-file).

   ```
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. С помощью машинного обучения Azure разверните модель ONNX в:
   + Экземпляры контейнеров Azure: [подробнее](how-to-deploy-to-aci.md).

   + Служба Azure Kubernetes (AKS): [подробнее...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Примеры
 
Следующие записные книжки показывают, как разворачивать модели ONNX с помощью машинного обучения Azure: 
+ `/onnx/onnx-inference-mnist.ipynb`
 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
Получите эту записную книжку:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Подробнее

Узнайте больше об ONNX или поддержите проект:
+ [Веб-сайт проекта ONNX](http://onnx.ai)

+ [Код ONNX в GitHub](https://github.com/onnx/onnx)
