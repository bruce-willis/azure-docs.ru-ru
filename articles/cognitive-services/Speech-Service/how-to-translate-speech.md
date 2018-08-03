---
title: Перевод речи с помощью служб речи | Документы Майкрософт
description: Сведения о переводе речи в службе "Речь".
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6acfcf0ae8ab4c63e6cc943f93da6b947f3d118c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071102"
---
# <a name="translate-speech-using-speech-service"></a>Перевод речи с помощью службы речи

[Пакет SDK для службы речи](speech-sdk.md) — самый простой способ использования перевода речи в приложении. Пакет SDK обеспечивает все функции службы. Базовый процесс выполнения перевода речи включает следующие шаги.

1. Создайте фабрику речи и укажите ключ подписки и [регион](regions.md) службы "Речь" или маркер авторизации. Кроме того, на этом этапе можно настроить исходный и целевой языки перевода, а также указать тип вывода: текст или речь.

2. Получите распознаватель из фабрики. Для перевода выберите распознаватель перевода. (Остальные распознаватели предназначены для *преобразования речи в текст*.) Существуют различные разновидности распознавателей перевода в зависимости от используемого источника звука.

4. При необходимости свяжите события для асинхронной операции. Распознаватель вызывает обработчики событий при получении промежуточного и конечного результатов. В противном случае приложение получает конечный результат перевода.

5. Начните распознавание и перевод.

# <a name="sdk-samples"></a>Примеры пакета SDK

Актуальный набор примеров доступен в [репозитории GitHub с примерами для пакета SDK службы "Речь" в Cognitive Services](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Дополнительная информация

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Краткое руководство. Распознавание речи с помощью пакета SDK службы распознавания речи Cognitive Services C#](quickstart-csharp-dotnet-windows.md)
