---
title: Что такое API Bing для поиска изображений? | Документация Майкрософт
description: Узнайте, как с помощью API Bing искать изображения в Интернете.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.openlocfilehash: ebf8c28b843768a9081a0086b10e4adf04572ea1
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42889257"
---
# <a name="what-is-bing-image-search"></a>Что такое API Bing для поиска изображений?

API Bing для поиска изображений позволяет использовать возможности когнитивного поиска изображений Bing в своем приложении. Отправляя пользовательские поисковые запросы с помощью API, можно получить и отобразить соответствующие и высококачественные изображения, которые аналогичны [изображениям Bing](https://www.bing.com/images).

Имейте в виду, что API Bing для поиска изображений предоставляет только результаты поиска изображений. Используйте [API Bing для поиска в Интернете](../bing-web-search/search-the-web.md), [API для поиска видео](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) и [API Bing для поиска новостей](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search) для других типов веб-содержимого.

## <a name="bing-image-search-features"></a>Возможности API Bing для поиска изображений

Хотя API Bing для поиска изображений в основном находит и возвращает соответствующие изображения на основе поискового запроса, эта служба также предоставляет несколько дополнительных функций для интеллектуального и специализированного извлечения изображений в Интернете.


| Функция                                                                                                                                                                                 | ОПИСАНИЕ                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Предложение условий поиска в режиме реального времени](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#using-and-suggesting-search-terms) | Улучшите работу приложения, используя [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), отображающий предлагаемые условия поиска по мере их ввода. |
| [Фильтрация и ограничение результатов поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | Отфильтруйте возвращаемые Bing изображения путем изменения параметров запроса.                                                                                                       |
| [Обрезка, изменения размера и отображение эскизов](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Изменяйте и отображайте предварительные версии эскизов для изображений, возвращаемых API Bing для поиска изображений.                                                                                      |
| [Сводка и расширение пользовательских поисковых запросов](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | Расширьте возможности поиска путем включения и отображения в запросах предлагаемых Bing условий поиска.                                                                    |
| [Получение изображений, набирающих популярность](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Настройте поиск популярных изображений со всего мира.                                                                                                          |

## <a name="workflow"></a>Рабочий процесс

API Bing для поиска изображений является веб-службой RESTful, которую легко вызвать с любого языка программирования, поддерживающего выполнение HTTP-запросов и анализ JSON. Вы можете использовать службу с помощью [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) или [пакета SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с доступом к API-интерфейсам поиска Bing. Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). 
2. Отправьте запрос к API с допустимым [поисковым запросом](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Обработайте ответ API путем анализа возвращенного сообщения JSON. 

## <a name="next-steps"></a>Дополнительная информация

Сначала попробуйте [интерактивную демоверсию](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) API Bing для поиска изображений.
В этой демонстрации показано, как можно быстро настроить поисковый запрос и найти изображение в Интернете.

Когда будете готовы вызвать API, создайте [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Если у вас нет подписки Azure, создайте бесплатную [учетную запись](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Чтобы быстро начать работу со своим первым запросом API, научитесь:

* [отправлять поисковые запросы в Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) с помощью REST API или
* [запрашивать и фильтровать](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) возвращаемые Bing изображения с помощью пакета SDK.

## <a name="see-also"></a>См. также

* Раздел справочника по [API Bing для поиска изображений версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) содержит определения и сведения о конечных точках, заголовках, ответах API и параметрах запроса, которые можно использовать для запроса результатов поиска на основе изображения.

* В статье [Требования к использованию и отображению API поиска Bing](./useanddisplayrequirements.md) рассматриваются приемлемые варианты использования содержимого и информации, получаемой с помощью API Bing для поиска.

* В статье [Получение изображений из Интернета с помощью API Bing для поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) показано, как выполнять поиск и получать изображения из Интернета.

* В статье [Отправка запросов в API Bing для поиска изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) приведены сведения о том, как создавать, настраивать и сводить поисковые запросы. 
