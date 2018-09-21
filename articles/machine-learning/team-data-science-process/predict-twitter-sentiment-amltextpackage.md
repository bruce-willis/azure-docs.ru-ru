---
title: Классификация тональности Twitter с помощью пакета машинного обучения Azure (AML) для анализа текста (AMLPTA) и командного процесса обработки и анализа данных (TDSP) | Документация Майкрософт
description: В этой статье описано, как выполнить классификацию тональности с использованием командного процесса обработки и анализа данных (TDSP).
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 9e5018bc4c7b90897f7f8c91169410284217b172
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577015"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Классификация тональности Twitter с помощью пакета машинного обучения Azure (AML) для анализа текста (AMLPTA) и командного процесса обработки и анализа данных (TDSP)

## <a name="introduction"></a>Введение
Стандартизация структуры и документации проектов по обработке и анализу данных, которые привязаны к стабильному [жизненному циклу обработки и анализа данных](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), — это главное условие успешной совместной работы в команде специалистов по обработке и анализу данных.

Ранее мы выпустили [репозиторий GitHub для структуры и шаблонов проектов TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Теперь, наконец, появилась возможность создавать экземпляры проектов службы "Машинное обучение Microsoft Azure" на основе [шаблонов структуры и документации TDSP для службы "Машинное обучение Microsoft Azure"](https://github.com/amlsamples/tdsp). Инструкции по использованию структуры и шаблонов TDSP в службе "Машинное обучение Microsoft Azure" вы найдете [здесь](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

В этом примере демонстрируется использование пакета машинного обучения Azure для анализа текста и TDSP для разработки и развертывания прогнозных моделей с целью классификации тональности Twitter.


## <a name="use-case"></a>Вариант использования 
### <a name="twitter-sentiment-polarity-sample"></a>Пример полярности тональности высказываний в Twitter
В этой статье приведен пример, с помощью которого можно создать и выполнить проект службы "Машинное обучение". В этом примере применяется структура и шаблоны TDSP в Azure Machine Learning Workbench. Полный пример см. в этом пошаговом руководстве. Задача моделирования предсказывает полярность тональности (положительная или отрицательная) на основе текста твитов. В этой статье приведены основные задачи моделирования данных, описанные в этом пошаговом руководстве. В рамках этого руководства рассматриваются следующие задачи:

1. Изучение данных, обучение и развертывание модели машинного обучения для выполнения задачи прогнозирования. Задача описана в обзоре вариантов использования. При выполнении этих задач используются данные тональности.
2. Выполнение проекта с помощью шаблона TDSP из службы "Машинное обучение Azure". При выполнении проекта и создании отчетов используется жизненный цикл TDSP.
3. Ввод решения в эксплуатацию напрямую из службы "Машинное обучение Azure" в Службе контейнеров Azure.

Проект выделяет использование пакета аналитики текста для службы "Машинное обучения Azure".


## <a name="link-to-github-repository"></a>Ссылка на репозиторий GitHub
Ссылка на репозиторий коллекции на GitHub [здесь](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Назначение
В этом пример демонстрируется, как создается и выполняется проект машинного обучения на основе структуры и шаблонов командного процесса обработки и анализа данных (TDSP) в службе "Машинное обучение Azure Workbench". Для этого используются [данные тональности Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Задача моделирования предназначена для предсказывания полярности тональности (положительная или отрицательная) на основе текста твитов.

### <a name="scope"></a>Область
- Изучение данных, обучение и развертывание модели машинного обучения для выполнения задачи прогнозирования. Задача описана в обзоре вариантов использования.
- Выполнение проекта в службе "Машинное обучение Microsoft Azure" на основе предоставляемого службой шаблона для командного процесса обработки и анализа данных (TDSP). Для выполнения проекта и создания отчетов мы будем использовать жизненный цикл TDSP.
- Ввод решения в эксплуатацию напрямую из службы "Машинное обучение Microsoft Azure" в Службе контейнеров Azure.

Проект демонстрирует использование ряда возможностей службы "Машинное обучение Azure", например создание и использование экземпляра структуры TDSP, выполнение кода в службе Azure Machine Learning Workbench, а также простую процедуру ввода в эксплуатацию в Службе контейнеров Azure с использованием Docker и Kubernetes.

## <a name="team-data-science-process-tds"></a>Командный процесс обработки и анализа данных (TDS)
Чтобы выполнить этот пример, используются шаблоны структуры проекта и документации TDSP. Здесь применяется [жизненный цикл TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Этот проект создан на основе инструкций, приведенных [здесь](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Обзор варианта использования
Задача состоит в прогнозировании двоичной полярности тональности текста каждого твита, используя функции вложения слов, извлеченных из текста твита. Подробное описание см. в этом [репозитории](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Получение и анализ данных](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding).
Сначала необходимо загрузить набор данных sentiment140 и разделить его на два набора данных: для обучения и тестирования. Набор данных sentiment140 включает в себя фактическое содержимое твита (без смайликов), а также полярность высказывания каждого твита (положительные твиты имеют значение 0, а отрицательные — 4) (без нейтральных твитов). В полученных данных для обучения содержится 1,3 миллиона строк, а в данных для тестирования — 320 тысяч строк.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Моделирование](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Эта часть образца разделена на три подраздела: 
- **Проектирование признаков** соответствует созданию признаков с использованием разных алгоритмов применения слов, а именно Word2Vec. 
- **Создание модели** касается обучения использования разных моделей (например, _логистическая регрессия_ и _градиентный бустинг_) для прогнозирования тональности текста ввода. 
- **Оценка модели** применяется к обученной модели на основе данных для тестирования.

#### <a name="feature-engineering"></a>Проектирование признаков
В рамках этого примера используется <b>Word2Vec</b> для создания внедрения слов. Сначала мы используем алгоритм Word2Vec в режиме Skipgram, как описано в документе [Работа Томаса Миколова (Tomas Mikolov) и его коллег. Distributed representations of words and phrases and their compositionality (Распределенные представления слов и фраз и их композиционность). Advances in neural information processing systems (Улучшения в нейросетевых системах обработки информации). за 2013 год](https://arxiv.org/abs/1310.4546) для создания внедрения слов.

Skip-gram является неполной нейронной сетью, использующей в качестве входных данных целевое слово, закодированное как прямой унитарный вектор, на основе которого прогнозируются ближайшие слова. Если V — это размер словаря, то размер выходного слоя составляет __C*V__. В этом случае C — размер контекстного окна. На следующем рисунке показана такая архитектура на основе модели Skip-gram.
 
<table class="image" align="center">
<caption align="bottom">Модель Skip-gram</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Сведения об алгоритме Word2Vec и модели Skip-gram не указаны в данном образце. При необходимости перейдите по следующим ссылкам, чтобы получить дополнительные сведения. Код 02_A_Word2Vec.py с указанными [примерами TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py).

* [Векторное представление слов](https://www.tensorflow.org/tutorials/word2vec).
* [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Принцип работы Word2Vec)
* [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Примечания об оценке сопоставления шума и отрицательной выборке)

После завершения обучения на этапе моделирования создается два вложенных TSV-файла.

#### <a name="model-training"></a>Обучение модели
Следующим шагом после создания векторов слов с помощью алгоритма SSWE или Word2vec является обучение модели классификации для предсказывания фактической полярности тональности. Мы применяем два типа функций (Word2Vec и SSWE) в двух моделях (логистическая регрессия и сверточные нейронные сети (CNN)). 

#### <a name="model-evaluation"></a>Оценка модели.
Мы предоставляем код, позволяющий загрузить и определить несколько обученных моделей в наборе данных для тестирования.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Развертывание](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
В этой части предоставлены ссылки на инструкции по вводу в эксплуатацию предварительно обученной модели прогнозирования тональности в веб-службу кластера в службе контейнеров Azure (AKS). Среда ввода в эксплуатацию подготавливает в кластере Docker и Kubernetes для управления развертыванием веб службы. Дополнительные сведения о вводе в эксплуатацию вы найдете [здесь](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Заключение
Вы изучили подробные сведения об обучении модели внедрения слов с помощью Word2Vec, а затем использовали возможность извлеченных внедрений, позволяющую обучать две различные модели для прогнозирования показателя тональности текстовых данных Twitter. Одна из этих моделей развернута в службе контейнеров Azure (AKS). 

## <a name="next-steps"></a>Дополнительная информация
Прочтите дополнительную документацию по началу работы с [пакетом машинного обучения Azure для анализа текста (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) и [командным процессом обработки и анализа данных (TDSP)](https://aka.ms/tdsp).

## <a name="references"></a>Ссылки
* [Что такое процесс обработки и анализа данных группы?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Использование командного процесса обработки и анализа данных (TDSP) в службе "Машинное обучение Microsoft Azure"](https://aka.ms/how-to-use-tdsp-in-aml).
* [Шаблон проекта TDSP для службы "Машинное обучение Microsoft Azure"](https://aka.ms/tdspamlgithubrepo).
* [Машинное обучение Azure ML Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Работа Томаса Миколова (Tomas Mikolov) и его коллег Distributed representations of words and phrases and their compositionality (Распределенные представления слов и фраз и их композиционность). Advances in neural information processing systems (Улучшения в нейросетевых системах обработки информации). за 2013 год](https://arxiv.org/abs/1310.4546)
