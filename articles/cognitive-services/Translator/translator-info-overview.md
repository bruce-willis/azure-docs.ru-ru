---
title: Обзор API перевода текстов Microsoft Translator — Azure Cognitive Services | Документация Майкрософт
description: Интеграция API перевода текстов Microsoft Translator в приложения, веб-сайты, инструменты и другие решения для обеспечения многоязычного взаимодействия с пользователем.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: bfbb316ac41045add7f424b5d478581aa226fc19
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42889308"
---
# <a name="what-is-microsoft-translator"></a>Что такое Microsoft Translator?

API перевода текстов Microsoft Translator легко интегрируется в приложения, веб-сайты, инструменты и другие решения, обеспечивая взаимодействие с пользователем [более чем на 60 языках](languages.md). Этот программный интерфейс выполняет перевод текста и поддерживается на любой аппаратной платформе и в любой операционной системе.

API перевода текстов Microsoft Translator является частью коллекции алгоритмов машинного обучения и ИИ [API Microsoft Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) в облаке, готовых к использованию в проектах по разработке.

## <a name="about-microsoft-translator"></a>О Microsoft Translator

Майкрософт Translator — это облачная служба автоматического перевода. В основе этой службы лежит API перевода текстов и [API перевода речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation), которые используют различные продукты и услуги корпорации Майкрософт и применяются тысячами компаний по всему миру в своих приложениях и рабочих процессах, что позволяет их содержимому достигать всемирной аудитории.

Функция перевода речи также доступна в [предварительной версии службы "Речь" Cognitive Services](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/), которая объединяет имеющиеся API перевода речи, API распознавания речи Bing и пользовательскую службу распознавания речи (предварительная версия) в единую и полностью настраиваемую службу.  

Дополнительные сведения о [службе Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>Настройка языка

Расширение основной службы Microsoft Translator, Custom Translator в сочетании с API перевода текстов помогут вам настроить систему нейронного перевода и улучшить перевод с учетом конкретной терминологии и стиля.

Custom Translator позволяет создавать системы перевода, которые распознают особую терминологию вашего бизнеса и отрасли. Вашу настраиваемую систему перевода будет легко интегрировать в имеющиеся приложения, рабочие процессы и веб-сайты на нескольких типах устройств через обычный API перевода текстов Microsoft Translator, используя параметр "Категория". 

Дополнительные сведения о [настройке языков](customization.md).

## <a name="microsoft-translator-neural-machine-translation"></a>Нейронный машинный перевод в Microsoft Translator

Нейронный машинный перевод (NMT) — это новый стандарт высококачественных машинных переводов на базе ИИ. Он заменяет устаревшую технологию статистического машинного перевода (SMT), которая достигла вершины качества в середине 2010-х годов.

NMT обеспечивает лучшие переводы, чем SMT, не только с точки зрения оценки качества перевода, но и потому, что они будут звучать более свободно и естественно. Секрет такого улучшения кроется в том, что технология NMT использует для перевода слов контекст всего предложения. SMT принимал непосредственный контекст нескольких слов до и после каждого слова.

Модели NMT лежат в основе API и не видны пользователям. Единственным заметным отличием является улучшение качества перевода, особенно для таких языков, как китайский, японский и арабский. 

Подробнее о [принципах работы NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с информацией о [ценах](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Зарегистрируйтесь](translator-text-how-to-signup.md) для получения ключа доступа.

- Просмотрите [краткое руководство](quickstarts/csharp.md). Это пошаговое руководство по вызовам REST API на C#. Узнайте, как переводить текст с одного языка на другой с минимальным кодом.

- Ознакомьтесь со [справочной технической документацией по API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference).

## <a name="see-also"></a>См. также

- [Страница документации по службам Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Страница продукта Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Решение и цены](https://www.microsoft.com/en-us/translator/default.aspx)
