---
title: Классификация изображений с помощью пакета машинного обучения Azure для компьютерного зрения и процесса обработки и анализа данных группы (TDSP) | Документы Майкрософт
description: В этой статье описана классификация изображений с использованием процесса обработки и анализа данных группы (TDSP) и пакета машинного обучения Azure для компьютерного зрения.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300805"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Классификация изображений рака кожи с помощью пакета машинного обучения Azure для компьютерного зрения и процесса обработки и анализа данных группы

Из этой статьи вы узнаете, как обучать, тестировать и развертывать модели *классификации изображений* с помощью [пакета машинного обучения Azure для компьютерного зрения](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest). В этом примере применяется структура и шаблоны процесса обработки и анализа данных группы (TDSP) в [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). В этом пошаговом руководстве приведен полный пример. Он использует [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) в качестве платформы глубокого обучения, которое выполняется на [виртуальной машине для обработки и анализа данных](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), развернутой на компьютере с GPU. В развертывании используется CLI ввода в эксплуатацию машинного обучения Azure.

Множество задач в области компьютерного зрения можно реализовать, используя классификацию изображений. К ним относятся создание моделей, которые отвечают на простые вопросы, например: "Присутствует ли объект на изображении?". В качестве объекта могут быть изображены собака, автомобиль или корабль. Это также могут быть более сложные вопросы, например: "Какой класс тяжести глазной болезни обнаруживается при сканировании сетчатки пациента?". Пакет машинного обучения Azure для компьютерного зрения упрощает обработку данных для классификации изображений и процесс моделирования. 

## <a name="link-to-the-github-repository"></a>Ссылка на репозиторий на GitHub
В этой статье приведены общие сведения о примере. Более подробную документацию можно найти на [сайте GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Пошаговые руководства по процессу обработки и анализа данных группы

В руководстве используется [жизненный цикл процесса обработки и анализа данных группы](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview). В рамках этого руководства рассматриваются следующие шаги жизненного цикла.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Сбор данных](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Для задачи классификации изображений используется набор данных International Skin Imaging Collaboration (ISIC). ISIC — это партнерство между научными и промышленными организациями, созданное с целью облегчения анализа цифровых изображений кожи для изучения и снижения смертности от меланомы. Архив [ISIC](https://isic-archive.com/#images) содержит более 13 000 изображений повреждений кожи с метками о доброкачественности или злокачественности. Скачайте примеры изображений из архива ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Моделирование](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling).
На этапе моделирования выполняются следующие шаги.

#### <a name="dataset-creation"></a>Создание набора данных

Создайте объект набора данных с помощью пакета машинного обучения Azure для компьютерного зрения, указав корневой каталог изображений на локальном диске. 

#### <a name="image-visualization-and-annotation"></a>Визуализация изображений и создание заметок

Визуализируйте изображения в объекте набора данных и при необходимости исправьте некоторые метки.

#### <a name="image-augmentation"></a>Дополнение изображений

Дополните объект набора данных с использованием преобразований, описанных в библиотеке [imgaug](https://github.com/aleju/imgaug).

#### <a name="dnn-model-definition"></a>Определение модели DNN

Определите архитектуру модели, используемую на этапе обучения. В пакете машинного обучения Azure для компьютерного зрения поддерживаются шесть предварительно обученных моделей глубоких нейронных сетей: AlexNet, Resnet-18, Resnet 34, Resnet 50, Resnet 101 и Resnet 152.

#### <a name="classifier-training"></a>Обучение классификатора

Обучите нейронные сети со стандартными или настраиваемыми параметрами.

#### <a name="evaluation-and-visualization"></a>Оценка и визуализация

Предоставьте функциональные возможности для оценки эффективности обученной модели на независимом тестовом наборе данных. Метрики оценки включают в себя точность, узнавание, полноту, а также кривую ROC.

Эти подэтапы подробно описаны в соответствующей записной книжке Jupyter. В этой записной книжке также приведены рекомендации по настройке параметров, таких как скорость обучения, размер мини-пакета и скорость отсева, для дальнейшего улучшения эффективности модели.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Развертывание](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment).

На этом этапе вводится в эксплуатацию модель, полученная на этапе моделирования. В этом разделе описаны предварительные требования и необходимые действия по настройке. В нем также рассматривается использование веб-службы. Из этого руководства вы узнаете, как создавать модели глубокого обучения с использованием пакета машинного обучения Azure для компьютерного зрения и вводить эти модели в эксплуатацию в Azure.

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с дополнительной документаций о [пакете машинного обучения Azure для компьютерного зрения](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Чтобы приступить к работе, ознакомьтесь с документацией по [процессу обработки и анализа данных группы](https://aka.ms/tdsp).


## <a name="references"></a>Ссылки

* [Пакет машинного обучения Azure для компьютерного зрения](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation).
* [Виртуальная машина для обработки и анализа данных](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

