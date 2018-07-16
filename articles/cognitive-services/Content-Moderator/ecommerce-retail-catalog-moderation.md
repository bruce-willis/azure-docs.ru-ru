---
title: Модерация каталогов электронной коммерции с помощью машинного обучения, ИИ и Azure Content Moderator| Документация Майкрософт
description: Автоматическая модерация каталогов электронной коммерции с помощью ИИ и машинного обучения.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 5cb93941751c8744a19a5473d13f1e135a589eaa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380261"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>Модерация каталогов электронной коммерции с помощью машинного обучения

В этом руководстве описывается, как реализовать машинную интеллектуальную модерацию каталогов электронной коммерции, объединив машинные технологии ИИ с пользовательской модерацией, чтобы создать интеллектуальную систему каталогизации.

![Классифицированные изображения продуктов](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Бизнес-сценарий

Машинные технологии можно использовать, чтобы классифицировать и модерировать изображения продуктов по следующим категориям:

1. содержимое для взрослых (нагота);
2. непристойное содержимое;
3. знаменитости;
4. государственные флаги;
5. игрушки;
6. ручки.

## <a name="tutorial-steps"></a>Этапы руководства

В данном руководстве описываются следующие шаги:

1. Регистрация и создание группы Content Moderator.
2. Настройка тегов модерации (меток) для потенциального содержимого со знаменитостями и флагами.
3. Поверка изображений на наличие потенциального содержимого для взрослых и непристойного содержимого с помощью API проверки Content Moderator.
4. Проверка изображений на наличие потенциальных знаменитостей с помощью API компьютерного зрения.
5. Проверка изображений на наличие флагов с помощью Пользовательской службы визуального распознавания.
6. Предоставление уточненных результатов проверки для пользовательской проверки и окончательного принятия решений.

## <a name="create-a-team"></a>Создание команды

Обратитесь к странице [краткого руководства](quick-start.md), чтобы зарегистрироваться в Content Moderator и создать команду. Запишите **идентификатор команды**, указанный на странице **Credentials** (Учетные данные).


## <a name="define-custom-tags"></a>Определение настраиваемых тегов

Обратитесь к статье [О тегах](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags), чтобы добавить настраиваемые теги. Наряду со встроенными тегами **adult** и **racy** новые теги позволяют инструменту проверки показывать описательные имена тегов.

В нашем случае мы определяем пользовательские теги **celebrity**, **flag**, **us**, **toy** и **pen**.

![Настройка пользовательских тегов](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Вывод списка ключей и конечных точек API

1. В руководстве используются три интерфейса API, а также соответствующие ключи и конечные точки API.
2. Конечные точки API зависят от регионов подписки и идентификатора команды проверки Content Moderator.

> [!NOTE]
> Данное руководство предназначено для использования ключей подписки в регионах, доступных в следующих конечных точках. Убедитесь, что ваши ключи API соответствуют универсальным кодам ресурса (URI) регионов, в противном случае эти ключи не будут работать с приведенными ниже конечными точками.

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Проверка на наличие содержимого для взрослых и непристойного характера

1. Эта функция принимает в качестве параметров URL-адрес изображения и массив пар "ключ-значение".
2. Она вызывает API модерации изображений Content Moderator для получения оценок зрелости и неприличности.
3. Если оценка больше 0,4 (используется диапазон от 0 до 1), то значению **ReviewTags** в массиве присваивается значение **True**.
4. Массив **ReviewTags** используется для выделения соответствующего тега в инструменте проверки.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Поиск знаменитостей

1. Зарегистрируйтесь для использования [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) [API компьютерного зрения](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Нажмите кнопку **Get API Key** (Получить ключ API).
3. Примите условия.
4. Чтобы войти в систему, выберите из списка доступную учетную запись в Интернете.
5. Запишите ключи API, которые отображаются на странице службы.
    
   ![Ключи API компьютерного зрения](images/tutorial-computer-vision-keys.PNG)
    
6. Обратитесь к исходному коду проекта, чтобы ознакомиться с функцией, которая проверяет изображения с помощью API компьютерного зрения.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Классификация по категориям флагов, игрушек и ручек

1. [Войдите](https://www.customvision.ai/account/signin) в [предварительную версию API пользовательского визуального распознавания](https://www.customvision.ai/).
2. Используйте [краткое руководство](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) для создания пользовательского классификатора, позволяющего определить потенциальное наличие флагов, игрушек и ручек.
   ![Учебные изображения для службы "Пользовательское визуальное распознавание"](images/tutorial-ecommerce-custom-vision.PNG)
3. [Получите URL-адрес конечной точки прогнозирования](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) для своего пользовательского классификатора.
4. Обратитесь к исходному коду проекта, чтобы ознакомиться с функцией, которая вызывает конечную точку прогнозирования пользовательского классификатора для проверки изображения.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Проверки с участием человека

1. В предыдущих разделах вы проверяли входящие изображения на наличие содержимого для взрослых и неприличного содержимого (Content Moderator), знаменитостей (API компьютерного зрения) и флагов (Пользовательское визуальное распознавание).
2. На основе порогов для каждой проверки подготовьте уточненные данные для пользовательской проверки в инструменте проверки.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Отправка пакета изображений

1. В этом руководстве предполагается, что каталог C:\Test содержит текстовый файл со списком URL-адресов изображений.
2. Следующий код проверяет существование этого файла и считывает все URL-адреса в память.
            // Проверка каталога test на наличие текстового файла со списком URL-адресов изображений для проверки var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Запуск всех проверок

1. Эта функция верхнего уровня обходит все URL-адреса изображений в текстовом файле, упомянутом ранее.
2. Она проверяет их с помощью каждого API, и если оценка достоверности соответствия удовлетворяет нашим критериям, функция создает проверку для модераторов-пользователей.
             // для каждого URL-адреса изображения в файле... foreach (var Url in Urls) { // Инициализация нового массива тегов проверки ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Лицензия

Все пакеты SDK и примеры для Microsoft Cognitive Services регулируются лицензией MIT. Дополнительные сведения см. в разделе о [лицензировании](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Правила поведения для разработчиков

Ожидается, что разработчики, использующие Cognitive Services, включая эту клиентскую библиотеку и пример, следуют правилам поведения разработчиков для служб Microsoft Cognitive Services, доступным здесь: http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Дополнительная информация

Выполните сборку решений из руководства и дополните его, воспользовавшись [исходными файлами проекта](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) на сайте GitHub.
