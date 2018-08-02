---
title: Создайте и разверните модель определения объектов, используя пакет Машинного обучения Azure для компьютерного зрения.
description: Узнайте, как создавать, обучать, тестировать и развертывать модель определения объектов компьютерного зрения, используя пакет Машинного обучения Azure для компьютерного зрения.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 44059de5a0ef0667b4268d9cdc2997162bab474a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295373"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Создание и развертывание моделей определения объектов с помощью машинного обучения Azure

В этой статье вы узнаете, как обучать, тестировать и развертывать модели определения объектов [Faster R-CNN](https://arxiv.org/abs/1506.01497) с помощью **пакета Машинного обучения Azure для компьютерного зрения**. 

Множество проблем в области компьютерного зрения можно решить, используя определение объектов. Эти проблемы включают в себя создание моделей, которые находят переменное количество объектов на изображении. 

При создании и развертывании модели с помощью этого пакета выполните следующие действия:
1.  Создание набора данных.
2.  Определение модели глубокой нейронной сети (DNN).
3.  Обучение модели
4.  Оценка и визуализация.
5.  Развертывание веб-службы.
6.  Нагрузочное тестирование веб-службы.

В этом примере используется TensorFlow как платформа глубокого обучения, которое выполняется локально на компьютере с графическим процессором, например [виртуальная машина для обработки и анализа данных для глубокого обучения](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), а в развертывании используется CLI для ввода Azure ML в эксплуатацию.

Ознакомьтесь со [справочной документацией](https://aka.ms/aml-packages/vision) с подробным описанием каждого модуля и класса.

## <a name="prerequisites"></a>Предварительные требования

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

1. Необходимо настроить и установить следующие учетные записи и приложения:
   - Учетная запись службы "Экспериментирование в Машинном обучении Azure". 
   - Учетная запись Управления моделями Машинного обучения Azure.
   - Установленное приложение Azure Machine Learning Workbench.

   Если они еще не созданы или не установлены, следуйте инструкциям в статье [Краткое руководство. Установка и начало работы со службами Машинного обучения Azure](../service/quickstart-installation.md). 

1. Необходимо установить пакет Машинного обучения Azure для компьютерного зрения. Узнайте, как [установить этот пакет здесь](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Демонстрационные данные и записная книжка

### <a name="get-the-jupyter-notebook"></a>Получение Jupyter Notebook

Скачайте записную книжку, чтобы самостоятельно запускать примеры, описанные здесь.

> [!div class="nextstepaction"]
> [Получение Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Загрузка демонстрационных данных

Для этой демонстрации предусмотрен набор данных о продуктах внутри холодильников, состоящий из 30 изображений и 8 классов (яичный лоток, йогурт, кетчуп, гриб, горчица, апельсин, лимонад и вода). Для каждого изображения в формате JPG есть XML-файл аннотации с аналогичным именем. 

На следующем рисунке показана рекомендуемая структура папок. 

![Структура папок](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Аннотация изображения

Для обучения и оценки объектного детектора необходимы расположения описанных объектов. [LabelImg](https://tzutalin.github.io/labelImg) — это средство аннотирования с открытым кодом, которое может использоваться для аннотирования изображений. LabelImg записывает XML-файл для каждого изображения в формате Pascal-VOC, которое может быть прочитано этим пакетом. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Создание набора данных

Создайте набор данных CVTK, который состоит из набора изображений с соответствующими аннотациями в ограничивающей рамке. В этом примере используются изображения холодильника, которые содержатся в папке "../sample_data/foods/training". Поддерживаются только изображения в формате JPEG.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Определение модели

В этом примере используется модель Faster R-CNN. При определении этой модели могут быть предоставлены различные параметры. Значение этих параметров, а также параметры, используемые для обучения (см. в следующий раздел), можно найти в документах API CVTK или на [сайте обнаружения объектов Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Более подробные сведения о модели Faster R-CNN можно найти по [этой ссылке](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Эта модель основана на Fast R-CNN. Более подробные сведения об этом можно найти [здесь](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Обучение модели

В качестве отправной точки для обучения используется обученная COCO модель Faster R-CNN с ResNet50. 

Для обучения средства обнаружения количество шагов обучения в коде устанавливается равным 350, так что обучение выполняется быстрее (~5 минут при использовании графического процессора). На практике ему следует задать число как минимум в 10 раз больше, чем количество изображений в наборе обучения.

В этом примере количество шагов обучения для средства обнаружения составляет 350 для ускорения обучения. Однако на практике имеет смысл установить шаги в 10 или более раз больше, чем количество изображений в наборе обучения.

Существуют два ключевых параметра для обучения:
- Количество шагов для обучения модели, представленные аргументом num_seps. Каждый шаг обучает модель с мини-пакетом размера 1.
- Скорость обучения, которая может быть установлена с помощью ​​initial_learning_rate.

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

Для визуализации прогресса обучения можно использовать TensorBoard. События TensorBoard находятся в папке, указанной атрибутом train_dir объекта модели. Чтобы просмотреть TensorBoard, выполните следующие действия:
1. Скопируйте распечатку, начинающуюся с tensorboard --logdir, в командную строку и запустите ее. 
2. Скопируйте возвращенный URL-адрес из командной строки в веб-браузер, чтобы просмотреть TensorBoard. 

TensorBoard должен выглядеть примерно так: Для заполнения папки обучения требуется несколько минут. Поэтому если TensorBoard не отображается правильно, попробуйте повторить шаги 1–2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Анализ модели

Метод evaluate используется для оценки модели. Этой функции требуется ObjectDetectionDataset в качестве объекта. Набор данных оценки может быть создан с использованием той же функции, что и для набора данных обучения. Поддерживаемая метрика — это Average Precision, определенная для [PASCAL VOC Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Результаты оценки могут быть распечатаны в чистом формате.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Точно так же вы можете вычислить точность модели в наборе обучения. Это помогает убедиться, что обучение преобразовывается в хорошее решение. Точность в наборе обучения после успешного обучения часто близка к 100 %.

Результаты оценки можно также просмотреть из TensorBoard, включая значения mAP и изображения с предсказанными ограничивающими прямоугольниками. Скопируйте распечатку из следующего кода в окно командной строки, чтобы запустить клиент TensorBoard. Здесь используется значение порта 8008, чтобы избежать конфликта со значением по умолчанию 6006, которое использовалось для просмотра состояния обучения.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Оценка изображения

Как только вы будете удовлетворены работой обучаемой модели, функция score объекта модели может использоваться для оценки новых изображений. Возвращенные результаты оценки можно визуализировать с помощью функции visualize. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Сохранение модели

Обученная модель может быть сохранена на диск и загружена обратно в память, как показано в следующих примерах кода.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Загрузка сохраненной модели для оценки

Чтобы использовать сохраненную модель, загрузите модель в память с помощью функции load. Необходимо выполнить загрузку один раз. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

После загрузки модели ее можно использовать для оценки изображения или списка изображений. Для одного изображения возвращается словарь с такими ключами, как detect_boxes, detect_scores и num_detections. Если входные данные представляют собой список изображений, возвращается список словарей, причем один словарь соответствует одному изображению. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Обнаруженные объекты с оценками выше 0,5, включая метки, оценки и координаты, могут быть распечатаны.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Визуализируйте оценки так же, как и раньше.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Ввод в эксплуатацию: развертывание и использование

Ввод в эксплуатацию — это процесс публикации моделей и кода в виде веб-служб и их использование для получения бизнес-результатов. 

После обучения модели ее можно развернуть в качестве веб-службы для использования с помощью [интерфейса командной строки службы "Машинное обучение Azure"](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Модели можно развернуть на локальном компьютере или в контейнере Службы контейнеров Azure (ACS). Используя ACS, вы можете масштабировать веб-службу вручную или при помощи функции автомасштабирования.

**Вход с помощью Azure CLI**

Используя учетную запись [Azure](https://azure.microsoft.com/) с действительной подпиской, войдите в систему, выполнив следующую команду CLI:
<br>`az login`

+ Чтобы переключить подписку Azure, используйте команду:
<br>`az account set --subscription [your subscription name]`

+ Для просмотра текущей учетной записи управления моделью используйте команду:
  <br>`az ml account modelmanagement show`

**Создание и настройка среды развертывания кластера**

Вам нужно установить среду развертывания только один раз. Если у вас ее еще нет, настройте среду развертывания, используя [эти инструкции](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Для просмотра среды активного развертывания используйте следующую команду CLI:
<br>`az ml env show`
   
Пример команды Azure CLI для создания и установки среды развертывания

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Управление веб-службами и развертываниями

Следующие API используются для развертывания моделей в виде веб-служб, управления этими веб-службами и развертываниями.

|Задача|API|
|----|----|
|Создание объекта развертывания|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Развертывание веб-службы|`deploy_obj.deploy()`|
|Оценка изображения|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Удаление веб-службы|`deploy_obj.delete()`|
|Создание образа docker без веб-службы|`deploy_obj.build_docker_image()`|
|Получение списка имеющихся развертываний|`AMLDeployment.list_deployment()`|
|Удаление (если имеется служба с именем развертывания)|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Документация по API**. Ознакомьтесь со [справочной документацией](https://aka.ms/aml-packages/vision) с подробным описанием каждого модуля и класса.

**Справочник по CLI**. Более сложные операции, связанные с развертыванием, см. в [справочнике по CLI управления моделью](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Управление развертыванием на портале Azure**. Вы можете отслеживать и управлять развертываниями на [портале Azure](https://ms.portal.azure.com/). На портале Azure найдите страницу учетной записи Управления моделями Машинного обучения с помощью ее имени. Затем перейдите на страницу учетной записи Управления моделями и выберите "Управление моделями > Службы".

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Использование веб-службы

После создания веб-службы вы можете оценивать изображения с помощью развернутой веб-службы. Доступно несколько параметров.

   - Вы можете напрямую оценить веб-службу в объекте развертывания с помощью: deploy_obj.score_image (image_path_or_url) 
   - Или вы можете использовать URL-адрес конечной точки и ключ службы (отсутствует для локального развертывания) с помощью: AMLDeployment.score_existing_service_with_image (image_path_or_url, service_endpoint_url, service_key=None)
   - Создавайте свои HTTP-запросы напрямую, чтобы получить оценку конечной точки веб-службы (для продвинутых пользователей).

### <a name="score-with-existing-deployment-object"></a>Оценка с помощью имеющегося объекта развертывания
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Оценка с помощью URL-адреса конечной точки и ключа службы
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Оценка конечной точки с использованием HTTP-запроса непосредственно
Ниже приведен пример кода для формирования HTTP-запроса непосредственно в Python. Однако это можно сделать на других языках программирования.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Синтаксический анализ сериализованных данных из веб-службы
Результат веб-службы находится в строке json, которая может быть проанализирована.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о пакете Машинного обучения Azure для компьютерного зрения в этих статьях:

+ Ознакомьтесь с [обзором пакета и узнайте, как его установить](https://aka.ms/aml-packages/vision).

+ Изучите [справочную документацию](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) для этого пакета.

+ Узнайте о [других пакетах Python для Машинного обучения Azure](reference-python-package-overview.md).
