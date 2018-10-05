---
title: Краткое руководство. Распознавание речи в приложении C# для UWP с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в UWP с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2eb6bda7066a01e5532fe7c0e20b0ee13f4289b6
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432993"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении UWP с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как создать приложение C# для универсальной платформы Windows с помощью [пакета SDK службы "Речь"](speech-sdk.md) в Cognitive Services. Вы преобразуете речь с микрофона вашего устройства в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **Разработка приложений для универсальной платформы Windows** доступна. Выберите **Инструменты** > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть Visual Studio Installer. Если эта рабочая нагрузка уже включена, закройте диалоговое окно. 

    ![Снимок экрана Visual Studio Installer с выделенной вкладкой "Рабочие нагрузки"](media/sdk/vs-enable-uwp-workload.png)

    В противном случае установите флажок рядом с полем **Кроссплатформенная разработка .NET** и выберите **Изменить** в правом нижнем углу диалогового окна. Для установки этого нового компонента нужно некоторое время.

1. Создайте пустое универсальное приложение Visual C# для Windows. Сначала из меню выберите **Файл** > **Создать** > **Проект**. В диалоговом окне **Создать проект** в области слева разверните элементы **Установлено** > **Visual C#** > **Универсальное приложение Windows**. Затем выберите **Пустое приложение (универсальное приложение Windows)**. В качестве имени проекта введите *helloworld*.

    ![Снимок экрана: диалоговое окно нового проекта](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Для пакета SDK службы "Речь" требуется, чтобы ваше приложение было создано для Windows 10 Fall Creators Update или более поздней версии. В появившемся окне **Новый проект приложения для универсальной платформы Windows** выберите значение **Windows 10 Fall Creators Update (10.0; сборка 16299)** для параметра **Минимальная версия**. В окне **Целевая версия** выберите ту же или более позднюю версию, после чего нажмите кнопку **ОК**.

    ![Снимок экрана окна нового проекта приложения для универсальной платформы Windows](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Если вы используете 64-разрядную версию Windows, можно переключиться на платформу сборки `x64` с помощью раскрывающегося меню на панели инструментов Visual Studio. (64-разрядная версия Windows может запускать 32-разрядные приложения, поэтому при необходимости вы можете оставить значение `x86`.)

   ![Снимок экрана с панелью инструментов Visual Studio, на которой выделено значение "x64"](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Пакет SDK для службы "Речь" поддерживает только Intel-совместимые процессоры. В настоящее время ARM не поддерживается.

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Снимок экрана обозревателя решений, где выделено управление пакетами NuGet для решения](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Снимок экрана диалогового окна управления пакетами для решения](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Установка пакета NuGet")

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

    ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](media/sdk/qs-csharp-uwp-06-nuget-license.png "Принять условия лицензии")

1. В консоли диспетчера пакетов отобразятся следующие выходные данные.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.0' to helloworld
   ```

1. Поскольку приложение использует микрофон для ввода речи, добавьте возможность **Микрофон** к проекту. В обозревателе решений дважды щелкните **Package.appxmanifest**, чтобы изменить манифест приложения. Затем перейдите на вкладку **Возможности**, установите флажок для возможности **Микрофон** и сохраните изменения.

   ![Снимок экрана манифеста приложения Visual Studio с выделенными вкладкой "Возможности" и пунктом "Микрофон"](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Добавление примеров кода

1. Пользовательский интерфейс приложения определяется с помощью XAML. Откройте `MainPage.xaml` в обозревателе решений. В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег Grid (между `<Grid>` и `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Откройте исходный файл с кодом `MainPage.xaml.cs` (он находится в группе `MainPage.xaml`). Замените все содержимое приведенным ниже кодом.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` этого файла замените строку `YourSubscriptionKey` своим ключом подписки.

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните все внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-csharp-uwp-08-build.png "Успешная сборка")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-csharp-uwp-09-start-debugging.png "Начало отладки приложения")

1. Откроется окно. Выберите**Включить микрофон** и подтвердите разрешение в появившемся окне запроса.

    ![Снимок экрана запроса разрешения](media/sdk/qs-csharp-uwp-10-access-prompt.png "Запуск отладки приложения")

1. Щелкните **Распознавание речи с помощью микрофона** и произнесите фразу или предложение на английском языке в микрофон вашего устройства. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

    ![Снимок экрана пользовательского интерфейса распознавания речи](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Распознавание намерений на основе речи с помощью пакета SDK службы "Речь" для C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
