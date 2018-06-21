---
title: Миграция из версии 2 в версию 3 Служб мультимедиа Azure | Документация Майкрософт
description: В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure, и приведены различия между двумя версиями.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660094"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Миграция из версии 2 в версию 3 Служб мультимедиа

> [!NOTE]
> Последняя версия Служб мультимедиа Azure представлена в предварительной версии и может называться версией 3.

В этой статье описаны изменения, которые впервые появились в версии 3 Служб мультимедиа Azure (AMS), и приведены различия между двумя версиями.

## <a name="why-should-you-move-to-v3"></a>Почему следует выполнить переход на версию 3

### <a name="api-is-more-approachable"></a>Более доступный API

*  Версия 3 создана на основе унифицированной области API, что позволяет использовать функции управления и операции, встроенные в Azure Resource Manager. Шаблоны Azure Resource Manager можно использовать для создания и развертывания преобразований, конечных точек потоковой передачи, событий прямой трансляции и т. д.
* Документ спецификации Open API (также называемый Swagger).
* Пакеты SDK для .Net, .Net Core, Node.js, Python, Java, Ruby.
* Интеграция Azure CLI.

### <a name="new-features"></a>новые функции;

* Теперь кодирование поддерживает прием HTTPS (ввод на основе URL-адреса).
* Преобразования появились в версии 3. Преобразование используется для совместного доступа к конфигурациям, создания шаблонов Azure Resource Manager и изолирования параметров кодирования определенного пользователя или клиента. 
* Ресурс может иметь несколько StreamingLocators с различными параметрами динамической упаковки и динамического шифрования.
* Защита содержимого поддерживает несколько ключевых возможностей. 
* Предварительная версия LiveEvent поддерживает динамическую упаковку и динамическое шифрование. Это включает защиту содержимого в предварительной версии, а также упаковки DASH и HLS.
* LiveOuput проще в использовании, чем старая сущность Program. 
* Была добавлена поддержка RBAC в сущностях.

## <a name="changes-from-v2"></a>Отличия от версии 2

* Пакеты SDK для Служб мультимедиа лишаются хранилища SDK, что обеспечивает больший контроль над используемым хранилищем SDK и позволяет избежать проблем с управлением версиями. 
* В версии 3 вся скорость кодировки указана в битах за секунду. Это отличается от предустановок Media Encoder Standard REST версии 2. Например, скорость в версии 2 указывается как 128, а в версии 3 она имела бы значение 128 000. 
* Объекты AssetFiles, AccessPolicies, IngestManifests не существуют в версии 3.
* ContentKeys больше не сущность, свойство StreamingLocator.
* Служба "Сетка событий" заменяет NotificationEndpoints.
* Некоторые сущности были переименованы.

  * JobOutput заменяет Task, теперь это только часть Job.
  * LiveEvent заменяет Channel.
  * LiveOutput заменяет Program.
  * StreamingLocator заменяет Locator.

## <a name="code-changes"></a>Изменения в коде

### <a name="create-an-asset-and-upload-a-file"></a>Создайте ресурс и отправьте файл. 

#### <a name="v2"></a>версия 2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>версия 3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Отправка задания

#### <a name="v2"></a>версия 2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>версия 3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Опубликуйте ресурс с шифрованием AES. 

#### <a name="v2"></a>версия 2

1. Создайте ContentKeyAuthorizationPolicyOption.
2. Создайте ContentKeyAuthorizationPolicy.
3. Создайте AssetDeliveryPolicy.
4. Создайте ресурс и передайте содержимое или отправьте задание и используйте выходной ресурс.
5. Свяжите AssetDeliveryPolicy с ресурсом.
6. Создайте ContentKey.
7. Присоедините ContentKey к Asset.
8. Создайте AccessPolicy.
9. Создайте Locator.

#### <a name="v3"></a>версия 3

1. Создайте политику ключа содержимого.
2. Создайте ресурс.
3. Передайте содержимое или используйте ресурс как JobOutput.
4. Создайте Locator.

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как легко начать кодирование и потоковую передачу видеофайлов, ознакомьтесь со сведениями о [файлах потоковой передачи](stream-files-dotnet-quickstart.md). 

