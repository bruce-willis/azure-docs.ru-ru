---
title: Примеры и пошаговые руководства для виртуальных машин для обработки и анализа данных в Azure | Документация Майкрософт
description: Примеры и пошаговые руководства для виртуальных машин для обработки и анализа данных
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392574"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Примеры для виртуальных машин для обработки и анализа данных

Виртуальные машины для обработки и анализа данных (DSVM) включают полноценный набор примеров кодов в виде записных книжек Jupyter, а также сценарии на различных языках, например Python и R.    
> [!NOTE]
> Дополнительные сведения о выполнении записных книжек Jupyter на виртуальной машине для обработки и анализа данных см. в разделе [Доступ к Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Краткий справочник примеров
| Категории примеров | ОПИСАНИЕ | Расположения |
| ------------- | ------------- | ------------- |
| Язык **R**  | Примеры на **R**, которые рассматривают такие сценарии, как подключение к облачным хранилищам данных Azure, сравнение моделей эксплуатации R и Microsoft R с открытым исходным кодом в Microsoft R Server и SQL Server. <br/> [Снимок экрана](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Язык **Python**  | Примеры на **Python**, которые рассматривают такие сценарии, как подключение к облачным хранилищам данных Azure и работу с **Машинным обучением Azure**.  <br/> [Снимок экрана](#python-language) | <br/>`~notebooks` <br/><br/>|
| Язык **Julia**  | Пример на **Julia**, описывающий построение графиков в Julia, глубокое обучение на Julia, вызов C и Python из Julia и т. д. <br/> [Снимок экрана](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Машинное обучение Azure** AzureML  | Создавайте модели машинного и глубокого обучения с помощью службы **Машинное обучение Azure** и развертывайте их в любое расположение. Воспользуйтесь преимуществами таких возможностей, как автоматическое машинное обучение, интеллектуальная оптимизация гиперпараметров, управление моделями и распределенное обучение. <br/> [Снимок экрана](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Записные книжки **PyTorch**  | Примеры глубокого обучения, использующие нейронные сети на основе **PyTorch**. Есть множество записных книжек: от базовых до расширенных сценариев.  <br/> [Снимок экрана](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Несколько различных примеров нейронной сети и методов, реализованных с помощью платформы **TensorFlow**. <br/> [Снимок экрана](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Примеры глубокого обучения, опубликованные командой Cognitive Toolkit в Майкрософт.  <br/> [Снимок экрана](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **Caffe2** | Примеры глубокого обучения, использующие нейронные сети на основе **Caffe2**. Есть несколько записных книжек, предназначенных для ознакомления пользователей с caffe2, и способов его эффективного использования, включая примеры, такие как предварительная обработка изображений, создание набора данных, регрессия и использование предварительно подготовленных моделей. <br/> [Снимок экрана](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Примеры на основе Python, использующие **H2O** для решения проблем во множестве реальных сценариев. <br/> [Снимок экрана](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Язык **SparkML**  | Пример использования функций и возможностей набора средств **MLlib** Spark через **pySpark** и приложение **Библиотека машинного обучения Microsoft для Apache Spark (MMLSpark)** в **Apache Spark 2.x**.  <br/> [Снимок экрана](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Примеры стандартного машинного обучения в **XGBoost** для сценариев классификации, регрессии и т. д. <br/> [Снимок экрана](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Доступ к Jupyter 

Доступ к Jupyter можно получить, щелкнув значок `Jupyter` на рабочем столе или в меню приложения. Jupyter можно также использовать на выпусках DSVM для Linux удаленно из браузера на сайте **`https://<Full Domain Name or IP Address of the DSVM>:8000`** в Ubuntu.

Ознакомьтесь со снимком экрана для добавления исключения и включения доступа Jupyter через браузер.


![Включение исключений Jupyter](./media/ubuntu-jupyter-exception.png)


Войдите, используя один и тот же пароль, что и для входа в DSVM.
<br/>

**Домашняя страница Jupyter**
<br/>![Домашняя страница Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Язык R 
<br/>![Примеры для R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Язык Python
<br/>![Примеры для Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Язык Julia 
<br/>![Примеры для Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Примеры AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Примеры PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Примеры TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Примеры CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Примеры Caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Примеры H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Примеры SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost; 
<br/>![Примеры XGBoost](./media/xgboost-samples.png)<br/>

