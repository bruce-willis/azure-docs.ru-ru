---
title: Краткое руководство. Распознавание речи в приложении C# для .NET Core в Windows с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении C# для .NET Core в Windows с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: ee83443c76851506fffbfcaaa12e72790d077cb0
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128508"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C# для .NET Core в Windows с помощью пакета SDK для службы "Речь".

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Windows с помощью пакета SDK службы "Речь" в Cognitive Services создать консольное приложение C# для .NET Core, которое будет преобразовывать речь в текст.
Приложение основано на [пакете SDK NuGet для Microsoft Cognitive Services](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер Windows с рабочим микрофоном.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), выпуск Community Edition или более поздняя версия.
* Рабочая нагрузка **Кроссплатформенная разработка .NET Core.** в Visual Studio. Их можно включить, выбрав **Инструменты** \> **Добавить инструменты и компоненты**.

  ![Включение рабочей нагрузки "Кроссплатформенная разработка .NET Core"](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Создайте в Visual Studio 2017 консольное приложение .NET Core на Visual C#. В диалоговом окне **Создать проект** в левой области разверните элементы **Установлено** \> **Visual C#** \> **.NET Core** и выберите **Консольное приложение (.NET Core)**. В качестве имени проекта введите *helloworld*.

    ![Создание консольного приложения Visual C# (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Create Visual C# Console App (.NET Core)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Щелкните правой кнопкой мыши Управление пакетами NuGet для решения](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Установка пакета Nuget")

1. Примите условия отображаемой лицензии.

    ![Принять лицензию](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Принять лицензию")

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Сборка успешно выполнена](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Сборка успешно выполнена.")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Запуск отладки приложения](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Запуск отладки приложения")

1. Появится всплывающее окно консоли для речи (на английском языке). Распознанный текст будет отображен в этом же окне.

    ![Выходные данные консоли после успешного распознавания](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Выходные данные консоли после успешного распознавания")

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Дополнительная информация

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
