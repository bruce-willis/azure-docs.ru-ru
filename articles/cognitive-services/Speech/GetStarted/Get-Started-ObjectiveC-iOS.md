---
title: Начало работы с API распознавания речи Майкрософт в Objective-C на iOS | Документация Майкрософт
description: Используйте API распознавания речи Майкрософт для разработки приложений iOS, которые преобразовывают устную речь в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380584"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>Приступите к работе с API распознавания речи в Objective-C на iOS

С помощью API распознавания речи можно разрабатывать приложения iOS, которые используют облачную службу "Речь" для преобразования устной речи в текст. API-интерфейс поддерживает передачи в реальном времени, поэтому приложение может одновременно и асинхронно получать результаты частичного распознавания, одновременно отправляя устную речь в службу.

В этой статье используется образец приложения, чтобы продемонстрировать основные принципы того, как приступить к работе с API распознавания речи для разработки приложений iOS. Полный справочник по API см. на странице [SpeechSDK-1_0-for-iOS Reference](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html) (Справка по SpeechSDK-1_0-for-iOS).

## <a name="prerequisites"></a>предварительным требованиям

### <a name="platform-requirements"></a>Требования платформы

Убедитесь, что Mac XCode IDE установлен.

### <a name="get-the-client-library-and-examples"></a>Получение клиентской библиотеки и примеров

Клиентская библиотека службы "Речь" и образцы для iOS доступны в [клиентском пакете SDK службы "Речь" для iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Подпишитесь на API распознавания речи и получите ключ бесплатной пробной версии подписки

Speech API — элемент Cognitive Services (ранее Project Oxford). Вы можете получить ключи бесплатной пробной версии подписки на странице [подписки на Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Чтобы получить ключ, после выбора SAPI выберите **Получить API-ключ**. Страница возвращает первичный и вторичный ключ. Оба ключа привязаны к одной и той же квоте, поэтому вы можете использовать любой из них.

Если вы хотите использовать *распознавание блокировки с намерением*, необходимо зарегистрироваться в службе [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Получить ключ подписки. Прежде чем использовать клиентские библиотеки службы "Речь", вам необходимо иметь [ключ подписки](https://azure.microsoft.com/try/cognitive-services/).
>
> * Используйте свой ключ подписки В предоставленном образце приложения iOS необходимо обновить файл Samples/SpeechRecognitionServerExample/settings.plist вашим ключом подписки. Дополнительные сведения см. в разделе [Образцы сборки и выполнения](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Используйте клиентскую библиотеку речи

Чтобы добавить клиентскую библиотеку в проект XCode, выполните следующие [инструкции](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Справочник по клиентской библиотеке для iOS см. на этой [веб-странице](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Сборка и выполнение примеров

Дополнительные сведения о сборке и выполнении примеров см. на странице [Файл сведений](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Описание образцов

### <a name="create-recognition-clients"></a>Создание клиентов для распознавания

В следующем примере кода показано создание клиентских классов распознавания на основе сценариев пользователя.

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Клиентская библиотека предоставляет предварительно реализованные клиентские классы распознавания для типичных сценариев в распознавания речи.

* `DataRecognitionClient`: распознавание речи с использованием данных PCM (например, из файла или из звукового источника файла). Данные помещаются в буферы, и каждый буфер отправляется в службу распознавания речи. В буферах не производится никакие изменения, поэтому пользователи могут применить собственные обнаружения тишины, при необходимости. Если данные предоставляются из WAV-файлов, можно отправлять данные из файла прямо на сервер. Если имеются необработанные данные, например, устная речь, поступающая через Bluetooth, сначала отправьте заголовок формата на сервер, а затем данные.
* `MicrophoneRecognitionClient`: распознавание речи, поступающей с микрофона. Убедитесь, что микрофон включен и эти данные отправляются в службу распознавания речи. Встроенный детектор тишины применяется к данным микрофона перед их отправкой в ​​службу распознавания.
* `DataRecognitionClientWithIntent` и `MicrophoneRecognitionClientWithIntent`: помимо распознавания текста эти клиенты возвращают структурированную информацию о намерениях говорящего, которую приложения могут использовать для дальнейшей действий. Чтобы использовать "Намерение", необходимо сначала обучить модель с помощью [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Распознавание языка

При использовании `SpeechRecognitionServiceFactory` для создания клиента необходимо выбрать язык. Полный список языков, поддерживаемых службой распознавания речи, см. в статье [Supported languages](../API-Reference-REST/supportedlanguages.md) (Поддерживаемые языки).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Необходимо указать `SpeechRecognitionMode` при создании клиента с помощью `SpeechRecognitionServiceFactory`.

* `SpeechRecognitionMode_ShortPhrase`: фраза продолжительностью до 15 секунд. Когда данные отправляются в службу, клиент получает несколько промежуточных результатов и один конечный результат с несколькими лучшими вариантами.
* `SpeechRecognitionMode_LongDictation`: фраза продолжительностью до 2 минут. После отправки данных в службу клиент получит несколько промежуточных и несколько конечных результатов в зависимости от того, как сервер распознает паузы предложения.

### <a name="attach-event-handlers"></a>Вложение обработчиков событий

Можно вложить различные обработчики событий в созданный клиент.

* **Событие частичных результатов**. Это событие вызывается каждый раз, когда служба "Речь" предсказывает, что вы можете сказать, даже до того, как вы закончите разговор (если вы используете `MicrophoneRecognitionClient`) или закончите отправку данных (если вы используете `DataRecognitionClient`).
* **События ошибок**. Вызываются, когда служба обнаруживает ошибку.
* **События с намерением**. Вызывает на клиенты "WithIntent" (только в режиме ShortPhrase) после синтаксического анализа распознавания окончательного результата в структурированном намерении JSON.
* **События результатов**.
  * В режиме `SpeechRecognitionMode_ShortPhrase` это событие вызывается и возвращает самые лучшие результаты после окончания речи.
  * В режиме `SpeechRecognitionMode_LongDictation` обработчик событий может вызываться несколько раз в зависимости от того, где служба распознает паузы в предложении.
  * **Для каждого из самых лучших вариантов** возвращаются значение достоверности и несколько различных форм распознанного текста. Для получения дополнительных сведений см. раздел [Формат выходных данных](../Concepts.md#output-format).

## <a name="related-topics"></a>Связанные разделы

* [SpeechSDK-1_0-for-iOS Reference](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html) (Справка по SpeechSDK-1_0-for-iOS)
* [Начало работы с API распознавания речи Microsoft в Java на Android](GetStartedJavaAndroid.md)
* [Приступите к работе с API распознавания речи Microsoft в JavaScript](GetStartedJSWebsockets.md)
* [Начало работы с API распознавания речи Microsoft с использованием REST](GetStartedREST.md)
