---
title: Краткое руководство. Распознавание речи в приложении C# для .NET Framework в Windows с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении C# для .NET Framework в Windows с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 8b257a6f4c32b4013ac0478d82dc1f7f32675b9b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578166"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C# для .NET Framework в Windows с помощью пакета SDK для службы "Речь".

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Windows с помощью пакета SDK службы "Речь" в Cognitive Services создать консольное приложение C# для .NET Framework, которое будет преобразовывать речь в текст.
Приложение основано на [пакете SDK NuGet для Microsoft Cognitive Services](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер Windows с рабочим микрофоном.
* Не ниже Visual Studio 2017, Community Edition.
* Рабочие нагрузки при **разработке классического приложения .NET** в Visual Studio. Их можно включить, выбрав **Инструменты** \> **Добавить инструменты и компоненты**.

  ![Включение рабочей нагрузки "Разработка классического приложения .NET"](media/sdk/vs-enable-net-desktop-workload.png)

  ![Включение рабочей нагрузки "Кроссплатформенная разработка .NET Core"](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Создайте в Visual Studio 2017 новое консольное приложение Visual C#. В диалоговом окне **Создать проект** в левой области разверните элементы **Установлено** \> **Visual C#** \> **Классическое приложение Windows** и выберите **Консольное приложение (.NET Framework)**. В качестве имени проекта введите *helloworld*.

    ![Создание консольного приложения Visual C# (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Щелкните правой кнопкой мыши Управление пакетами NuGet для решения](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "Установка пакета Nuget")

1. Примите условия отображаемой лицензии.

    ![Принять лицензию](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Принять лицензию")

1. В консоли диспетчера пакетов отобразятся следующие выходные данные.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Создание конфигурации платформы, соответствующей архитектуре вашего ПК

Из этого раздела вы узнаете, как добавить новую платформу конфигурации, которая соответствует архитектуре процессора.

1. Запустите диспетчер конфигураций. Выберите **Создать** > **Диспетчер конфигураций**.

    ![Запуск диспетчера конфигураций](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Запуск диспетчера конфигураций")

1. Добавьте новую платформу в диалоговом окне **Диспетчера конфигураций**. В раскрывающемся списке **Платформа активного решения** выберите команду **Создать**.

    ![Добавление новой платформы в окне диспетчера конфигураций](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Добавление новой платформы в окне диспетчера конфигураций")

1. В случае использования 64-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x64`. В случае использования 32-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x86`. Работая с этой статьей, вы создали платформу конфигурации `x64`.

    ![Добавление новой платформы с именем "64" в 64-разрядной версии Windows](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Добавление 64-разрядной платформы")

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Откройте файл `Program.cs` и замените все содержимое приведенным ниже кодом.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

    ![Сборка успешно выполнена](media/sdk/qs-csharp-dotnet-windows-08-build.png "Сборка успешно выполнена.")

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

    ![Запуск отладки приложения](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Запуск отладки приложения")

1. Появится всплывающее окно консоли для речи (на английском языке). Распознанный текст будет отображен в этом же окне.

    ![Выходные данные консоли после успешного распознавания](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Выходные данные консоли после успешного распознавания")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Дополнительная информация

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
