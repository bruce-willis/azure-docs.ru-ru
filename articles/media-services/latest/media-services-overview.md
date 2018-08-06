---
title: Общие сведения о Службах мультимедиа Azure версии 3 | Документация Майкрософт
description: В этой статье приводится обобщенный обзор Служб мультимедиа и содержатся ссылки на статьи для получения дополнительных сведений.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 07/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 6c3fb7391c25628ba12526a04c022215bdbd9d40
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325937"
---
# <a name="what-is-azure-media-services-v3"></a>Что такое Службы мультимедиа Azure версии 3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Версия 2 — общедоступная](../previous/media-services-overview.md)
> * [Версия 3 — предварительная](media-services-overview.md)

> [!NOTE]
> Последняя версия Служб мультимедиа Azure представлена в предварительной версии и может называться версией 3.

Службы мультимедиа Azure — это облачная платформа, которая позволяет создавать решения для потоковой передачи видео широковещательного уровня. Она повышает доступность и уровень распространения, дает возможность анализировать содержимое и предоставляет многие другие функции. Службы мультимедиа позволяют создавать приложения для обработки данных мультимедиа высокого качества для крупных аудиторий на самых популярных современных мобильных устройствах и браузерах для всевозможных сфер деятельности (разработчик приложения, центр обработки вызовов, государственное учреждение или компания, специализирующаяся в сфере индустрии развлечений). 

## <a name="what-can-i-do-with-media-services"></a>Какие функциональные возможности представлены в Службах мультимедиа?

Службы мультимедиа позволяют создавать различные рабочие процессы мультимедиа в облаке. Ниже приведены некоторые примеры действий, которые можно выполнить с помощью Служб мультимедиа.  

* Доставка видеосодержимого в различных форматах для воспроизведения на разных устройствах и в разных браузерах. Для доставки по требованию и доставки потоковой трансляции разным клиентам (мобильных устройств, ТВ, ПК и т. д.) видео- и аудиосодержимое необходимо зашифровать и упаковать соответствующим образом. Чтобы узнать, как выполнить доставку и потоковую передачу такого содержимого, см. статью [Quickstart: Stream video files — .NET](stream-files-dotnet-quickstart.md) (Краткое руководство по потоковой передаче видеофайлов (.NET)).
* Потоковая передача спортивных событий в прямой трансляции для большой аудитории в Интернете (например, футбол, бейсбол, спортивная жизнь в высших и средних учебных заведениях и многое другое). 
* Широковещательная передача открытых заседаний и событий (например, совещание в мэрии, городском совете и законодательных органах).
* Анализ записанного видео- или аудиосодержимого. Например, для достижения высшего качества обслуживания клиентов организации могут извлекать текст с помощью технологии преобразования речи в текст и создавать индексы поиска и панели мониторинга. Затем они могут извлекать аналитику по распространенным жалобам, источникам жалоб и другим связанным данным. 
* Создание видеослужбы подписки и потоковая передача содержимого, защищенного с помощью DRM, если необходимо ограничить доступ и использовать произведение, защищенное авторским правом клиента (например, для киностудии).
* Доставка автономного содержимого для воспроизведения на самолетах, в поездах и автомобилях. Возможно, пользователю потребуется загрузить содержимое на телефон или планшет для воспроизведения в автономном режиме (в случае отсутствия подключения к сети).
* Добавление субтитров и заголовков в видео для более широкой аудитории (например, для людей с нарушениями слуха или пользователей, которым во время просмотра необходимо одновременно читать титры на другом языке). 
* Реализация образовательной видеоплатформы электронного обучения со Службами мультимедиа Azure и [API-интерфейсами Cognitive Services Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) для ввода субтитров с помощью технологии преобразования речи в текст, перевода текста на несколько языков и т. д.
* Активация Azure CDN для достижения более высокого уровня масштабирования, который улучшает обработку кратковременных пиковых нагрузок (например, при запуске нового продукта). 

## <a name="v3-capabilities"></a>Возможности версии 3

Версия 3 создана на основе унифицированной области API, что позволяет использовать функции управления и операции, встроенные в Azure Resource Manager. 

Эта версия предоставляет следующие возможности.  

* **Преобразования** позволяют определить простые рабочие процессы обработки и аналитики мультимедиа. Преобразование — это способ обработки аудио- и видеофайлов. Вы можете применять эту функцию несколько раз для обработки всех файлов в библиотеке содержимого, отправляя задания на преобразование.
* **Задания** предназначены для обработки (шифрования или анализа) видеосодержимого. Содержимое входных данных можно указывать в задании с помощью URL-адресов протоколов HTTP, URL-адресов SAS или путей к файлам, находящимся в хранилище BLOB-объектов Azure. 
* **Уведомления**, отслеживающие ход выполнения задания или состояния, а также события включения, выключения и возникновения ошибок динамического канала. Уведомления интегрированы с системой уведомлений службы "Сетка событий Azure". Вы можете легко подписаться на события в нескольких ресурсах на платформе "Службы мультимедиа Azure". 
* Шаблоны **управления ресурсами Azure** можно использовать для создания и развертывания преобразований, конечных точек потоковой передачи, каналов и т. д.
* **Управление доступом на основе ролей** можно задать на уровне ресурсов, что позволит заблокировать доступ к определенным ресурсам (например, к преобразователям, каналам и т. д.).
* **Клиентские пакеты SDK** на нескольких языках: .NET, .NET Core, Python, Go, Java и Node.js.

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов. 

Имена ресурсов Служб мультимедиа не должны содержать следующие символы: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", символ единичной кавычки или любые управляющие символы. Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов. 

Дополнительные сведения об именовании Azure Resource Manager см. в разделе о [требованиях к именованию](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) и статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Принципы проектирования API Служб мультимедиа версии 3

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API версии 3 не возвращает секреты или учетные данные в **Get** или в операции **List**. Ключи всегда являются NULL, пустыми или исключенными из ответа. Необходимо вызвать отдельный метод действий для получения секретов или учетных данных. Отдельные действия позволяют устанавливать разные разрешения безопасности RBAC в случае, если некоторые API извлекают или отображают секреты, в то время как другие API этого не делают. Сведения о том, как управлять доступом с помощью RBAC, см. в разделе [Управление доступом с помощью RBAC и REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Примеры включений 

* невыполнение возврата значений ContentKey в запросе Get StreamingLocator, 
* невыполнение возврата ключей ограничения в запросе Get ContentKeyPolicy, 
* невыполнение возврата части строки запроса URL-адреса (чтобы удалить подпись) URL-адресов ввода заданий HTTP.

В следующем примере .NET показано, как получить ключ подписывания из существующей политики. Для получения ключа нужно использовать **GetPolicyPropertiesWithSecretsAsync**.

```csharp
private static async Task<ContentKeyPolicy> GetOrCreateContentKeyPolicyAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string contentKeyPolicyName)
{
    ContentKeyPolicy policy = await client.ContentKeyPolicies.GetAsync(resourceGroupName, accountName, contentKeyPolicyName);

    if (policy == null)
    {
        // Configure and create a new policy.
        
        . . . 
        policy = await client.ContentKeyPolicies.CreateOrUpdateAsync(resourceGroupName, accountName, contentKeyPolicyName, options);
    }
    else
    {
        var policyProperties = await client.ContentKeyPolicies.GetPolicyPropertiesWithSecretsAsync(resourceGroupName, accountName, contentKeyPolicyName);
        var restriction = policyProperties.Options[0].Restriction as ContentKeyPolicyTokenRestriction;
        if (restriction != null)
        {
            var signingKey = restriction.PrimaryVerificationKey as ContentKeyPolicySymmetricTokenKey;
            if (signingKey != null)
            {
                TokenSigningKey = signingKey.KeyValue;
            }
        }
    }

    return policy;
}
```

## <a name="how-can-i-get-started-with-v3"></a>Как начать работу с версией 3?

Как разработчик вы можете использовать [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) Служб мультимедиа или клиентских библиотек, которые позволяют взаимодействовать с REST API для простого создания, контроля и обслуживания настраиваемых рабочих процессов.  

Службы мультимедиа предоставляют [файлы Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media), которые можно использовать для создания пакетов SDK с использованием предпочтительного языка или технологии.  

Корпорация Майкрософт создает и поддерживает следующие клиентские библиотеки: 

|Справочники по API|Пакеты SDK и инструменты|Примеры|
|---|---|---|---|
|[Справочник по REST](https://aka.ms/ams-v3-rest-ref)|[Пакет SDK для REST](https://aka.ms/ams-v3-rest-sdk)|[Примеры REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman)<br/>[REST API на основе Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)|
|[Справочник по Azure CLI](https://aka.ms/ams-v3-cli-ref)|[интерфейс командной строки Azure](https://aka.ms/ams-v3-cli)|[Примеры использования интерфейса командной строки Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)||
|[Справочник по .NET](https://aka.ms/ams-v3-dotnet-ref)|[ПАКЕТ SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Примеры .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)||
||[Пакет SDK для .NET Core](https://aka.ms/ams-v3-dotnet-sdk) (выберите вкладку **.NET CLI**)|[Примеры .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)||
|[Справочник по Java](https://aka.ms/ams-v3-java-ref)|[Пакет SDK для Java](https://aka.ms/ams-v3-java-sdk)||
|[Справочник по Node.js](https://aka.ms/ams-v3-nodejs-ref)|[Пакет SDK для Node.js](https://aka.ms/ams-v3-nodejs-sdk)|[Примеры для Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)||
|[Справочник по Python](https://aka.ms/ams-v3-python-ref)|[Пакет SDK для Python](https://aka.ms/ams-v3-python-sdk)||
|[Справочник по Go](https://aka.ms/ams-v3-go-ref)|[Пакет SDK для GO](https://aka.ms/ams-v3-go-sdk)||

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как легко начать кодирование и потоковую передачу видеофайлов, ознакомьтесь со сведениями о [файлах потоковой передачи](stream-files-dotnet-quickstart.md). 

