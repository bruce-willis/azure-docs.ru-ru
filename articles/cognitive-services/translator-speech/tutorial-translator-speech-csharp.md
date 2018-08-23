---
title: Руководство по Переводу речи (C#) | Документация Майкрософт
titleSuffix: Cognitive Services
description: Сведения об использовании службы "Перевод речи" для перевода текста в режиме реального времени.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 010ad8b5ceeaf046c8d361ff352e6058154a482d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "41936762"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Руководство. Приложение WPF Microsoft Translator на языке C#

Это руководство содержит обзор средства интерактивного перевода речи, которое использует службу "Перевод речи Microsoft" как элемент Microsoft Cognitive Services в Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Запрос списка языков, поддерживаемых службой.
> * Запись и передача звука в службу.
> * Получение и отображение переводов речи в виде текста.
> * Воспроизведение устной (преобразование текста в речь) версии перевода (при необходимости).

Файл решения Visual Studio для этого приложения [доступный в GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходим любой выпуск Visual Studio 2017, включая Community Edition. 

Решение Visual Studio также создает установщик для приложения. Требуется [набор инструментов WiX](http://wixtoolset.org/) и [расширение Visual Studio для набора инструментов WiX](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension), чтобы поддерживать эту функцию.

Необходим также ключ подписки для службы "Перевод речи", который можно получить с помощью панели мониторинга Microsoft Azure. Доступная бесплатная ценовая категория позволяет перевести в месяц до 10 часов речи бесплатно. Для данного руководства этой ценовой категории будет достаточно.

Сторонняя [библиотека JSON.Net](https://www.newtonsoft.com/json) (от Newtonsoft) также необходима. NuGet автоматически устанавливает эту сборку, если в параметрах Visual Studio установлены оба флажка восстановления пакетов.

## <a name="trying-the-translation-app"></a>Проверка приложения перевода в действии

Открыв в Visual Studio решение "Перевод речи Microsoft" (`SpeechTranslator.sln`), нажмите клавишу F5 для создания и запуска приложения.  Откроется главное окно программы.

![[Главное окно службы "Перевод речи"]](media/speech-translator-main-window.png)

Во время первого выполнения выберите **параметры учетной записи** в меню **параметров**, чтобы открыть окно, показанное ниже.

![[Главное окно службы "Перевод речи"]](media/speech-translator-settings-window.png)

В этом окне вставьте ключ подписки службы "Перевод речи Microsoft", затем нажмите кнопку **Сохранить**. Ключ сохраняется при последующих запусках.

Вернитесь в главное окно, выберите устройства ввода и вывода звука, которые вы хотите использовать, а также исходный и целевой язык. Если вы хотите услышать перевод, убедитесь, что включен параметр **TTS** (преобразование текста в речь). Если вы хотите увидеть предположительные частичные переводы во время произнесения, включите параметр **Partial Results** (Частичные результаты).

Наконец, нажмите кнопку **Запуск**, чтобы начать перевод. Скажите фразу, которую нужно перевести, и посмотрите на распознанный текст и перевод, которые отобразятся в окне. Если вы включили параметр TTS, вы также услышите перевод.

## <a name="obtaining-supported-languages"></a>Сведения о поддерживаемых языках

На момент написания этой статьи для перевода текста служба Microsoft Translator поддерживает более пяти десятков языков. Для перевода речи поддерживается меньше языков. Такие языки требуют поддержки как для транскрибирования (распознавания речи), так и для вывода при преобразовании текста в речь, синтеза.

Другими словами, для перевода речи исходный язык должен быть одним из поддерживаемых языков для транскрибирования. Выходной язык может быть любым из языков, поддерживаемых для перевода текста, если вам нужен текстовый результат. Если требуется вывод речи, то вы можете переводить только на язык, поддерживаемый для преобразования текста в речь.

Корпорация Майкрософт может время от времени добавлять поддержку новых языков. По этой причине не следует в приложении жестко задавать набор знаний поддерживаемых языков. Теперь API перевода речи предоставляет конечную точку языков, что позволяет получить поддерживаемые языки в среде выполнения. Вы можете получить один или несколько списков языков: 

| | |
|-|-|
|`speech`|Языки, поддерживаемые для транскрибирования речи. Могут быть исходными языками для перевода речи.|
|`text`|Языки, поддерживаемые для текстового перевода. Могут быть целевыми языками для перевода речи, если используется текстовый вывод.|
|`tts`|Голоса, поддерживаемые в синтезе речи, каждый из которых связан с определенным языком. Могут быть целевыми языками для перевода речи при использовании преобразования текста в речь. Язык может поддерживаться для нескольких голосов.|

Конечная точка языков не требует ключ подписки, и ее использование не учитывается в квоте. URI — `https://dev.microsofttranslator.com/languages`, возвращающий результаты в формате JSON.

Метод `UpdateLanguageSettingsAsync()` в `MainWindow.xaml.cs`, показанный здесь, вызывает конечную точку языков, чтобы получить список поддерживаемых языков. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Этот метод сначала создает запрос HTTP к конечной точке языков, запрашивая все три списка языков (`text`, `speech` и `tts`).

Конечная точка языков использует заголовок запроса `Accept-Languages`, чтобы определить язык, на котором представлены названия языков. Например, язык, известный англоговорящим как German, называется Deutsch на немецком и Alemán на испанском языке. В списке языков отражаются эти различия. Для этого заголовка используется язык системы по умолчанию.

После отправки запроса и получения ответа JSON ответ анализируется во внутренних структурах данных. Эти структуры затем используются для создания меню исходного и целевого языков. 

Невозможно настроить меню голоса, так как доступные голоса зависят от исходного языка, выбранного пользователем. Доступные голоса для каждого языка хранятся для последующего использования. Обработчик `ToLanguage_SelectionChanged` (в том же исходном файле) позже обновляет меню голоса путем вызова `UpdateVoiceComboBox()` при выборе пользователем целевого языка. 

Целевой язык выбирается случайным образом, если пользователь не запускал приложение раньше. (Параметры меню сохраняются между сеансами.)

## <a name="authenticating-requests"></a>Выполнение проверки подлинности запросов

Для проверки подлинности службы "Перевод речи Microsoft" вам необходимо в запросе на подключение отправить ключ подписки Azure в заголовке как значение для `Ocp-Apim-Subscription-Key`.

## <a name="translation-overview"></a>Общие сведения о переводе

API перевода (конечная точка WebSockets `wss://dev.microsofttranslator.com/speech/translate`) принимает аудио для перевода в монофоническом формате, 16 кГц, 16-разрядный WAVE. Служба возвращает один или несколько ответов JSON, содержащих распознанный и переведенный текст. Если запрошено преобразование текста в речь, то отправляется звуковой файл.

Пользователь выбирает источник аудио, используя меню микрофона или входного файла. Звук может поступать с аудиоустройства (например, микрофона) или из файла `.WAV`.

Метод `StartListening_Click` вызывается, когда пользователь нажимает кнопку "Запуск". Этот обработчик событий, в свою очередь, вызывает `Connect()` для начала процесса отправки аудио для конечной точки API службы. Метод `Connect()` выполняет следующие задачи:


> [!div class="checklist"]
> * Получение параметров пользователя из главного окна и их проверка.
> * Инициализация входных и выходных аудиопотоков.
> * Вызов `ConnectAsync()` для выполнения оставшейся работы.

`ConnectAsync()`, в свою очередь, выполняет следующие рутинные операции:

> [!div class="checklist"]
> * Проверка подлинности в заголовке `Ocp-Apim-Subscription-Key` с ключом подписки Azure.
> * Создание экземпляра `SpeechClient` (найденного в `SpeechClient.cs`) для взаимодействия со службой.
> * Инициализация экземпляра `TextMessageDecoder` и `BinaryMessageDecoder` (см. `SpeechResponseDecoder.cs`) для обработки ответов.
> * Отправка звука через экземпляр `SpeechClient` в службу "Перевод речи".
> * Получение и обработка результатов перевода.

`SpeechClient` выполняет меньше операций:

> [!div class="checklist"]
> * Установка соединения WebSocket со службой "Перевод речи".
> * Отправка аудиоданных и получение ответов через сокет.

## <a name="a-closer-look"></a>Более подробный взгляд

Теперь должно быть понятно, как взаимодействуют элементы приложения для выполнения запроса на перевод. Давайте взглянем на код, сосредоточившись на соответствующих элементах.

Вот неполная версия `Connect()`, в которой показана настройка аудиопотоков:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Значительная часть `Connect()` включает создание экземпляра `SpeechClientOptions` (см. `SpeechClientOptions.cs`) для хранения параметров перевода. Параметры содержат сведения, необходимые для подключения к службе (например, ключ проверки подлинности и имя узла), и функции, используемые для перевода. Здесь поля сопоставляются с полями заголовков и параметрами HTTP, предоставляемыми [API перевода речи](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` также создает и инициализирует входное аудиоустройство (переменная `sampleProvider`), которое служит источником речи для перевода. Это устройство соответствует такому аппаратному устройству ввода, как, например, микрофон, или файлу WAVE, содержащему аудиоданные.

Вот метод `ConnectAsync()`, создающий экземпляр класса `speechClient` и подключающий анонимные функции для обработки текста и двоичных ответов службы.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

После проверки подлинности метод создает экземпляр `SpeechClient`. Класс `SpeechClient` (в `SpeechClient.cs`) вызывает обработчики событий при получении двоичных и текстовых данных. Дополнительные обработчики вызываются при сбое подключения или отключениях.

Двоичные данные являются аудиосодержимым (выходные данные преобразования текста в речь), отправленным службой при включенном параметре TTS. Текстовые данные будут частичным или полным переводом произносимого текста. Поэтому после создания экземпляра метод подключает функции для обработки таких сообщений: аудио, сохраняя его для последующего воспроизведения, и текста, отображая его в окне.

## <a name="next-steps"></a>Дополнительная информация

Этот пример кода представляет собой приложение с расширенными возможностями и демонстрирует использование API перевода речи. Таким образом, имеется довольно много составных элементов, с которыми следует разобраться. Вы ознакомились с наиболее важными моментами. В остальном может быть полезно установить несколько точек останова в Visual Studio и изучить процесс перевода. Разобравшись с примером приложения, вы сможете использовать в своих приложениях службу "Перевод речи".

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API reference](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) (Справочник по API перевода речи Microsoft)
