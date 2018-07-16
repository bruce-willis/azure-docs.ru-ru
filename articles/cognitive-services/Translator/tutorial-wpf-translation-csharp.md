---
title: Написание приложения WPF Microsoft Translator на языке C#, Azure Cognitive Services | Документация Майкрософт
description: Узнайте, как использовать текстовую службу Translator для перевода текста, получения локализованного списка поддерживаемых языков и т. д.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380825"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Руководство по написанию приложения WPF Microsoft Translator на языке C#

В этом руководстве мы создадим интерактивное средство для перевода текста с использованием текстового API перевода текстов Microsoft Translator (V3), входящего в состав Microsoft Cognitive Services в Azure. Вы узнаете, как:

> [!div class="checklist"]
> * Запросить список коротких кодов для языков, поддерживаемых службой.
> * Получить список локализованных названий языков, соответствующих этим языковым кодам.
> * Получить переведенную версию введенного пользователем текста с одного языка на другой.

Это приложение также содержит интеграцию с двумя другими службами Microsoft Cognitive Services.

|||
|-|-|
|[Анализ текста](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Используется для необязательного автоматического определения исходного текста для перевода.|
|[Проверка орфографии Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Для текста на английском языке, используемого для исправления орфографических ошибок, поэтому перевод является более точным.

![[Работа программы обучения]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством необходим любой выпуск Visual Studio 2017, включая Community Edition.

Вам также нужны ключи подписки для трех служб Azure, используемых в программе. Вы можете получить ключ для текстовой службы Translator на панели мониторинга Azure. Доступная бесплатная ценовая категория позволяет перевести в месяц до 2 млн знаков бесплатно.

Службы "Анализ текста" и "Проверка орфографии Bing" предлагают бесплатные пробные версии, на которые вы можете подписаться на странице[Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Вы также можете создать подписку на любую службу на панели управления Azure. Служба "Анализ текста" имеет бесплатный уровень.

Исходный код для этого руководства приведен ниже. Ваши ключи подписки должны быть скопированы в исходный код в виде переменных `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` и т. д. в `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Служба "Анализ текста" доступна в нескольких регионах. URI в нашем учебном исходном коде находится в регионе `westus`, который используется для бесплатных пробных версий. Если у вас есть подписка в другом регионе, соответствующим образом обновите этот URI.

## <a name="source-code"></a>Исходный код

Это исходный код API для работы с текстами Microsoft Translator. Чтобы запустить приложение, скопируйте исходный код в соответствующий файл в новый проект WPF в Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Это файл с кодом, который обеспечивает функциональность приложения.

```cs
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog when we get an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml:

Этот файл определяет пользовательский интерфейс для приложения (форма WPF). Если вы хотите создать собственную версию формы, вам не нужен этот XAML.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox" 
                HorizontalAlignment="Left" 
                Margin="306,88,0,0" 
                VerticalAlignment="Top" 
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox" 
            HorizontalAlignment="Left" 
            Margin="42,88,0,0" 
            VerticalAlignment="Top" 
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Конечные точки службы

Служба Microsoft Translator имеет множество конечных точек, которые предоставляют различные функции перевода. Используемые в этом руководстве:

|||
|-|-|
|`Languages`|Возвращает набор языков, поддерживаемых в настоящее время другими операциями API перевода текстов.|
|`Translate`|Исходный текст, код исходного и целевого языка, возвращает перевод исходного текста на целевой язык.|

## <a name="the-translation-app"></a>Приложение перевода

Пользовательский интерфейс нашего приложения-переводчика создается с использованием Windows Presentation Foundation (WPF). Создайте проект WPF в Visual Studio, выполнив следующие шаги.

* В меню **Файл** выберите **Создать > Проект**.
* В окне "Новый проект" откройте **Установлено > Шаблоны > Visual C#**. В центре диалогового окна отображается список доступных шаблонов проектов.
* Убедитесь, что **.NET Framework 4.5.2** выбран в раскрывающемся меню над списком шаблонов проектов.
* Щелкните **WPF App (.NET Framework)** (Приложение WPF (.NET Framework)) в списке шаблонов проекта.
* Используя поля в нижней части диалогового окна, назовите новый проект и его решение.
* Нажмите кнопку **OK**, чтобы создать проект и решение.

![[Создание приложения WPF в Visual Studio]](media/translator-text-csharp-new-project.png)

Добавьте ссылки на следующие сборки .NET Framework в свой проект.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Установите следующий пакет NuGet `Newtonsoft.Json` в своем проекте.

Теперь найдите файл `MainWindow.xaml` в обозревателе решений и откройте его. Изначально он пуст. Вот как должен выглядеть готовый пользовательский интерфейс, аннотированный с использованием имен элементов управления синего цвета. Используйте те же имена для элементов управления в вашем пользовательском интерфейсе, так как они также отображаются в коде.

![[Аннотированный вид главного окна в конструкторе Visual Studio]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Исходный код в этом руководстве включает в себя источник XAML для этой формы. Вы можете вставить его в свой проект вместо создания формы в Visual Studio.

* `FromLanguageComboBox` *(поле со списком)* — отображает список языков, поддерживаемых Microsoft Translator для перевода текста. Пользователь выбирает язык, с которого нужно переводить.
* `ToLanguageComboBox` *(поле со списком)* — отображает тот же список языков, что и `FromComboBox`, но используется для выбора языка, на который пользователь переводит.
* `TextToTranslate` *(поле со списком)* — пользователь вводит здесь текст, который нужно перевести.
* `TranslateButton` *(кнопка)* — пользователь нажимает эту кнопку (или нажимает Enter) для перевода текста.
* `TranslatedTextLabel` *(метка)* — здесь отображается перевод текста пользователя.

Ваша версия этой формы не должна *точно* воспроизводить нашу. Но убедитесь, что раскрывающиеся списки выбора языка достаточно широкие, чтобы не обрезать имя языка.

## <a name="the-mainwindow-class"></a>Класс MainWindow

Файл кода программной части `MainWindow.xaml.cs`, где находится код, позволяет программе выполнять действия. Работа происходит в двух случаях:

* Во время запуска программы. Когда экземпляр `MainWindow` создается, мы извлекаем список языков с помощью API-интерфейсов `GetLanguagesForTranslate` и `GetLanguageNames` и заполняем раскрывающиеся меню. Эта задача выполняется один раз в начале каждой сессии.

* Когда пользователь нажимает кнопку **​​Перевести**, мы получаем варианты языка пользователя и текст, который он ввел. Затем мы вызываем API `Translate` для выполнения перевода. Мы также можем вызвать другие функции, чтобы определить язык текста и исправить его правописание перед переводом.

Давайте посмотрим, как мы начинаем класс:

```cs
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog when we get an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Две объявленные здесь переменные-члены содержат информацию о наших языках:

|||
|-|-|
|`languageCodes`<br>Массив строк|Кэширует коды языков. Служба Translator использует короткие коды, например `en` для английского языка, чтобы идентифицировать язык.|
|`languageCodesAndTitles`<br>SortedDictionary|Отображает понятные имена в пользовательском интерфейсе вместо коротких кодов, используемых в API. Они сортируются в алфавитном порядке без учета регистра.|

Первый код, выполняемый нашим приложением, — это конструктор `MainWindow`. Сначала мы устанавливаем метод `HandleExceptions` как глобальный обработчик ошибок. Таким образом, мы, по крайней мере, получаем предупреждение об ошибке, если какое-либо исключение не обрабатывается.

Затем мы проверяем, чтобы ключи подписки API состояли из 32 символов. Если это не так, наиболее вероятной причиной является то, что *кто-то* не вставил свои ключи API (tsk). В этом случае мы видим сообщение об ошибке и выходим из системы. (Передача этого теста не означает, что ключи действительны).

Если у нас есть ключи, по крайней мере правильной длины, вызов `InitializeComponent()` приводит к тому, что пользовательский интерфейс находит, загружает и создает описание XAML основного окна приложения.

Наконец, мы настраиваем раскрывающиеся меню языков. Для выполнения этой задачи требуется три отдельных вызова метода. Каждый из этих методов описан в следующих разделах.

## <a name="get-supported-languages"></a>Получение сведений о поддерживаемых языках

В данный момент служба Microsoft Translator поддерживает в общей сложности 61 язык. Со временем может быть добавлено больше. Поэтому лучше не жестко программировать поддерживаемые языки в вашей программе. Вместо этого запросите службу Translator, какие языки она поддерживает. Любой поддерживаемый язык может быть переведен на любой другой поддерживаемый язык.

Вызовите API `Languages`, чтобы получить список поддерживаемых языков.

API `Languages` принимает необязательный параметр запроса GET, *scope*. *scope* может иметь одно из трех значений: `translation`, `transliteration` и `dictionary`. Мы будем использовать значение `translation`.

API `Languages` также принимает необязательный заголовок HTTP `Accept-Language`. Значение этого заголовка определяет язык, на котором возвращаются имена поддерживаемых языков. Значение должно быть тегом языка BCP 47 с правильным форматом. Мы будем использовать значение `en`, чтобы получить имена языков на английском языке.

API `Languages` возвращает ответ JSON, который выглядит следующим образом.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Мы хотим извлечь языковые коды (например, `af`) и имена языков (например, `Afrikaans`). Для этого мы используем метод NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm).

Исходя из этого, мы создаем следующий метод для получения кодов языков и их названий.

```cs
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` сначала создает запрос HTTP. Параметр строки запроса `scope=translation` указывает, что мы хотим получить языки, которые поддерживаются для перевода текста. Мы добавляем ключ подписки API перевода текста в наши заголовки запросов. Мы также добавляем заголовок `Accept-Language` со значением `en`, чтобы указать, что мы хотим, чтобы поддерживаемые языки возвращались на английском языке.

По завершении запроса мы проанализируем ответ JSON и преобразуем его в словарь. Мы добавляем коды языков к переменной-члену `languageCodes`. Затем мы перебираем пары "ключ — значение", содержащие коды языков и понятные названия языков, и добавляем их к переменной-члену `languageCodesAndTitles`. (В раскрывающихся меню в нашей форме отображаются понятные имена, но нам нужны коды для запроса перевода).

## <a name="populate-the-language-menus"></a>Заполнение меню языка

Большая часть нашего пользовательского интерфейса определена в XAML, поэтому нам не нужно много настроек, кроме вызова `InitializeComponent()`. Единственное, что нам нужно сделать, это добавить понятные названия языков в раскрывающиеся списки "На" и "С", что выполняется в `PopulateLanguageMenus()`.

```cs
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Заполнение меню — это простая итерация словаря `languageCodesAndTitles` и добавление каждого ключа, который является понятным названием, в оба меню. После заполнения меню мы устанавливаем для языков по умолчанию для списков "На" и "С" значение "Определение" (для автоматического определения языка) и "Английский".

> [!TIP]
> Если мы не задаем выбор по умолчанию для наших меню, пользователь может нажать кнопку **Перевести**, не выбирая исходный и целевой язык. Параметры по умолчанию устраняют необходимость решения этой проблемы.

Теперь инициализируется `MainWindow`, создавая пользовательский интерфейс. Мы не получаем элемент управления, пока пользователь не нажмет кнопку **​​Перевести**.

## <a name="perform-translation"></a>Выполнение перевода

Когда пользователь нажимает кнопку **​​Перевести**, WPF вызывает обработчик событий `TranslateButton_Click()`, показанный здесь.

```cs
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Здесь мы сначала извлекаем исходный и целевой язык вместе с текстом из формы, который ввел пользователь.

Если для исходного языка установлено значение "Обнаружить", мы вызываем `DetectLanguage()`, чтобы определить язык текста. Текст может быть на языке, который API-интерфейсы перевода не поддерживают (может быть обнаружено больше языков, чем количество поддерживаемых для перевода), или API анализа текста, возможно, не сможет его обнаружить. В этом случае мы выводим сообщение для информирования пользователя и возврата без перевода.

Если исходным языком является английский (независимо от того, указан ли он или обнаружен), мы проверяем текст с помощью `CorrectSpelling()` и применяем любые исправления. Скорректированный текст добавляется обратно в поле ввода, поэтому пользователь знает, что была выполнена коррекция. (Пользователь может поставить дефис перед переводом текста, чтобы отключить исправление орфографии).

Если пользователь не ввел какой-либо текст или если исходный и целевой языки совпадают, перевод не требуется. В этом случае мы не передаем запрос.

Код для выполнения запроса на перевод должен выглядеть знакомым. Мы создаем URI, создаем запрос, отправляем его и обрабатываем ответ. Чтобы отобразить текст, мы храним его в элементе управления `TranslatedTextLabel`.

Мы передаем текст в API `Translate` в сериализованном массиве JSON в теле запроса POST. Массив JSON может содержать несколько фрагментов текста для перевода, но здесь мы включаем один.

Заголовок HTTP с именем `X-ClientTraceId` не является обязательным. Значение должно быть идентификатором GUID. Идентификатор трассировки клиента полезен для отслеживания запросов, когда что-то не работает должным образом. Однако, чтобы быть полезным, значение X-ClientTraceID должно быть записано клиентом. Идентификатор трассировки клиента и дата запросов могут помочь Майкрософт диагностировать проблемы, которые могут возникнуть.

> [!NOTE]
> В этом руководстве основное внимание уделяется службе Microsoft Translator, поэтому мы подробно не описываем методы `DetectLanguage()` и `CorrectSpelling()`. Службы "Анализ текста" и "Проверка орфографии Bing" предоставляют ответы в JSON, а не XML, и для службы "Анализ текста" требуется, чтобы запрос был отформатирован как JSON. Эти характеристики составляют большинство отличий кода от методов, которые мы уже видели.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Microsoft Translator Text API reference](http://docs.microsofttranslator.com/text-translate.html) (Руководство по API для работы с текстами Microsoft Translator)
