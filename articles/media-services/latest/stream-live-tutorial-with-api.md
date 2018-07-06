---
title: Потоковая трансляция с помощью Служб мультимедиа Azure v3 и .NET Core | Документация Майкрософт
description: В этом руководстве описано, как настроить потоковую трансляцию с помощью Служб мультимедиа Azure v3 и .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: 82ef04993b597778c808d649032603a0f3672e4c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110336"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Потоковая трансляция с помощью Служб мультимедиа Azure v3 и .NET Core

В Службах мультимедиа [События прямой трансляции](https://docs.microsoft.com/rest/api/media/liveevents) отвечают за обработку содержимого потоковой трансляции. Событие прямой трансляции предоставляет входную конечную точку (URL-адрес входа), которую вы можете передать динамическому кодировщику. Событие прямой трансляции получает от кодировщика входные потоки трансляции и предоставляет их для потоковой передачи через одну или несколько [конечных точек потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints). Также события прямой трансляции предоставляют конечную точку предварительного просмотра (URL-адрес предварительного просмотра), которая позволяет просмотреть и проверить поток перед его обработкой и доставкой. В этом руководстве описано, как с помощью .NET Core создавать события прямой трансляции **сквозного** типа. 

> [!NOTE]
> Прежде чем продолжить работу, ознакомьтесь со статьей [Live streaming with Azure Media Services v3](live-streaming-overview.md) (Потоковая трансляция в Службах мультимедиа Azure v3). 

В этом руководстве описаны следующие процедуры.    

> [!div class="checklist"]
> * Создание учетной записи служб мультимедиа
> * Доступ к API Служб мультимедиа.
> * Настройка примера приложения
> * Проверка кода, который выполняет потоковую трансляцию
> * Просмотр событий с помощью [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) в http://ampdemo.azureedge.net
> * Очистка ресурсов

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Ниже перечислены необходимые условия для выполнения действий, описанных в этом учебнике.

* Установка Visual Studio Code или Visual Studio.
* Веб-камера или другое устройстве (например, ноутбук) для потоковой трансляции события.
* Локальный динамический кодировщик, который преобразует сигналы от камеры в потоки и отправляет их службе потоковой трансляции в Службах мультимедиа. Поток должен иметь формат **RTMP** или **Smooth Streaming**.

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий GitHub, содержащий пример потоковой передачи данных .NET, на компьютер с помощью следующей команды:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Пример потоковой трансляции размещен в папке [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> Этот пример использует уникальный суффикс для каждого ресурса. Если вы отмените отладку или завершите приложение раньше, чем оно закончит свою работу, в вашей учетной записи сохранятся несколько событий прямой трансляции. <br/>
> Не забудьте остановить все запущенные события прямой трансляции. В противном случае за них будет **начислена плата**!

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Проверка кода, который выполняет потоковую трансляцию

В этом разделе описаны функции, определенные в файле [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) проекта *MediaV3LiveApp*.

Этот пример создает уникальный суффикс для каждого ресурса, чтобы избежать конфликтов имен при многократном запуске примера без очистки.

> [!IMPORTANT]
> Этот пример использует уникальный суффикс для каждого ресурса. Если вы отмените отладку или завершите приложение раньше, чем оно закончит свою работу, в вашей учетной записи сохранятся несколько событий прямой трансляции. <br/>
> Не забудьте остановить все запущенные события прямой трансляции. В противном случае за них будет **начислена плата**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Начало использования API Служб мультимедиа с пакетом SDK для .NET

Чтобы начать использование API Служб мультимедиа с .NET, создайте объект **AzureMediaServicesClient**. Чтобы создать объект, введите учетные данные, необходимые клиенту для подключения к Azure с помощью Azure AD. В коде, который вы клонировали в начале статьи, функция **GetCredentialsAsync** создает объект ServiceClientCredentials с использованием учетных данных, предоставленных в локальном файле конфигурации.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Создание события прямой трансляции

В этом разделе показано, как создать событие прямой трансляции **сквозного** типа (для которого параметр LiveEventEncodingType имеет значение None). Если вы хотите создать событие прямой трансляции, поддерживающее кодирование в реальном времени, задайте для LiveEventEncodingType значение Basic. 

При создании событий прямой трансляции также можно настроить следующее:

* Расположение Служб мультимедиа. 
* Протокол потоковой передачи для событий прямой трансляции (сейчас поддерживаются протоколы RTMP и Smooth Streaming).
       
    Изменить протокол во время работы события прямой трансляции или связанных с ним выходов прямой трансляции нельзя. Если вам нужны другие протоколы, создайте отдельное событие потоковой трансляции для каждого из них.  
* Ограничения по IP-адресам для приема и предварительного просмотра. Вы можете определить IP-адреса, с которых событие потоковой трансляции будет принимать видео. Можно указать отдельный допустимый IP-адрес (например, 10.0.0.1), или диапазон IP-адресов, используя IP-адрес и маску подсети CIDR (например, 10.0.0.1/22), или IP-адрес и маску подсети с точками (например, 10.0.0.1(255.255.252.0)).
    
    Если IP-адреса не указаны и правила не заданы, ни один IP-адрес не разрешен. Чтобы разрешить все IP-адреса, создайте правило и задайте адрес 0.0.0.0/0.

Для создаваемого события можно настроить автоматический запуск. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>Получение URL-адресов приема

После создания канала можно получить URL-адреса приема, которые необходимо передать динамическому кодировщику. Он использует эти адреса для передачи динамического потока на вход.


```csharp
// Get the input endpoint to configure the on-premises encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on-premises encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Получение URL-адреса предварительного просмотра

Используйте конечную точку предварительного просмотра, чтобы проверить получение входных данных от кодировщика.

> [!IMPORTANT]
> Прежде, чем продолжить, убедитесь, что видео правильно передается на URL-адрес предварительного просмотра.

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Создание событий и выходов прямой трансляции и управление ими

Настроив передачу потока данных через событие прямой трансляции, вы можете запустить событие потоковой передачи, создав ресурс, выход прямой трансляции и указатель потоковой передачи. Так вы сможете запустить архивирование потока и сделать его доступным его зрителям через конечную точку потоковой передачи. 

#### <a name="create-an-asset"></a>Создание ресурса

Создайте ресурс, который будет использоваться в выходе прямой трансляции.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>Создание выхода прямой трансляции

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>Создание указателя потоковой передачи

> [!NOTE]
> При создании учетной записи служб мультимедиа в нее добавляется конечная точка потоковой передачи **по умолчанию** в состоянии **Остановлена**. Чтобы начать потоковую передачу содержимого и воспользоваться динамической упаковкой и динамическим шифрованием, конечная точка потоковой передачи, из которой необходимо выполнять потоковую передачу содержимого, должна находиться в состоянии **Выполняется**. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Очистка ресурсов в учетной записи Служб мультимедиа

После завершения потоковой передачи мероприятия вы можете удалить выделенные ранее ресурсы с помощью описанной ниже процедуры.

* Остановите трансляцию потока из кодировщика.
* Остановите событие прямой трансляции. Плата за остановленное событие прямой трансляции не взимается. Если вам понадобится снова запустить его, вы можете воспользоваться тем же URL-адресом приема (перенастраивать кодировщик не потребуется).
* Вы можете остановить конечную точку потоковой передачи, если не хотите предоставлять доступ к архиву события прямой трансляции по требованию. Плата за остановленное событие прямой трансляции не взимается.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Просмотр события

Чтобы просмотреть событие, скопируйте URL-адрес потоковой передачи, полученный при выполнении кода в разделе о [создании указателя потоковой передачи](#create-a-streaminglocator) и вставьте его в любой проигрыватель. Для проверки потока на сайте http://ampdemo.azureedge.net можно использовать [Проигрыватель мультимедиа Azure](http://amp.azure.net/libs/amp/latest/docs/index.html). 

После остановки интерактивное событие автоматически преобразуется в содержимое по требованию. Даже после остановки и удаления события пользователи смогут запрашивать потоковую передачу архивированного видеосодержимого, пока не удален соответствующий ресурс. Ресурс невозможно удалить, пока он используется каким-либо событием: сначала нужно удалить это событие. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая Службы мультимедиа и учетные записи хранения, созданные для этого руководства, удалите группу ресурсов, созданную ранее. Для этого можно использовать средство **CloudShell**.

В **CloudShell** выполните следующую команду:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> За запущенное событие прямой трансляции взимается плата. Учтите также, что при сбое или остановке проекта и (или) программы по любой причине, событие прямой трансляции может остаться в работающем состоянии, за что также будет взиматься плата.

## <a name="next-steps"></a>Дополнительная информация

[Потоковая передача файлов](stream-files-tutorial-with-api.md)

