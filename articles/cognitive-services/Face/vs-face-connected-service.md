---
title: Руководство по использованию API распознавания лиц для C# | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Создание простого приложения Windows, которое использует API распознавания лиц Cognitive Services для обнаружения особенностей лиц на изображении.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: b51760f889db27aa25e54582070ee7d3adcf66f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38663892"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Подключение к API распознавания лиц Cognitive Services с помощью Подключенных служб в Visual Studio

С помощью API распознавания лиц Cognitive Services можно обнаруживать, анализировать, систематизировать и помечать лица на фотографиях.

В этой статье и других статьях этой серии приводятся сведения об использовании компонента Подключенной службы Visual Studio для API распознавания лиц Cognitive Services. Эта возможность доступна как в Visual Studio 2017 15.7, так и в более поздней версии, с установленным расширением Cognitive Services.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 версии 15.7** с установленной рабочей нагрузкой **Веб-разработка**. [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Создание проекта и добавление поддержки для API распознавания лиц Cognitive Services

1. Создайте новый веб-проект ASP.NET Core. Используйте шаблон пустого проекта. 

1. В **обозревателе решений** выберите **Добавить** > **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.

   ![Добавление пункта меню Подключенной службы](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. В меню доступных служб выберите **API распознавания лиц Cognitive Services**.

   ![Выбор службы для подключения](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок.

   ![Выберите свою подписку.](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Выберите подписку, которую необходимо использовать, придумайте имя для API распознавания лиц или выберите ссылку "Изменить", чтобы изменить автоматически созданное имя, а затем выберите группу ресурсов и ценовую категорию.

   ![Изменение сведений о подключенной службе](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Для получения дополнительных сведений о ценовых категориях перейдите по ссылке.

1. Щелкните "Добавить", чтобы добавить поддержку для подключенной службы.
   Visual Studio модифицирует проект, чтобы добавить пакеты NuGet, записи файла конфигурации и другие изменения для поддержки подключения API распознавания лиц.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Использование API распознавания лиц для обнаружения атрибутов лиц на изображении

1. Добавьте следующие инструкции в Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Добавьте поле конфигурации, а также конструктор, который инициализирует поле конфигурации в классе Startup, чтобы включить конфигурацию в программе.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. В папку "wwwroot" проекта добавьте папку изображений и файл изображения. Например, можно использовать одно из изображений на странице [API распознавания лиц](https://azure.microsoft.com/services/cognitive-services/face/). Щелкните правой кнопкой одно из изображений и сохраните его на локальный жесткий диск, а затем в обозревателе решений щелкните правой кнопкой мыши папку изображений и выберите **Добавить** > **Существующий элемент**, чтобы добавить это изображение в проект. В обозревателе решений проект должен выглядеть примерно так.
 
   ![Папка изображений с файлом изображения](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Щелкните правой кнопкой мыши файл изображения, выберите "Свойства", а затем выберите **Копировать, если новее**.

   ![Копировать, если новее](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Метод Configure замените следующим кодом, чтобы получить доступ к API распознавания лиц и протестировать изображение. Замените строку imagePath на правильный путь и имя файла для изображения лица.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    На этом шаге код создает HTTP-запрос с вызовом REST API распознавания лиц с помощью ключа, полученного при добавлении подключенной службы.

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

1. Запустите веб-приложение и посмотрите, что будет найдено в изображении с помощью API распознавания лиц.
 
   ![Изображение и отформатированные результаты API распознавания лиц](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этом будут удалены ресурсы Сognitive Service и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
1. Выберите **Удалить группу ресурсов**.
1. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об API распознавания лиц см. в разделе [Что собой представляет API распознавания лиц](Overview.md).
