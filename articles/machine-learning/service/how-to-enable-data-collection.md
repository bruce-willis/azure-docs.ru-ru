---
title: Включение сбора данных для моделей в рабочей среде — машинное обучение Azure
description: Сведения о сборе входных данных модели машинного обучения Azure в хранилище BLOB-объектов Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 412871c0c692f60e690f61fa4e6f67f836cd3ef7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158213"
---
# <a name="collect-data-for-models-in-production"></a>Сбор данных для моделей в рабочей среде

Из этой статьи вы узнаете, как собирать входные данные модели от служб машинного обучения Azure, которые были развернуты в кластере Azure Kubernetes в хранилище BLOB-объектов Azure. 

После включения сбора данных собранные данные помогут вам:
* Отслеживать колебания данных при поступлении рабочих данных в вашу модель

* Принимать более взвешенные решения о необходимости повторного обучения и оптимизации модели

* Повторно обучить модель с использованием собранных данных

## <a name="what-is-collected-and-where-does-it-go"></a>Какие данные собираются и куда они попадают?

Вы можете собирать следующие данные:
* **Входные** данные модели от веб-служб, развернутых в кластере Azure Kubernetes (AKS) (голосовые данные, видео и изображения **не** собираются) 
  
* Прогнозирование моделей с использованием входных данных рабочей среды.

> [!Note]
> Предварительная статистическая обработка и предварительные вычислительные операции с этими данными сейчас не выполняются в рамках службы.   

Выходные данные сохраняются в BLOB-объекте Azure. Так как данные добавляются в BLOB-объект Azure, вы можете выбрать привычные инструменты для проведения анализа. 

Для пути к выходным данным в BLOB-объекте используется следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

- Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со сценариями и пакет SDK машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).

- Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS). Если у вас ее нет, обратитесь к [руководству по обучению модели классификации изображений](tutorial-train-models-with-aml.md).

- [Кластер AKS](how-to-deploy-to-aks.md).

- [В вашей среде](how-to-configure-environment.md) должны быть установлены следующие зависимости и модули:
  + В Linux
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Действия для ОС Windows.
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Включение сбора данных
Сбор данных можно включить независимо от модели, развертываемой с помощью службы машинного обучения Azure или других средств. 

Чтобы включить сбор данных, выполните следующие действия:

1. Откройте файл оценки. 

1. Добавьте следующий код в начало файла.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Объявите переменные коллекции данных в функции `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    Параметр *CorrelationId* является необязательным, и если он не требуется для модели, указывать его не нужно. Указание идентификатора корреляции упрощает сопоставление с другими данными. (К ним относятся LoanNumber, CustomerId и т. д.)
    
    *Идентификатор* используется в дальнейшем для создания структуры папок BLOB-объекта. Его можно использовать, чтобы отделить "необработанные" данные от "обработанных".

3.  Добавьте в функцию `run(input_df)` следующие строки кода.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Сбор данных **не** **включается** автоматически при развертывании службы в AKS, поэтому вам необходимо обновить файл конфигурации, например: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Также можно включить AppInsights для мониторинга служб, изменив эту конфигурацию:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Создание образа и развертывание службы.](how-to-deploy-to-aks.md) 


Если в **файле среды** и **файле оценки** уже указана служба с установленными зависимостями, вы можете включить сбор данных следующим образом:

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Перейдите в рабочую область.

1. Выберите **Развертывания** -> **Выбор службы** -> **Изменить**.

   ![Изменение службы](media/how-to-enable-data-collection/EditService.png)

1. В разделе **Дополнительные параметры** отмените выбор параметра **Включить сбор данных модели**. 

   ![Снятие флажка сбора данных](media/how-to-enable-data-collection/CheckDataCollection.png)

   В этом окне вы также можете установить параметр "Включить диагностику AppInsights", чтобы отслеживать работоспособность своей службы.  

1. Чтобы применить изменение, нажмите кнопку **Обновить**.


## <a name="disable-data-collection"></a>Отключение сбора данных
Вы можете остановить сбор данных в любое время. Отключение сбора данных с помощью кода Python или портала Azure.

+ Вариант 1 — отключение сбора данных на портале Azure: 
  1. Войдите на [портал Azure](https://portal.azure.com).

  1. Перейдите в рабочую область.

  1. Выберите **Развертывания** -> **Выбор службы** -> **Изменить**.

     ![Изменение службы](media/how-to-enable-data-collection/EditService.png)

  1. В разделе **Дополнительные параметры** отмените выбор параметра **Включить сбор данных модели**. 

     ![Снятие флажка сбора данных](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Чтобы применить изменение, нажмите кнопку **Обновить**.

* Вариант 2 — отключение сбора данных с помощью Python:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Пример записной книжки

Записная книжка `00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` иллюстрирует основные понятия, приведенные в этой статье.  

Получите эту записную книжку:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]