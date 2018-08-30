---
title: Использование пакета SDK распознавания речи для C# с LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: Использование примера пакета SDK распознавания речи для C# для проговаривания фраз в микрофон и получения намерения и предсказания сущностей LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: diberry
ms.openlocfilehash: aadca428fa076d697cc0f893673672850ddc27d4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124402"
---
# <a name="integrate-speech-service"></a>Интеграция службы распознавания речи
[Служба распознавания речи](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) позволяет использовать один запрос для получения аудио и возврата объектов JSON с предсказаниями LUIS.

В этой статье описано, как загрузить и использовать проект C# в Visual Studio, чтобы произнести фразу в микрофон и получить информацию о предсказании LUIS. В этом проекте используется уже включенный в качестве ссылки пакет [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) для распознавания речи. 

Для работы с этой статьей требуется бесплатная учетная запись для веб-сайта [LUIS][LUIS], в которую вы импортируете приложение.

## <a name="create-luis-endpoint-key"></a>Создание ключа конечной точки LUIS
На портале Azure [создайте](luis-how-to-azure-subscription.md#create-luis-endpoint-key) ключ **службы "Распознавание речи"** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Импорт приложения LUIS Human Resources
Намерения и фразы для этой статьи взяты из приложения LUIS Human Resources, доступного в репозитории GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Загрузите файл [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json), сохраните его с расширением JSON и [импортируйте](luis-how-to-start-new-app.md#import-new-app) в LUIS. 

Это приложение имеет намерения, сущности и фразы, относящиеся к кадровым ресурсам. Ниже приведены примеры фраз.

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Добавление предварительно созданной сущности KeyPhrase
После импорта приложения выберите **Entities** (Сущности), а затем **Manage prebuilt entities** (Управление предварительно созданными сущностями). Добавьте сущность **KeyPhrase**. Сущность KeyPhrase извлекает запрашиваемое содержимое из фразы.

## <a name="train-and-publish-the-app"></a>Обучение и публикация приложения
1. На правой верхней панели навигации нажмите кнопку **Train** (Обучить) для обучения приложения LUIS.

2. Чтобы перейти к странице публикации, щелкните **Publish** (Опубликовать). 

3. В нижней части страницы **публикации** добавьте ключ LUIS, созданный в разделе о [создании конечной точки LUIS](#create-luis-endpoint-key).

4. Опубликуйте приложение LUIS, нажав кнопку **Publish** (Опубликовать) справа от слота публикации. 

  На странице **публикации** скопируйте идентификатор приложения, регион публикации и идентификатор подписки ключа LUIS, созданного в разделе о [создании конечной точки LUIS](#create-luis-endpoint-key). Необходимо изменить код, чтобы использовать эти значения далее в этой статье. 

  Эти значения включены в URL-адрес конечной точки в нижней части страницы **публикации** для созданного ключа. 
  
  Для этого упражнения **не используйте** бесплатный начальный ключ. Здесь подойдет ключ **Распознавание речи**, созданный на портале Azure. 

  https://**Регион**.api.cognitive.microsoft.com/luis/v2.0/apps/**ИД_приложения**?subscription-key=**Ключ_LUIS**&q=

## <a name="audio-device"></a>Звуковое устройство
В этой статье используется звуковое устройство на вашем компьютере. Это может быть гарнитура с микрофоном или встроенное звуковое устройство. Чтобы ваша речь обнаруживалась на звуковом устройстве, проверьте уровни входного аудиосигнала и определите, следует ли говорить громче, чем обычно. 

## <a name="download-the-luis-sample-project"></a>Загрузка примера проекта LUIS
 Клонируйте или загрузите репозиторий [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). Откройте [проект преобразование речи в намерения](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) в Visual Studio и восстановите пакеты NuGet. Файл решения VS имеет путь .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Пакет SDK для распознавания речи уже включен в качестве ссылки. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Снимок экрана Visual Studio 2017 с пакетом NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Изменение кода C#
Откройте файл **LUIS_samples.cs** и измените следующие переменные:

|Имя переменной|Назначение|
|--|--|
|luisSubscriptionKey|Соответствует значению ключа подписки в URL-адресе конечной точки со страницы публикации|
|luisRegion|Соответствует первому поддомену URL-адреса конечной точки|
|luisAppId|Соответствует URL-адресу конечной точки после **apps/**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Снимок экрана Visual Studio 2017 с отображением переменных LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

В файле уже сопоставлены намерения Human Resources.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Снимок экрана Visual Studio 2017 с отображением намерений LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Выполните сборку и запустите приложение. 

## <a name="test-code-with-utterance"></a>Проверка кода с фразой
Выберите **1** и скажите в микрофон: "Who is the manager of John Smith".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

Правильное намерение **GetEmployeeOrgChart** обнаружено с показателем точности 61 %. Сущность keyPhrase была возвращена. 

Пакет SDK для распознавания речи возвращает полный ответ LUIS. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS HumanResources, если оно больше не нужно. Для этого нажмите кнопку многоточия (***...***) справа от имени приложения в списке и выберите **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

Не забудьте удалить каталог LUIS-Samples, когда вы закончите использовать пример кода.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Интеграция LUIS с бот](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website