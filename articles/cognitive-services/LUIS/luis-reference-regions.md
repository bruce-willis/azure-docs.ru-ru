---
title: Регионы службы Language Understanding (LUIS) | Документы Майкрософт
titleSuffix: Azure
description: Эта статья содержит списки регионов LUIS для веб-сайта LUIS, подписок Azure и регионов мира.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36236839"
---
# <a name="regions-and-keys"></a>Регионы и ключи

Регион, в который публикуется приложение LUIS, соответствует региону или расположению, указываемому на портале Azure при создании ключа конечной точки LUIS Azure. Когда вы [публикуете приложение](./PublishApp.md), LUIS автоматически создает URL-адрес конечной точки для региона, связанного с ключом. Чтобы опубликовать приложение LUIS в несколько регионов, требуется по меньшей мере один ключ для каждого региона. 

## <a name="luis-website"></a>Веб-сайт LUIS
Существует три веб-сайта LUIS на основе региона. Разработку и публикацию следует выполнять в одном регионе. 

|LUIS|Регион|
|--|--|
|[www.luis.ai][www.luis.ai]|Данные учреждений<br>не Европа<br>не Австралия|
|[au.luis.ai][au.luis.ai]|Австралия|
|[eu.luis.ai][eu.luis.ai]|Европа|


## <a name="publishing-regions"></a>Регионы публикации

Приложения LUIS, созданные в https://www.luis.ai, могут быть опубликованы во всех конечных точках, за исключением [европейских](#publishing-to-europe) и [австралийских](#publishing-to-australia) регионов. 

Приложение региона разработки можно публиковать только в соответствующий регион публикации. Если приложение находится в неправильном регионе разработки, экспортируйте его, а затем импортируйте в правильный регион разработки для региона публикации. 

 Глобальный регион | Регион разработки | Регион публикации и запроса   |   Веб-сайт LUIS | Формат URL-адреса конечной точки   |
|-----|------|------|------|------|
| Азия | Запад США| Восточная Азия     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | Запад США| Юго-Восточная Азия     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Австралия](#publishing-to-australia) | Восточная часть Австралии| Восточная часть Австралии     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Европа](#publishing-to-europe)| Западная Европа| Северная Европа     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Европа](#publishing-to-europe) | Западная Европа| Западная Европа     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка | Запад США | Восток США      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | Запад США | Восток США 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | Запад США | Южно-центральный регион США     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка | Запад США | Западно-центральная часть США     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | Запад США | Запад США |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка | Запад США | Западный регион США 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка | Запад США | Южная часть Бразилии     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Публикация в Европе

Для публикации в европейские регионы приложения LUIS нужно создавать только по адресу https://eu.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в европейском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://eu.luis.ai. Приложения LUIS, созданные по адресу [https://eu.luis.ai][eu.luis.ai], не переносятся в другие регионы автоматически. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="publishing-to-australia"></a>Публикация в Австралии

Для публикации в австралийские регионы приложения LUIS нужно создавать только по адресу https://au.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в австралийском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://au.luis.ai. Приложения LUIS, созданные по адресу [https://au.luis.ai][au.luis.ai], не переносятся в другие регионы автоматически. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по предварительно созданным сущностям](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai