---
title: Приступите к работе с API распознавания речи Microsoft в Java на Android | Документация Майкрософт
titlesuffix: Azure Cognitive Services
description: Используйте API распознавания речи Microsoft для разработки приложений Android, которые преобразовывают устную речь в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 3b1e82d65a3eafb9e41277a18569270ef02cd8e5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989046"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Краткое руководство. Использование API распознавания речи Bing в Java на платформе Android

С помощью API распознавания речи Bing можно разрабатывать приложения Android, которые используют облачную службу "Распознавание речи Bing" для преобразования устной речи в текст. API-интерфейс поддерживает передачи в реальном времени, поэтому приложение может одновременно и асинхронно получать результаты частичного распознавания, одновременно отправляя устную речь в службу.

В этой статье используется образец приложения, чтобы продемонстрировать использование клиентской библиотеки службы "Речь" для Android, чтобы разработать приложения преобразования устной речи в текст на языке Java для устройств Android.

## <a name="prerequisites"></a>Предварительные требования

### <a name="platform-requirements"></a>Требования платформы

Образец разработан на платформе [Android Studio](http://developer.android.com/sdk/index.html) для Windows на языке Java.

### <a name="get-the-client-library-and-sample-application"></a>Получить клиентскую библиотеку и образец приложения

Клиентская библиотека службы "Речь" и образцы для Android доступны в [клиентском пакете SDK службы "Речь" для Android](https://github.com/microsoft/cognitive-speech-stt-android). Можно найти образец для построения в каталоге samples/SpeechRecoExample. Имеются две библиотеки, которые необходимо использовать в приложениях, в каталоге SpeechSDK/libs в разделе armeabi и папке x86. Размер файла libandroid_platform.so — 22 МБ, но он сокращается до 4 МБ во время развертывания.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Подписка на API распознавания речи и получение ключа бесплатной пробной версии подписки

Speech API — элемент Cognitive Services (ранее Project Oxford). Вы можете получить ключи бесплатной пробной версии подписки на странице [подписки на Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Чтобы получить ключ, после выбора SAPI выберите **Получить API-ключ**. Страница возвращает первичный и вторичный ключ. Оба ключа привязаны к одной и той же квоте, поэтому вы можете использовать любой из них.

Чтобы использовать *распознавание блокировки с намерением*, необходимо зарегистрироваться для использования [Интеллектуальной службы распознавания речи (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Получить ключ подписки. Прежде чем использовать клиентские библиотеки службы "Речь", вам необходимо иметь [ключ подписки](https://azure.microsoft.com/try/cognitive-services/).
>
>* Используйте свой ключ подписки С помощью предоставленного образца приложения Android и ключей вашей подписки обновите файл samples/SpeechRecoExample/res/values/strings.xml. Дополнительные сведения см. в разделе [Образцы сборки и выполнения](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Используйте клиентскую библиотеку речи

Чтобы использовать клиентскую библиотеку в приложении, выполните [инструкции](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Справочник клиентской библиотеки для Android можно найти в папке документов [клиентского пакета SDK службы "Речь" для Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Сборка и выполнение примеров

Дополнительные сведения о сборке и выполнении примеров см. на странице [Файл сведений](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Описание образцов

### <a name="create-recognition-clients"></a>Создание клиентов для распознавания

В следующем примере кода показано создание клиентских классов распознавания на основе сценариев пользователя.

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Клиентская библиотека предоставляет предварительно реализованные клиентские классы распознавания для типичных сценариев в распознавания речи.

* `DataRecognitionClient`: распознавание речи с использованием данных PCM (например, из файла или из звукового источника файла). Данные помещаются в буферы, и каждый буфер отправляется в службу распознавания речи. В буферах не производится никакие изменения, поэтому пользователь может применить собственные обнаружения тишины, при необходимости. Если данные предоставляются из WAV-файлов, можно отправлять данные из файла прямо в службу "Речь". Если имеются необработанные данные, например устная речь, поступающая через Bluetooth, сначала отправьте заголовок формата в службу "Речь", а затем данные.
* `MicrophoneRecognitionClient`: распознавание речи, поступающей с микрофона. Убедитесь, что микрофон включен и данные отправляются в службу распознавания речи. Встроенный детектор тишины применяется к данным микрофона перед их отправкой в ​​службу распознавания.
* `DataRecognitionClientWithIntent` и `MicrophoneRecognitionClientWithIntent`: эти клиенты возвращают, помимо распознавания текста, структурированную информацию о намерениях говорящего, которая может использоваться для дальнейших действий приложения. Чтобы использовать "Намерение", необходимо сначала обучить модель с помощью [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Распознавание языка

При использовании `SpeechRecognitionServiceFactory` для создания клиента необходимо выбрать язык. Полный список языков, поддерживаемых службой распознавания речи, см. в статье [Supported languages](../API-Reference-REST/supportedlanguages.md) (Поддерживаемые языки).

### `SpeechRecognitionMode`

Необходимо указать `SpeechRecognitionMode` при создании клиента с помощью `SpeechRecognitionServiceFactory`.

* `ShortPhrase`: фраза продолжительностью до 15 секунд. Когда данные отправляются в службу, клиент получает несколько промежуточных результатов и один конечный результат с несколькими лучшими вариантами.
* `LongDictation`: фраза продолжительностью до 2 минут. После отправки данных в службу клиент получит несколько промежуточных и несколько конечных результатов в зависимости от того, как служба распознает паузы предложения.

### <a name="attach-event-handlers"></a>Вложение обработчиков событий

Можно вложить различные обработчики событий в созданный клиент.

* **События частичных результатов**. Это событие вызывается каждый раз, когда служба речи предсказывает, что вы можете сказать, даже до того, как вы закончите разговор (если вы используете `MicrophoneRecognitionClient`) или закончите отправку данных (если вы используете `DataRecognitionClient`).
* **События ошибок**. Вызываются, когда служба обнаруживает ошибку.
* **События с намерением**. Вызывает на клиенты "WithIntent" (только в режиме`ShortPhrase`) после синтаксического анализа распознавания окончательного результата в структурированном намерении JSON.
* **События результатов**.
  * В режиме `ShortPhrase` это событие вызывается и возвращает самые лучшие результаты после окончания речи.
  * В режиме `LongDictation` обработчик событий может вызываться несколько раз в зависимости от того, где служба распознает паузы в предложении.
  * **Для каждого из самых лучших вариантов** возвращаются значение достоверности и несколько различных форм распознанного текста. Для получения дополнительных сведений см. раздел [Формат выходных данных](../Concepts.md#output-format).

## <a name="related-topics"></a>Связанные разделы

* [Справочник по клиентской библиотеке для Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Get started with the Speech Recognition API in C# for .NET on Windows](GetStartedCSharpDesktop.md) (Начало работы с API распознавания речи в C# для .NET на платформе Windows)
* [Приступите к работе с API распознавания речи Microsoft в Objective-C на iOS](Get-Started-ObjectiveC-iOS.md)
* [Приступите к работе с API распознавания речи Microsoft в JavaScript](GetStartedJSWebsockets.md)
* [Начало работы с API распознавания речи Microsoft с использованием REST](GetStartedREST.md)
