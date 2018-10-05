---
title: Обзор API языковой модели на основе веб-содержимого в Azure Cognitive Services | Документация Майкрософт
description: API языковой модели на основе веб-содержимого в Microsoft Cognitive Services предоставляет современные инструментальные средства для обработки естественного языка.
services: cognitive-services
author: piyushbehre
manager: yanbo
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ms.openlocfilehash: dc5dc0519e33e024014033ac5260004482b419c2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096961"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Что такое API языковой модели на основе веб-содержимого? (предварительная версия)

API языковой модели на основе веб-содержимого Microsoft — это облачная служба на основе REST, которая предоставляет современные инструментальные средства для обработки естественного языка. Этот API позволяет вашему приложению использовать потенциал больших данных с помощью языковых моделей, обученных с помощью веб-данных, собранных Bing на рынке en-US (английский, США).

Эти сглаженные вероятностные языковые модели с использованием N-граммы, которые поддерживают до 5 шагов цепи Маркова, обучены на основании следующих единиц данных:

- основного текста веб-страницы;
- заголовка веб-страницы;
- текста привязки веб-страницы;
- текста запроса веб-поиска.

API веб-модели языка поддерживают четыре операции поиска.

1. Совместная (log10) вероятность последовательности слов.
2. Условная (log10) вероятность одного слова в заданной последовательности предыдущих слов.
3. Список слов (завершений), скорее всего, для того чтобы следовать определенной последовательности слов.
4. Разбиение слов строк, которые не содержат пробелы.

## <a name="getting-started"></a>Приступая к работе

1. Оформите подписку на службу.
2. Скачайте [пакет SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Запустите пример кода пакета SDK.
4. Подробные сведения о конечных точках, включая фрагменты кода в разных языках, см. в [Справочнике по API](https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104).

## <a name="underlying-technology"></a>Основная технология

Следующий документ содержит сведения о разработке эти языковых моделей и на него следует ссылаться в научных публикациях, которые используют эту услугу.

- [An Overview of Microsoft Web N-gram Corpus and Applications](http://research.microsoft.com/apps/pubs/default.aspx?id=130762) (Обзор веб-данных N-граммы и приложений Microsoft), NAACL-HLT 2010

Нажмите [здесь](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0), чтобы увидеть текущий список документов, ссылающихся на этот текст.
