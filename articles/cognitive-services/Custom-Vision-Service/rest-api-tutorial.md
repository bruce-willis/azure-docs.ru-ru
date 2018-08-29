---
title: Использование REST API Пользовательской службы визуального распознавания — Azure Cognitive Services | Документация Майкрософт
description: Использование REST API для создания, обучения, тестирования и экспорта пользовательской модели компьютерного зрения.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250252"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Руководство по использованию REST API Пользовательской службы визуального распознавания

Здесь показано, как создавать, обучать, тестировать и экспортировать пользовательскую модель компьютерного зрения с использованием REST API Пользовательской службы визуального распознавания.

В этом документе показано, как использовать клиент REST, чтобы обучить Пользовательскую службу визуального распознавания с помощью REST API. В примерах показано, как использовать API с помощью служебной программы `curl` из среды bash и `Invoke-WebRequest` из Windows PowerShell.

> [!div class="checklist"]
> * Получение ключей.
> * Создание проекта
> * Создание тегов
> * Добавление изображений.
> * Обучение и тестирование модели.
> * Экспорт модели.

## <a name="prerequisites"></a>Предварительные требования

* Знакомство со службой передачи репрезентативного состояния (REST). В этом документе не рассматриваются детали, такие как HTTP-команды, JSON или другие вещи, обычно используемые с REST.

* Вash (Bourne Again Shell) со служебной программой [curl](https://curl.haxx.se) или Windows PowerShell 3.0 (или выше).

* Учетная запись Пользовательской службы визуального распознавания. Дополнительные сведения см. в документе [о создании классификатора](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Получение ключей

При использовании REST API нужно проходить проверку подлинности с помощью ключа. Для выполнения операций управления или обучения используйте __ключ обучения__. В случае применения модели для прогнозирования используйте __ключ прогнозирования__.

При выполнении запроса ключ отправляется как заголовок запроса.

Чтобы получить ключи для своей учетной записи, посетите [веб-страницу Пользовательской службы визуального распознавания](https://customvision.ai) и выберите __значок шестеренки__ в правом верхнем углу. В разделе __Accounts__ (Учетные записи) скопируйте значения в полях __Training Key__ (Ключ обучения) и __Prediction Key__ (Ключ прогнозирования).

![Изображение пользовательского интерфейса с ключами](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Так как ключи используются для аутентификации каждого запроса, примеры в этом документе предполагают, что значения ключей содержатся в переменных среды. Выполните следующие команды, чтобы сохранить ключи в переменных среды, прежде чем использовать любые другие фрагменты в этом документе:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Создание нового проекта

Следующие примеры создают новый проект с именем `myproject` в экземпляре Пользовательской службы визуального распознавания. Эта служба по умолчанию использует домен `General`:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> В поле `id` в ответе содержится идентификатор нового проекта. Он используется в других примерах далее в этом документе.

Дополнительные сведения об этом запросе см. в разделе [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Отдельные домены

Чтобы создать проект для отдельного домена, можно указать __идентификатор домена__ в качестве необязательного параметра. В следующих примерах показано, как получить список доступных доменов:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Дополнительные сведения об этом запросе см. в разделе [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

Следующий пример демонстрирует создание нового проекта, который использует домен __Landmarks__:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Создание тегов

Чтобы отметить тегом изображения, нужно использовать идентификатор тега. В следующем примере показано, как создать новый тег с именем `cat` и получить идентификатор тега. Замените `{projectId}` идентификатором проекта. Используйте параметр `name=`, чтобы указать имя тега:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON. 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Сохраните значение `id`, так как оно используется при добавлении тегов к изображениям.

Дополнительные сведения об этом запросе см. в разделе [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Добавление изображений

Следующие примеры демонстрируют добавление файла, размещенного по URL-адресу. Замените `{projectId}` идентификатором проекта. Замените `{tagId}` идентификатором тега изображения:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Дополнительные сведения об этом запросе см. в разделе [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Обучение модели

В следующих примерах показано, как обучать модель. Замените `{projectId}` идентификатором проекта.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Сохраните значение `id`, так как оно используется для тестирования и экспорта модели.

Дополнительные сведения см. в разделе [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Тестирование модели

В следующих примерах показано, как тестировать модель. Замените `{projectId}` идентификатором проекта. Замените `{iterationId}` идентификатором, полученным после обучения модели. Замените `https://linktotestimage` путем к тестовому изображению.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Дополнительные сведения см. в разделе [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Экспорт модели

Экспорт модели — это двухэтапный процесс. Сначала вы должны указать формат модели, а затем запросить URL-адрес экспортируемой модели.

### <a name="request-a-model-export"></a>Запрос на экспорт модели

В следующих примерах показано, как экспортировать модель `coreml`. Замените `{projectId}` идентификатором проекта. Замените `{iterationId}` идентификатором, полученным после обучения модели.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Дополнительные сведения см. в разделе [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Скачивание экспортируемой модели

В следующих примерах показано, как получить URL-адрес экспортируемой модели. Замените `{projectId}` идентификатором проекта. Замените `{iterationId}` идентификатором, полученным после обучения модели.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Ответ на запрос аналогичен приведенному ниже документу JSON.

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Дополнительные сведения см. в разделе [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).