---
title: Пакеты SDK и примеры для Azure Content Moderator | Документация Майкрософт
description: Получение пакетов SDK и примеров для Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380249"
---
# <a name="content-moderator-sdks-and-samples"></a>Пакеты SDK и примеры для Content Moderator

## <a name="sdks-for-python-java-nodejs-and-net"></a>Пакеты SDK для Python, Java, Node.js и .NET

- [Пакет SDK Content Moderator для Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Пакет SDK Content Moderator для Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Пакет SDK Content Moderator для Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Пакет SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Примеры пакета SDK для .NET

Ниже приведен список ссылок на примеры кода, созданные с помощью пакета SDK Azure Content Moderator для .NET.

- **Вспомогательная библиотека**: [создание клиента Content Moderator для использования в других примерах](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). См. [краткое руководство](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Модерация 
- **Модерация изображений**: [оценка изображения на наличие содержимого для взрослых, непристойного содержимого, текста и лиц](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). См. [краткое руководство](image-moderation-quickstart-dotnet.md).
- **Пользовательские изображения**: [модерация с помощью настраиваемых списков изображений](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). См. [краткое руководство](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Действует ограничение в **5 списков изображений**, в каждом из которых может быть **не более 10 000 изображений**.
>

- **Модерация текста**: [проверка текста на наличие нецензурных выражений и личных сведений](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). См. [краткое руководство](text-moderation-quickstart-dotnet.md).
- **Настраиваемые термины**: [модерация с помощью настраиваемых списков терминов](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). См. [краткое руководство](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Действует ограничение лимит в **5 списков терминов**, в каждом из которых может быть **не более 10 000 терминов**.
>

- **Модерация видео**: [проверка видео на наличие содержимого для взрослых и нецензурного содержимого и получение результатов](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). См. [краткое руководство](video-moderation-api.md).

### <a name="review"></a>Проверка
- **Задания модерации изображений**: [запуск задания модерации, проверяющего содержимое и создающего проверки](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). См. [краткое руководство](moderation-jobs-quickstart-dotnet.md).
- **Проверки изображений**: [создание проверок для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). См. [краткое руководство](moderation-reviews-quickstart-dotnet.md).
- **Проверки видео**: [создание проверок видео для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). См. [краткое руководство](video-reviews-quickstart-dotnet.md).
- **Проверки расшифровок видео**: [создание проверок расшифровок видео для рассмотрения человеком](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). См. [краткое руководство](video-reviews-quickstart-dotnet.md).

Ознакомьтесь со всеми примерами для .NET на странице [примеров Content Moderator для .NET на сайте GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Примеры для REST API на C#

- [Модерация изображений](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Модерация текста](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Модерация видео](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Проверка изображений](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Задания модерации изображений](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Пошаговые руководства по этим примерам представлены в [вебинаре по запросу](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Учебники
- [Модерация каталога электронной коммерции](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) См. это [руководство](ecommerce-retail-catalog-moderation.md).
- [Модерация контента Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration) См. это [руководство](facebook-post-moderation.md).
- [Модерация видео и расшифровок и решение для проверки](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp). См. это [руководство](video-transcript-moderation-review-tutorial-dotnet.md).

## <a name="on-demand-webinars"></a>Вебинары по запросу
- [Масштабная машинная модерация контента с помощью Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
