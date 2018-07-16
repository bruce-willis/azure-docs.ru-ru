---
title: Руководство по мобильному приложению для визуального поиска
description: Приложение C# с открытым кодом внедряет визуальный поиск, используя API компьютерного зрения Cognitive Services, API Bing для поиска в Интернете и кроссплатформенную платформу Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380768"
---
# <a name="visual-search-mobile-app-tutorial"></a>Руководство по мобильному приложению для визуального поиска

## <a name="introduction"></a>Введение  
В этом руководстве рассматриваются конечные точки [API компьютерного зрения](https://azure.microsoft.com/services/cognitive-services/computer-vision/) и [API Bing для поиска в Интернете](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) и показано, как их можно использовать для создания базового приложения для визуального поиска с [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  В целом в этом руководстве рассматриваются следующие темы: 

> [!div class="checklist"]
> * Настройка Visual Studio для разработки приложений Xamarin.Forms.
> * Использование [подключаемого модуля медиаданных Xamarin](https://github.com/jamesmontemagno/MediaPlugin) для записи и импорта данных изображений.
> * Синтаксический анализ текста с изображения с помощью API компьютерного зрения.
> * Отправка запросов на поиск в API Bing для поиска в Интернете.
> * Синтаксический анализ ответов JSON из обоих интерфейсов API с помощью [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (с помощью LINQ и десериализации объекта модели).
> * Создание кроссплатформенного пользовательского интерфейса Xamarin.Forms для визуального поиска. 

## <a name="prerequisites"></a>предварительным требованиям

Этот пример разработан с использованием Xamarin.Forms с [Visual Studio 2017](https://www.visualstudio.com/downloads/). Может использоваться выпуск Community Edition Visual Studio 2017. Дополнительные сведения об использовании Xamarin с Visual Studio см. в [документации по Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/).

В этом примере используются следующие пакеты NuGet:

> [!div class="checklist"]
> * [подключаемый модуль медиаданных Xamarin](https://github.com/jamesmontemagno/MediaPlugin);
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

Приложение использует следующие API-интерфейсы Cognitive Services:

> [!div class="checklist"]
> * [API Bing для поиска в Интернете](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/); 
> * [API компьютерного зрения](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Сведения о получении ключей 30-дневной пробной версии для этих API-интерфейсов см. на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Дополнительные сведения о получении ключей для коммерческого использования см. на странице [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Настройка для разработки  

### <a name="installing-xamarin-on-windows"></a>Установка Xamarin в Windows
Если у вас установлен любой выпуск Visual Studio 2017, откройте Visual Studio Installer, выберите меню с тремя полосками, связанное с вашей установкой Visual Studio, и выберите **Изменить**.  

![Visual Studio Installer](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Прокрутите список вниз до раздела Mobile & Gaming (Мобильные приложения и игры) и установите флажок **Mobile Development with .NET** (Разработка мобильных приложений на .NET), если он не установлен.

![Visual Studio Installer с выбранным Xamarin.Forms](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Наконец, нажмите кнопку **Изменить** в нижнем правом углу окна и дождитесь установки Xamarin.

### <a name="installing-xamarin-on-macos"></a>Установка Xamarin в macOS
Xamarin предварительно поставляется вместе с Visual Studio для Mac. Установка не требуется.

## <a name="building-and-running-the-app"></a>Сборка и запуск приложения

Файл решения Visual Studio *(SLN)* для приложения визуального поиска можно загрузить [здесь](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Загрузите ZIP-архив в веб-браузере, клонируйте его на рабочую станцию из GitHub или загрузите его с помощью Visual Studio.

Чтобы приступить к работе с примером, откройте `VisualSearchApp.sln` в Visual Studio.  Инициализация всех необходимых компонентов может занять некоторое время.

Приложению требуется две сторонних библиотеки: **Json.NET** и **подключаемый модуль медиаданных Xamarin**. Эти библиотеки можно установить непосредственно в Visual Studio с помощью диспетчера пакетов NuGet. Выберите **Инструменты > Диспетчер пакетов NuGet > Manage NuGet Packages For Solution (Управление пакетами NuGet для решения)** или щелкните правой кнопкой мыши решение в обозревателе решений и выберите **Управление пакетами NuGet** в контекстном меню.

В окне NuGet найдите и установите **подключаемый модуль медиаданных Xamarin** (Xam.Plugin.Media) версии 2.6.2 и **Json.NET** (Newtonsoft.Json) 10.0.3. Выберите все проекты при установке.

Чтобы построить приложение для всех доступных платформ, нажмите клавиши **Ctrl + Shift + B** или щелкните **Построить** в меню ленты и выберите **Построить решение**.  Сведения о компиляции и тестировании кода для iOS из системы Windows см. в [этом руководстве](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/).

Перед запуском приложения необходимо выбрать целевую конфигурацию, платформу и проект.  Приложения Xamarin.Forms компилируются в машинный код для Windows, iOS и Android. В этом руководстве показаны снимки экрана версии Windows примера, но все версии функционально эквивалентны. Здесь приведены параметры развертывания, используемые в этом руководстве.  

![Visual Studio, настроенный для компиляции с телефонов Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

После успешного процесса построения и выбора целевой платформы нажмите кнопку **Запуск** на панели инструментов или нажмите клавишу **F5**. Visual Studio развертывает ваше решение на целевой платформе и запускает его.

Появится страница "Добавить ключи". (Эта страница определена в `AddKeysPage.xaml`.)  

![Изображение страницы "Добавить ключи"](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Здесь вставьте ключи API компьютерного зрения и API Bing для поиска в Интернете. Для API компьютерного зрения также необходимо выбрать сервер, на котором размещена конечная точка.

> [!TIP]
> Чтобы пропустить эту страницу, введите эти сведения в соответствующих местах в `App.xaml.cs`. 

Приложение проверяет ключи путем выполнения тестового запроса, а затем открывается страница OCR Select (Выбор распознавания текста) (определенная в `OcrSelectPage.xaml`).
   
![Изображение страницы OCR Select (Выбор распознавания текста)](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

В верхней части этого экрана можно выбрать способ распознавания текста: печатный или рукописный.

> [!TIP]
> Удерживайте элемент, начиная с которого необходимо распознать текст, максимально горизонтально и убедитесь, что он равномерно подсвечивается без отражения. Мы обнаружили, что функция распознавания рукописного текста иногда лучше работает с рукописными шрифтами или другими фантазийными текстами.

Далее щелкните **Take Photo** (Сфотографировать) или **Импорт фото**, чтобы сделать фотографию с помощью камеры на устройстве или выбрать фотографию, хранящуюся на устройстве.

После того как фотография сделана или выбрана, изображение передается в API компьютерного зрения. На странице Words Found (Найденные слова) (определена в `OcrResultsPage.xaml`) отобразятся все слова, указанные в изображении.

![Изображение страницы результатов распознавания текста](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> Изображение, использованное для этих результатов, находится в исходном репозитории с именем `SamplePhotos\TestImage.jpg`.

Если щелкнуть элемент на странице Words Found (Найденные слова), на странице "Результаты поиска в Интернете" (`WebResultsPage.xaml`) появится топ результатов Bing для этого поиска.

![Изображение страницы результатов поиска в Интернете](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Наконец, выберите элемент на странице результатов поиска в Интернете, чтобы открыть ссылку во встроенном веб-представлении. (Или перейдите назад, чтобы выбрать другой результат.)

![Изображение страницы веб-представления](./media/computer-vision-web-search-tutorial/web-view-page.png)  

Вы можете работать на странице, как и в любом веб-браузере, или вернуться на страницу результатов поиска в Интернете. 

## <a name="review-and-learn"></a>Просмотр и изучение

После того, как вы проверили приложение визуального поиска в деле, давайте теперь узнаем, каким именно образом используются два API-интерфейса Cognitive Services.  Используете ли вы этот пример в качестве отправной точки для собственного приложения либо просто как практическое руководство для API-интерфейсов, важно пройти все шаги приложения поэтапно, чтобы проверить, как именно оно работает.

### <a name="add-keys-page"></a>Страница Add Keys (Добавление ключей)
Пользовательский интерфейс страницы Add Keys (Добавление ключей) определен в `AddKeysPage.xaml`, а ее основная логика определена в `AddKeysPage.xaml.cs`. Так как ключи проверяются путем выполнения тестового запроса, пришло время установить использование конечных точек Cognitive Services в C#.  

Базовая структура взаимодействия такова: 

1. Создайте объекты *HttpResponseMessage* и *HttpClient* из *System.Net.Http*.
2. Подключите все нужные заголовки (определенные в справочник по API для каждой конечной точки) для объекта *HttpClient*.
3. Отправьте запрос GET или POST с данными, добавив все необходимые параметры в URI конечной точки.
4. Проверьте успешность ответа.
5. Передайте ответ для дальнейшей обработки.

Функция, которая проверяет действительность ключа API Поиска Bing, — `CheckBingSearchKey()`, показана ниже.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Подобная функция, `CheckComputerVisionKey()`, используется для проверки ключа Компьютерного зрения.

### <a name="ocr-select-page"></a>Страница OCR Select (Выбор распознавания текста)

У страницы OCR Select (Выбор распознавания текста) (`OcrSelectPage.xaml`) две важные роли. Во-первых, она определяет, какой тип распознавания текста выполняется на целевой фотографии. Во-вторых, она дает пользователю возможность сделать или открыть изображение, которое требуется обработать.

Вторая задача может быть довольно сложной в кроссплатформенном приложении, так как для каждой платформы требуется различный код. Подключаемый модуль медиаданных Xamarin обработает его с помощью всего нескольких строк кода.

Следующая функция включает пример использования подключаемого модуля медиаданных Xamarin для фотосъемки.  В нем нужно сделать следующее:

1. Убедитесь, что на текущем устройстве доступна камера.
2. Создайте объект `StoreCameraMediaOptions`, чтобы указать расположение для сохранения сделанного снимка.
3. Сделайте снимок и получите объект `MediaFile`, содержащий данные снимка.
4. Распакуйте `MediaFile` в массив байтов.
5. Возвратите массив байтов для дальнейшей обработки.

`TakePhoto()` — функция, которая использует подключаемый модуль медиаданных Xamarin для записи фотографий.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
Программа импорта фотографий работает аналогичным образом. Оба этих компонента можно найти в `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> Конечная точка рукописного распознавания текста может обрабатывать только фотографии, размер которых меньше 4 МБ. Во избежание проблем с размером файла мы уменьшаем размер фотографии на 50 % от исходного, задав параметр `PhotoSize = PhotoSize.Medium` в объекте `StoreCameraMediaOptions`.  Если устройство принимает исключительно фотографии высокого качества и вы получаете ошибки, вместо него можно попробовать `PhotoSize = PhotoSize.Small`.

Вот служебная функция, используемая для преобразования *MediaFile* в массив байтов.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Страница результатов распознавания текста

На странице результатов распознавания текста отображается текст, извлеченный из изображения с помощью [метода SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) **Json.NET**.  Две конечные точки распознавания текста работают немного по-разному, поэтому важно рассмотреть обе.  

Так как API компьютерного зрения размещается только в нескольких расположениях серверов, его конечная точка должна быть создана динамически во время выполнения. Функция `SetOcrLocation` (часть статического класса *AppConstants* в `AppConstants.cs`) задает расположение конечной точки URI. Она соответствует выбору пользователя на странице Add Keys (Добавление ключей) или использует значение, заданное в `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Рассмотрим эти запросы API подробнее. API распознавания текста Компьютерного зрения способен анализировать текст с неопределенного языка, но нам необходимо, чтобы он искал текст на английском языке для улучшения результатов. Мы также позволяем API определить ориентацию текста. Используя предопределенную ориентацию, можно улучшить результаты анализа, но определение ориентации может быть полезно в мобильном приложении.

Дополнительные сведения о параметрах, доступных при использовании этой конечной точки, см. в [справочнике по API оптического распознавания печатных символов](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

API распознавания рукописного текста находится на стадии предварительной версии и сейчас работает только с англоязычным текстом.  По этой причине его единственный параметр сейчас — это флаг, указывающий, нужно ли вообще анализировать рукописный текст. API распознавания рукописного текста может анализировать как печатный, так и рукописный текст, но `handwriting=false` дает лучшие результаты с печатным текстом. 

Так как это приложение на английском языке, для этого примера мы можем использовать только распознавание рукописного текста, а также задать флаг `handwriting`, согласно с предпочтениями пользователя относительно распознавания.  Мы использовали обе конечные точки в качестве примера.  

Дополнительные сведения о параметрах, доступных при использовании этой конечной точки, см. в [справочнике по API оптического распознавания рукописных символов](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Теперь давайте рассмотрим функции, которые вызывают эти API-интерфейсы.

`FetchPrintedWordList()` использует конечную точку распознавания печатного текста, чтобы анализировать печатный текст с изображений. HTTP-запрос соответствует структуре вызова, используемого на странице Add Keys (Добавление ключей) для проверки ключа, но нам необходимо отправить фото. Фото слишком велико для передачи в строке запроса, поэтому вместо запроса GET мы должны использовать запрос HTTP POST. Нам нужно закодировать наше фото (которое существует в памяти в виде массива байтов) в объект `ByteArrayContent` и добавить заголовок в этот объект, определяющий тип отправляемых данных. 

Сведения о допустимых типах содержимого см. в [справочнике по API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Обратите внимание на использование [метода SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) **Json.NET** для извлечения текста из объекта ответа. `SelectToken` идеально подходит, если необходима только часть ответа JSON, которую можно затем передать в следующую функцию. В других частях кода мы десериализируем ответы JSON в объекты модели, определенные в `ModelObjects.cs`.

Основное различие между запросами распознавания печатного и рукописного текста заключается в том, что служба распознавания печатного текста возвращает распознанный текст как часть ответа HTTP, в то время как службе распознавания рукописного текста требуется дополнительный запрос для получения сведений. Первоначальный запрос распознавания рукописного текста возвращает состояние HTTP 202, которое сообщает только о начале обработки. Ответ содержит конечную точку, которую клиент должен проверить, чтобы получить законченный ответ, если он доступен. Принципы его работы см. в `FetchHandwrittenWordList()`.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` — вторая часть процесса распознавания рукописного текста. Он проверяет связь с URI, извлеченным из метаданных первого ответа, до получения результата или истечения времени ожидания функции.  Очень важно вызывать эту функцию асинхронно в отдельном потоке. В противном случае этот метод может заблокировать пользовательский интерфейс до завершения обработки.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Страница результатов поиска в Интернете
После того как пользователь выберет условие поиска, отображаемое на странице результатов распознавания текста, мы переходим на страницу результатов поиска в Интернете.  Здесь мы создаем запрос [API Bing для поиска в Интернете](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/), отправляем его в конечную точку службы и десериализируем ответ JSON с помощью метода [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) **Json.NET**.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

API Bing для поиска в Интернете лучше всего работает, если предоставить как можно больше информации о том, что может захотеть пользователь. В частности, почти всегда необходимо использовать параметры `mkt` и `setLang` (здесь задан английский язык), чтобы определить расположение и предпочитаемый язык.

> [!NOTE]
> Мы упростили наш запрос API Bing для поиска в Интернете, чтобы исходный код был понятным.  Чтобы улучшить результаты, в реальном приложении в HTTP-запрос следует добавить следующие заголовки. 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> Дополнительные сведения об этих значениях заголовка см. в [справочнике по API Bing для поиска в Интернете](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers).

## <a name="next-steps"></a>Дальнейшие действия
Cognitive Services предоставляют дополнительные службы, которые можно легко интегрировать в приложение.  Например, можно выполнить следующее.

* Добавьте [API Bing для поиска сущностей](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/), чтобы расширить результаты поиска в Интернете.
* Вместо API Bing для поиска в Интернете перейдите на использование [API пользовательского поиска](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/).
* Используйте возможность получения сведений об изображении [API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/), чтобы узнать больше о сделанном снимке и найти подобные изображения в Интернете.
* Используйте [API проверки орфографии](https://azure.microsoft.com/services/cognitive-services/spell-check/) с целью повышения качества анализированного текста.
* Интегрируйте [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) для просмотра извлеченного текста на разных языках.
* Комбинируйте и сопоставляйте остальные службы [на портале Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Возможностям нет предела!
