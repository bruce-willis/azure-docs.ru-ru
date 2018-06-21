---
title: Экспорт или удаление данных управления моделью или экспериментированием. Машинное обучение Azure | Документация Майкрософт
description: Используя машинное обучение Azure, вы можете экспортировать или удалять данные учетной записи, которые связаны с управлением моделью или экспериментированием. Это можно сделать с помощью портала Azure, CLI, пакета SDK и аутентификации REST API. В этой статье показано, как это сделать.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 7db37865c99908e0fd44be3ec04a8493d190e941
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833518"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Экспорт или удаления данных управления моделью или экспериментирования в машинном обучение

Используя машинное обучение Azure, вы можете экспортировать или удалять данные учетной записи, которые связаны с управлением моделью или экспериментированием. Это можно сделать с помощью аутентификации REST API. В этой статье описано, каким образом это можно сделать.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Управление данными учетной записи
Данные о продукте, которые хранятся в экспериментах и моделях управления машинного обучение Azure, доступны для экспорта и удаления через портал Azure, CLI, пакет SDK и аутентификацию REST API. Доступ к данным телеметрии может осуществляться через портал конфиденциальности Azure. 

В машинном обучении Azure персональные данные состоят из информации пользователя, которая находится в документах журнала выполнения, и записей телеметрии о взаимодействии некоторых пользователей со службой.

## <a name="delete-account-data-with-the-rest-api"></a>Удаление данных учетной записи с помощью REST API 

Чтобы удалить данные, необходимо выполнить следующие вызовы API с помощью команды HTTP DELETE. Они авторизованы, так как у них в запросе присутствует заголовок `Authorization: Bearer <arm-token>`, где `<arm-token>` является маркером доступа AAD для конечной точки `https://management.core.windows.net/`.  

Сведения о том, как получить этот маркер и вызывать конечные точки Azure, см. в статье [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure/) (Справочник по Azure REST API).  

В приведенных ниже примерах замените текст в {} именами экземпляров, которые определяют сопоставленный ресурс.

## <a name="delete-from-a-hosting-account"></a>Удаление из учетной записи размещения

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Удаление из службы управления модели

### <a name="model-document"></a>Документ модели
Используйте этот вызов для получения списка моделей и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Отдельные модели могут быть удалены с помощью:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Документ манифеста
Используйте этот вызов для получения списка всех манифестов и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Отдельные манифесты могут быть удалены с помощью:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Документы службы
Используйте этот вызов для получения списка всех служб и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Отдельные службы могут быть удалены с помощью:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Документ изображения
Используйте этот вызов для получения списка всех изображений и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Отдельные изображения могут быть удалены с помощью:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Удаление журнала выполнения, артефакта и данных уведомления
В журнале выполнения, артефакте и уведомлении хранится вся соответствующая проектная документация, которая была удалена.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Удаление данных из учетной записи Экспериментирования поставщика ресурсов
Документы проекта удаляются при помощи:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Документы рабочей области удаляются при помощи:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

Учетная запись Экспериментирования удаляется с помощью:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Экспорт данных службы с помощью REST API
Чтобы экспортировать данные, необходимо выполнить следующие вызовы API с помощью команды HTTP GET. Они авторизованы, так как у них в запросе присутствует заголовок `Authorization: Bearer <arm-token>`, где `<arm-token>` является маркером доступа AAD для конечной точки `https://management.core.windows.net/`.  

Сведения о том, как получить этот маркер и вызывать конечные точки Azure, см. в статье [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure/) (Справочник по Azure REST API).   

В приведенных ниже примерах замените текст в {} именами экземпляров, которые определяют сопоставленный ресурс.

## <a name="export-hosting-account-data"></a>Экспорт данных учетной записи размещения

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Экспорт данных службы "Управление моделями".
### <a name="model-document"></a>Документ модели

Используйте этот вызов для получения списка моделей и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Определенные модели можно получить, задав:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Манифесты
Используйте этот вызов для получения списка всех манифестов и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Определенные манифесты можно получить, задав:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Службы
Используйте этот вызов для получения списка всех служб и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Определенные службы можно получить, задав: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Образы
Используйте этот вызов для получения списка всех изображений и их идентификаторов.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Определенные службы можно получить, задав: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Экспорт данных вычислений
### <a name="compute-clusters"></a>Вычислительные кластеры
Используйте этот вызов для получения списка всех вычислительных кластеров и их имен.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Определенные кластеры можно получить, задав:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Использование кластеров
Используйте этот вызов для получения списка всех кластеров и их имен.

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Определенные кластеры можно получить, задав:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Экспортер данных журнала выполнения
Используйте этот вызов для получения списка всех запусков и их идентификаторов.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Используйте этот вызов для получения списка экспериментов и их идентификаторов.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Элементы журнала выполнения можно получить, задав:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Элементы выполнения метрик можно получить, задав:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Результаты выполнения экспериментов можно получить, задав:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Журнал выполнения артефактов.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Журнал выполнения URI артефактов.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Экспорт артефактов
Используйте этот вызов для получения списка активов и их имен.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Используйте этот вызов для получения списка артефактов и их местоположения.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Содержимое артефакта

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Документы артефакта

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Активы

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Уведомление об экспорте

1. Перейдите к [разделу "Пользователи" на портале Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/) и выберите пользователя из столбца **Имя**. 
2. Обратите внимание на **идентификатор объекта**, который необходимо использовать при следующем вызове.     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Экспорт информации учетной записи Экспериментирования
### <a name="experimentation-account-information"></a>Учетная запись Экспериментирования

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Сведения о рабочей области

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Сведения о проекте

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
