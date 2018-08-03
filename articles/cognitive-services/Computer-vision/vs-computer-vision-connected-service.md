---
title: Руководство по API компьютерного зрения для Python | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Подключение к API компьютерного зрения в Cognitive Services из веб-приложения ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 76ca1215144a5caa40971e1eda23f6462f7bf27b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38663912"
---
# <a name="connecting-to-cognitive-services-computer-vision-api-by-using-connected-services-in-visual-studio"></a>Подключение к API компьютерного зрения в Cognitive Services с помощью компонента "Подключенные службы" в Visual Studio

С помощью API компьютерного зрения в Cognitive Services можно извлекать из изображений ценные сведения для классификации и обработки визуальных данных, а функция машинной модерации изображений обеспечит необходимый контроль за вашими службами.

В этой статье и других статьях этой серии приводятся сведения об использовании компонента "Подключенные службы" Visual Studio для API компьютерного зрения в Cognitive Services. Эта возможность доступна как в Visual Studio 2017 15.7, так и в более поздней версии с установленным расширением Cognitive Services.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 версии 15.7** с установленной рабочей нагрузкой **Веб-разработка**. [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Добавление в проект поддержки API компьютерного зрения в Cognitive Services

1. Создайте проект веб-приложения ASP.NET Core. Используйте шаблон пустого проекта. 

1. В **обозревателе решений** выберите **Добавить** > **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.

   ![Пункт меню "Добавить подключенную службу"](../media/vs-common/Connected-Service-Menu.PNG)

1. В меню доступных служб выберите **Cognitive Services Computer Vision API** (API компьютерного зрения в Cognitive Services).

   ![Выбор службы для подключения](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок.

   ![Выберите свою подписку.](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Выберите подписку, которую необходимо использовать, придумайте имя для API компьютерного зрения или выберите ссылку "Изменить", чтобы изменить автоматически созданное имя, а затем выберите группу ресурсов и ценовую категорию.

   ![Изменение сведений о подключенной службе](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Для получения дополнительных сведений о ценовых категориях перейдите по ссылке.

1. Щелкните "Добавить", чтобы добавить поддержку для подключенной службы.
   Visual Studio модифицирует проект, чтобы добавить пакеты NuGet, записи файла конфигурации и другие изменения для поддержки подключения к API компьютерного зрения. В окне вывода показан журнал того, что происходит в проекте. Вы должны увидеть нечто вроде этого:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 1.0.2-preview.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Использование API компьютерного зрения для обнаружения атрибутов изображения

1. Добавьте следующие инструкции в Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Добавьте поле configuration, а также конструктор, который инициализирует поле configuration в классе `Startup`, чтобы включить Configuration в программе.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. В папку "wwwroot" проекта добавьте папку изображений и файл изображения. Например, можно использовать одно из изображений на странице [API компьютерного зрения](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Щелкните правой кнопкой одно из изображений и сохраните его на локальный жесткий диск, а затем в обозревателе решений щелкните правой кнопкой мыши папку изображений и выберите **Добавить** > **Существующий элемент**, чтобы добавить это изображение в проект. В обозревателе решений проект должен выглядеть примерно так. 
  
   ![Папка изображений с файлом изображения](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Щелкните правой кнопкой мыши файл изображения, выберите "Свойства", а затем выберите **Копировать, если новее**. 

   ![Копировать, если новее](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Метод Configure замените следующим кодом, чтобы получить доступ к API компьютерного зрения и протестировать изображение.

   ```csharp
        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site. 
            string imagePath = @"images/subway.png";

            // Enable static files such as image files. 
            app.UseStaticFiles();

            string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
            string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

            // Assemble the URI for the REST API Call.
            string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts an image you've added to your site's images folder. 
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentString = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentString = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
                await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentString));
                await context.Response.WriteAsync("<p>");
            });
        }

   ```
    Этот код создает HTTP-запрос с универсальным кодом ресурса (URI) и изображением в виде двоичного содержимого для вызова REST API компьютерного зрения.

1. Добавьте вспомогательные функции GetImageAsByteArray и JsonPrettyPrint.

   ```csharp
        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. Запустите веб-приложение и посмотрите, что API компьютерного зрения найдет в вашем изображении.

   ![Изображение и отформатированные результаты API компьютерного зрения](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этом будут удалены ресурсы Сognitive Service и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше об API компьютерного зрения из [документации по API компьютерного зрения](Home.md).
