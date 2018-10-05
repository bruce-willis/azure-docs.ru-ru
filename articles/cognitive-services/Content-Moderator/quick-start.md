---
title: Начало работы с Content Moderator
titlesuffix: Azure Cognitive Services
description: Приступая к работе с Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225300"
---
# <a name="get-started-with-content-moderator"></a>Начало работы с Content Moderator

У вас есть несколько возможных путей для начала работы с Content Moderator:

- [Используйте средство проверки](#start-with-the-review-tool), чтобы получить ключ API и создать команду проверки. Преимущество заключается в том, что ключ API можно использовать, чтобы вызывать API-интерфейсы модерации для сканирования содержимого и API проверки для создания проверок без дополнительных действий.
- [Подпишитесь на Content Moderator](#start-with-the-apis) в Azure, чтобы получить ключ API. Ознакомьтесь со [справочником по API](api-reference.md) и [пакетами SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). При этом для создания команды проверки по-прежнему потребуется онлайн-регистрация.
- [Примените соединитель и шаблоны для Microsoft Flow](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/), чтобы изучить широкий диапазон средств интеграции с помощью удобного конструктора.

Независимо от выбранного варианта, ознакомьтесь со статьей [об управлении учетными данными](review-tool-user-guide/credentials.md), чтобы узнать учетные данные для API.

## <a name="start-with-the-review-tool"></a>Начало работы со средством проверки
[Зарегистрируйтесь](http://contentmoderator.cognitive.microsoft.com/) на веб-сайте средства проверки Content Moderator.

![Домашняя страница сайта Content Moderator](images/homepage.PNG)

### <a name="create-a-review-team"></a>Создание команды проверки
Присвойте имя вашей команде. Если вы хотите пригласить коллег, введите их адреса электронной почты.

![Приглашение участников команды](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Передача изображений или ввод текста
Щелкните **Проверить > Изображение** или **Проверить > Текст**. Отправить до пяти примеров изображений или введите образец текста для модерирования.

![Проверка модерации для изображений или текста](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Отправка на автоматическую модерацию
Отправьте контент для автоматической модерации. Средство проверки через встроенные механизмы вызывает API-интерфейсы модерации для проверки содержимого. Когда сканирование завершится, вы получите сообщение о результатах, которые нужно просмотреть.

![Модерация файлов](images/submitted.png)

### <a name="review-and-confirm-results"></a>Проверка и подтверждение результатов
Просмотрите теги, присвоенные автоматической модерацией, измените их, если потребуется, и подтвердите с помощью кнопки **Далее**. Когда бизнес-приложение вызывает API Content Moderator, содержимое с присвоенными тегами ставится в очередь для рассмотрения сотрудниками из команды проверки. Такой подход позволяет быстро просмотреть большие объемы содержимого.

![просмотрите результаты;](images/reviewresults.png)

Узнайте, как использовать все [возможности средства проверки](Review-Tool-User-Guide/human-in-the-loop.md) или переходите к следующему разделу с информацией об API-интерфейсах. Пропустите шаг регистрации, так как у вас уже есть ключ API, подготовленный в средстве проверки при изучении статьи [об управлении учетными данными](review-tool-user-guide/credentials.md).

### <a name="use-the-apis"></a>Использование API-интерфейсов

Узнайте, как интегрировать Content Moderator с бизнес-приложениями. Ознакомьтесь со [справочником по API](api-reference.md) и [пакетами SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Оформление подписки на портале Azure

[Подпишитесь на Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) на портале Azure. Начните работу с любого из следующих API-интерфейсов.

### <a name="image-moderation"></a>Модерация изображений

Откройте [консоль API](try-image-api.md) или [краткое руководство по .NET](image-moderation-quickstart-dotnet.md), чтобы проверить изображения на наличие материалов для взрослых и (или) непристойного характера с использованием тегов, оценки достоверности и других сведений, извлекаемых из содержимого.

### <a name="text-moderation"></a>Модерация текста

Откройте [консоль API](try-text-api.md) или [краткое руководство по .NET](text-moderation-quickstart-dotnet.md), чтобы проверить текстовое содержимое на наличие ненормативной лексики, машинной классификации нежелательных текстов (предварительная версия) и персональных данных. 


### <a name="video-moderation"></a>Модерация видео

Изучите [краткое руководство по .NET](video-moderation-api.md), чтобы проверить видео на наличие материалов для взрослых и (или) содержимого непристойного характера. 


### <a name="review-apis"></a>Просмотр API-интерфейсов

Для начала выберите API заданий, проверки или рабочих процессов.

- [API заданий](try-review-api-job.md) проверяет содержимое, используя API модерации, и создает отчеты в средстве проверки. 
- [API проверки](try-review-api-review.md) создает отчеты об изображениях, текстах или видео для подтверждения модераторами без предварительного сканирования содержимого. 
- [API рабочих процессов](try-review-api-workflow.md) создает, обновляет пользовательские рабочие процессы вашей команды и получает сведения о них.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь со [справочником по API](api-reference.md) и [пакетами SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Ускорьте процесс интеграции, используя [примеры пакета SDK для .NET](sdk-and-samples.md#net-sdk-samples), [примеры REST API на C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) и [руководства](sdk-and-samples.md#tutorials).
