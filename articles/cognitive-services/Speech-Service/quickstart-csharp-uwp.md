---
title: Краткое руководство. Распознавание речи в приложении C# для UWP с помощью пакета SDK службы "Речь" в Cognitive Services | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении C# для UWP с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 751736a4333932a68775589dddeb82ef6fa6f398
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258214"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении UWP с помощью пакета SDK для службы "Речь"

Из этой статьи вы узнаете, как с помощью пакета SDK службы "Речь" в Cognitive Services создать приложение UWP (универсальная платформа Windows), которое будет преобразовывать речь в текст.
Приложение основано на [пакете SDK NuGet для Microsoft Cognitive Services](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

> [!NOTE]
> Универсальная платформа Windows позволяет разрабатывать приложения, работающие на любом устройстве, которое поддерживает Windows 10, включая компьютеры, Xbox, Surface Hub и другие устройств. Приложения, использующие пакет SDK для службы "Речь", еще не прошли сертификацию WACK (комплект сертификации приложений для Windows). Имеется возможность загрузить неопубликованное приложение, но в настоящее время его невозможно отправить в Windows Store.

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер Windows (Windows 10 Fall Creators Update или более поздней версии) с рабочим микрофоном.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), выпуск Community Edition или более поздняя версия.
* Рабочая нагрузка **Разработка приложений для универсальной платформы Windows** в Visual Studio. Ее можно включить, выбрав **Средства** \> **Get Tools and Features** (Получить инструменты и компоненты).

  ![Включение рабочей нагрузки "Разработка приложений для универсальной платформы Windows"](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. В Visual Studio 2017 создайте пустое универсальное приложение Visual C# для Windows. В диалоговом окне **Создать проект** в левой области разверните элементы **Установлено** \> **Visual C#** \> **Универсальная платформа Windows** и выберите **Пустое приложение (универсальное приложение для Windows)**. В качестве имени проекта введите *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. В появившемся окне **Новый проект приложения для универсальной платформы Windows** выберите значение **Windows 10 Fall Creators Update (10.0; СБОРКА 16299)** для параметра **Минимальная версия**. Для параметра **Целевая версия** выберите ту же или более позднюю версию, после чего нажмите кнопку **ОК**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Если вы работаете с 64-разрядной версией Windows, то вы можете переключить платформу сборки на `x64`.

   ![Переключение платформы сборки на 64-разрядную версию](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > В настоящее время пакет SDK для службы "Речь" поддерживает Intel-совместимые процессоры, но поддерживает не ARM.

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Нажатие правой кнопкой мыши пункт Manage NuGet Packages for Solution (Управление пакетами NuGet для решения)](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Установка пакета Nuget")

1. Примите условия лицензии в появившемся диалоговом окне.

    ![Принять лицензию](media/sdk/qs-csharp-uwp-06-nuget-license.png "Принять лицензию")

1. В консоли диспетчера пакетов отобразятся следующие выходные данные.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. В обозревателе решений дважды щелкните **Package.appxmanifest**, чтобы изменить манифест приложения.
   На вкладке **Возможности** установите флажок для возможности **Микрофон** и сохраните изменения.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Измените пользовательский интерфейс приложения, дважды щелкнув `MainPage.xaml` в обозревателе решений. 

    В представлении XAML конструктора вставьте следующий фрагмент кода XAML в тег Grid (между `<Grid>` и `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Измените код XAML, дважды щелкнув `MainPage.xaml.cs` в обозревателе решений (он расположен в элементе `MainPage.xaml`).
   Замените все содержимое этого файла приведенным ниже кодом.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` замените строку `YourSubscriptionKey` своим ключом подписки.

1. В обработчике `SpeechRecognitionFromMicrophone_ButtonClicked` замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните все внесенные в проект изменения.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Сборка успешно выполнена](media/sdk/qs-csharp-uwp-08-build.png "Сборка успешно выполнена.")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Запуск отладки приложения](media/sdk/qs-csharp-uwp-09-start-debugging.png "Запуск отладки приложения")

1. Откроется окно графического пользовательского интерфейса. Сначала нажмите кнопку **Enable Microphone** (Включить микрофон) и подтвердите разрешение в появившемся окне запроса.

    ![Запуск отладки приложения](media/sdk/qs-csharp-uwp-10-access-prompt.png "Запуск отладки приложения")

1. Щелкните **Speech recognition with microphone input** (Распознавание речи с помощью микрофона) и произнесите короткую фразу в микрофон устройства. Распознанный текст будет отображен в окне.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Дополнительная информация

- [Translate speech using Speech service](how-to-translate-speech.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
