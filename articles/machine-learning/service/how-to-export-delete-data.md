---
title: Экспорт или удаление данных рабочей области с помощью Машинного обучения Azure | Документация Майкрософт
description: Используя Машинное обучение Azure, вы можете экспортировать или удалять данные рабочей области с помощью портала Azure, CLI, пакета SDK и аутентифицированных REST API. В этой статье показано, как это сделать.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.openlocfilehash: aef7bae7d2c511766bdbebbf1180e19263d7ccec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986308"
---
# <a name="export-or-delete-your-workspace-data-in-machine-learning"></a>Экспорт или удаление данных рабочей области с помощью Машинного обучения

Используя Машинное обучение Azure, вы можете экспортировать или удалять данные рабочей области с помощью аутентифицированных REST API. В этой статье описано, каким образом это можно сделать.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Управление данными рабочей области
Внутренние данные продукта, хранимые Службами машинного обучения Azure, доступны для экспорта и удаления с помощью портала Azure, CLI, пакета SDK и аутентифицированных REST API. Доступ к данным телеметрии может осуществляться через портал конфиденциальности Azure. 

В Службах машинного обучения Azure персональные данные состоят из информации пользователя, которая находится в документах журнала выполнения, и записей телеметрии о взаимодействии некоторых пользователей со службой.

## <a name="delete-workspace-data-with-the-rest-api"></a>Удаление данных рабочей области с помощью REST API 

Чтобы удалить данные, необходимо выполнить следующие вызовы API с помощью команды HTTP DELETE. Они авторизованы, так как у них в запросе присутствует заголовок `Authorization: Bearer <arm-token>`, где `<arm-token>` является маркером доступа AAD для конечной точки `https://management.core.windows.net/`.  

Сведения о том, как получить этот маркер и вызывать конечные точки Azure, см. в статье [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure/) (Справочник по Azure REST API).  

В приведенных ниже примерах замените текст в {} именами экземпляров, которые определяют сопоставленный ресурс.

### <a name="delete-an-entire-workspace"></a>Удаление всей рабочей области

Используйте следующий вызов, чтобы удалить всю рабочую область.  
> [!WARNING]
> Все данные будут удалены, и рабочую область больше нельзя будет использовать.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Удаление моделей

Используйте этот вызов для получения списка моделей и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Отдельные модели могут быть удалены с помощью:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Удаление ресурсов

Используйте этот вызов для получения списка ресурсов и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Отдельные ресурсы могут быть удалены с помощью:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Удаление изображений

Используйте этот вызов для получения списка изображений и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Отдельные изображения могут быть удалены с помощью:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Удаление служб

Используйте этот вызов для получения списка служб и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Отдельные службы могут быть удалены с помощью:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Экспорт данных службы с помощью REST API

Чтобы экспортировать данные, необходимо выполнить следующие вызовы API с помощью команды HTTP GET. Они авторизованы, так как у них в запросе присутствует заголовок `Authorization: Bearer <arm-token>`, где `<arm-token>` является маркером доступа AAD для конечной точки `https://management.core.windows.net/`.  

Сведения о том, как получить этот маркер и вызывать конечные точки Azure, см. в статье [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure/) (Справочник по Azure REST API).   

В приведенных ниже примерах замените текст в {} именами экземпляров, которые определяют сопоставленный ресурс.

### <a name="export-workspace-information"></a>Экспорт сведений о рабочей области

Используйте этот вызов для получения списка всех рабочих областей.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Сведения об отдельной рабочей области можно получить с помощью вызова:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Экспорт сведений о целевых объектах вычислений

Все целевые объекты вычислений, присоединенные к рабочей области, можно получить с помощью вызова:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Сведения об отдельном целевом объекте вычислений можно получить с помощью вызова:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Экспортер данных журнала выполнения
Используйте этот вызов для получения списка всех экспериментов и соответствующих сведений.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Сведения обо всех запусках определенного эксперимента можно получить с помощью вызова:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Элементы журнала выполнения можно получить, задав:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Все выполнения метрик для эксперимента можно получить с помощью вызова:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Отдельное выполнение метрики можно получить с помощью вызова:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Экспорт артефактов

Используйте этот вызов для получения списка артефактов и их местоположения.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Уведомление об экспорте

Используйте этот вызов для получения списка сохраненных задач.     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Уведомления для одной задачи можно получить с помощью вызова:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Экспорт хранилищ данных

Используйте этот вызов для получения списка хранилищ данных.

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Определенные хранилища данных можно получить с помощью вызова:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Экспорт моделей

Используйте этот вызов для получения списка моделей и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Определенные модели можно получить, задав:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Экспорт ресурсов

Используйте этот вызов для получения списка ресурсов и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Определенные ресурсы можно получить с помощью вызова:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Экспорт изображений

Используйте этот вызов для получения списка изображений и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Определенные изображения можно получить с помощью вызова:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Экспорт служб

Используйте этот вызов для получения списка служб и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Определенные службы можно получить, задав:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Экспорт экспериментов конвейера

Определенные эксперименты можно получить с помощью вызова:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Экспорт диаграмм конвейера

Определенные диаграммы можно получить с помощью вызова:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Экспорт модулей конвейера

Модули можно получить с помощью вызова:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Экспорт шаблонов конвейера

Шаблоны можно получить с помощью вызова:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Экспорт источников данных конвейера

Источники данных можно получить с помощью вызова:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}
