---
title: Запуск заданий по модерации контента с помощью консоли API — Content Moderator
titlesuffix: Azure Cognitive Services
description: Узнайте, как выполнять задания модерации контента в консоли API.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 240b26cd86a6985825e3145c5bc43ef31524d7b7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227116"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Запуск задания модерации из консоли API

Используйте [операции заданий](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API проверки для запуска заданий комплексной модерации контента изображений или текста в Azure Content Moderator. 

Задание модерации проверяет содержимое с помощью API модерации изображений и API модерации текста Content Moderator. Затем задание модерации использует рабочие процессы (определенные в инструменте проверки) для создания проверок в инструменте проверки. 

После того, как модератор-пользователь проверит автоматически назначенные теги и данные прогноза и отправит окончательное решение по модерации, API проверки отправляет все сведения на конечную точку API.

## <a name="prerequisites"></a>Предварительные требования

Перейдите к [инструменту проверки](https://contentmoderator.cognitive.microsoft.com/). Зарегистрируйтесь, если вы этого не сделали. В инструменте проверки [определите пользовательский рабочий процесс](Review-Tool-User-Guide/Workflows.md) для использования в этой операции `Job`.

## <a name="use-the-api-console"></a>Использование консоли API
Для тестирования API с помощью веб-консоли необходимо ввести в нее несколько значений.
    
- `teamName`: используйте поле `Id` (Идентификатор), отображенное на экране учетных данных инструмента проверки. 
- `ContentId`: эта строка передается в API и возвращается с помощью обратного вызова. **ContentId** удобно использовать для связывания внутренних идентификаторов или метаданных с результатами задания модерации. `Workflowname`: имя [рабочего процесса, который вы создали](Review-Tool-User-Guide/Workflows.md) в предыдущем разделе.
- `Ocp-Apim-Subscription-Key`: отображается на вкладке **Settings** (Параметры). Дополнительные сведения см. в этом [обзоре](overview.md).

Перейдите к консоли API из окна **Credentials** (Учетные данные).

### <a name="navigate-to-the-api-reference"></a>Переход к справочнику по API
В окне **Credentials** (Учетные данные) выберите [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) (Справочник по API).

  Откроется страница `Job.Create`.

### <a name="select-your-region"></a>Выбор региона
Для параметра **Open API testing console** (Открытая тестовая консоль API) выберите регион, лучше всего подходящий для вашего расположения.
  ![Выбор региона на странице "Job - Create" (Задание — создание)](images/test-drive-job-1.png)

  Откроется консоль API `Job.Create`. 

### <a name="enter-parameters"></a>Ввод параметров

Введите значения обязательных параметров запроса и ключ подписки. В поле **Request body** (Текст запроса) укажите расположение данных, которые требуется проверить. Например, используем этот [пример изображения](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Параметры запроса, заголовки и поле "Request body" (Текст запроса) в консоли "Job - Create" (Задание — создание)](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Отправка запроса
Нажмите кнопку **Отправить**. Будет создан идентификатор задания. Скопируйте его, чтобы использовать на следующих шагах.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Открытие страницы "Get Job details" (Получение сведений о задании)
Выберите **Get** (Получить), а затем откройте API, нажав кнопку, которая соответствует вашему региону.

  ![Получение результатов в консоли "Job - Create" (Задание — создание)](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Просмотр ответа

Введите значения параметров **teamName** и **JobID**. Введите ключ подписки, а затем щелкните **Send** (Отправить). Ниже приведен пример ответа, содержащий состояние задания и сведения о нем.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>Переход к инструменту проверки
На панели мониторинга Content Moderator выберите **Review** (Проверка)  >  **Image** (Изображение). Отобразится проверенное изображение, подготовленное к пользовательской проверке.

  ![Изображение трех велосипедистов в инструменте проверки](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Дополнительная информация

Используйте в коде REST API или начните с [краткого руководства по работе с заданиями с помощью .NET](moderation-jobs-quickstart-dotnet.md), чтобы интегрировать эту функцию в приложение.
