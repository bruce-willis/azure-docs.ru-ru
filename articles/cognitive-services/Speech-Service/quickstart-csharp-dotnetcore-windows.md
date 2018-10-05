---
title: Краткое руководство. Распознавание речи в приложении C# для .NET Core в Windows с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении C# для .NET Core в Windows с помощью пакета SDK службы "Речь" в Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2cb89606986645d567136655d5ab3f07223ba70d
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434778"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C# для .NET Core в Windows с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services создать консольное приложение C# для .NET Core в Windows. Вы преобразуете речь с микрофона вашего ПК в текст в реальном времени. Приложение основано на [пакете SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

> [!NOTE]
> .NET Core — это кроссплатформенная платформа .NET с открытым кодом, которая реализует спецификацию [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).


## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **.NET cross-platform development** (Кроссплатформенная разработка .NET) доступна. Выберите **Tools**(Средства) > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть установщик Visual Studio. Если эта рабочая нагрузка уже включена, закройте диалоговое окно.

    ![Снимок экрана Visual Studio Installer с выделенной вкладкой "Рабочие нагрузки"](media/sdk/vs-enable-net-core-workload.png)

    В противном случае установите флажок рядом с полем **Кроссплатформенная разработка .NET Core** и выберите **Изменить** в правом нижнем углу диалогового окна. Для установки нового компонента нужно некоторое время.

1. Создайте новое консольное приложение .NET Core Visual C#. В диалоговом окне **Создать проект** в левой области разверните **Установлено** > **Visual C#** > **.NET Core**. Затем выберите **Console App (.NET Core)** (Консольное приложение (.NET Core)). В качестве имени проекта введите *helloworld*.

    ![Снимок экрана диалогового окна нового проекта](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Создание консольного приложения Visual C# (.NET Core)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

    ![Снимок экрана обозревателя решений, где выделен параметр "Управление пакетами NuGet для решения"](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Снимок экрана диалогового окна "Управление пакетами для решения"](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png " Установить пакет Nuget")

1. Примите условия отображаемого лицензионного соглашения, чтобы начать установку пакета NuGet.

    ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Принять условия лицензионного соглашения")

После установки пакета в консоли диспетчера пакетов появится подтверждение.


## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Начать отладку приложения")

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь" и транскрибируется в текст, который отображается в том же окне.

    ![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Выходные данные консоли после успешного распознавания")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Распознавание намерений из речи с помощью пакета SDK службы "Речь" для C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
