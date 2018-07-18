---
title: Быстрое начало. Распознавание речи с помощью пакета SDK службы распознавания речи Cognitive Services в C# для Windows | Документация Майкрософт
description: Сведения о распознавании речи с помощью пакета SDK службы распознавания речи в C#.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016840"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы распознавания речи Cognitive Services C#

Из этой статьи вы узнаете, как в Windows с помощью пакета SDK службы распознавания речи Cognitive Services создать консольное приложение C#, которое будет преобразовывать речь в текст.

## <a name="prerequisites"></a>предварительным требованиям

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Не ниже Visual Studio 2017, Community Edition.
* Рабочие нагрузки при **разработке классического приложения .NET** в Visual Studio. Их можно включить, выбрав **Инструменты** \> **Добавить инструменты и компоненты**. 

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Создайте в Visual Studio 2017 новое консольное приложение Visual C#. В диалоговом окне **Создать проект** на левой панели разверните элемент **Установлено** и выберите **Консольное приложение (.NET Framework)**. В качестве имени проекта введите *CsharpHelloSpeech*.

    ![Создание консольного приложения Visual C# (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Создание консольного приложения Visual C#")

2. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Щелкните правой кнопкой мыши Управление пакетами NuGet для решения](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "Управление пакетами NuGet для решения")

3. В верхнем правом углу в поле **Источник пакета** выберите **Nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **CsharpHelloSpeech**.

    ![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Установка пакета Nuget")

4. Примите лицензию во всплывающем окне.

    ![Принять лицензию](media/sdk/speechsdk-09-vs-cs-nuget-license.png "Принять лицензию")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Создание конфигурации платформы, соответствующей архитектуре вашего ПК

Из этого раздела вы узнаете, как добавить новую платформу конфигурации, которая соответствует архитектуре процессора.

1. Запустите диспетчер конфигураций. Выберите **Создать** > **Диспетчер конфигураций**.

    ![Запуск диспетчера конфигураций](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Запуск диспетчера конфигураций")

2. Добавьте новую платформу в диалоговом окне **Диспетчера конфигураций**. В раскрывающемся списке **Платформа активного решения** выберите команду **Создать**.

    ![Добавление новой платформы в окне диспетчера конфигураций](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Добавление новой платформы в окне диспетчера конфигураций")

3. В случае использования 64-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x64`. В случае использования 32-разрядной версии Windows, создайте новую конфигурацию платформы с именем `x86`. Работая с этой статьей, вы создали платформу конфигурации `x64`. 

    ![Добавление новой платформы с именем "64" в 64-разрядной версии Windows](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Добавление 64-разрядной платформы")

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. В проекте Visual Studio `Program.cs` необходимо заменить тело класса `Program` следующим значением: Убедитесь, что ключ подписки и регион, полученные для службы, были заменены.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. После вставки кода, метод `Main()` должен выглядеть так, как показано на следующем снимке экрана:

    ![Как выглядит метод main после вставки кода](media/sdk/speechsdk-17-vs-cs-paste-code.png "Окончательное представление метода main")

3. Visual Studio IntelliSense выделит ссылки на классы пакета SDK для службы "Речь", которые не могут быть завершены. Чтобы устранить эту ошибку, в начало кода необходимо добавить следующее выражение `using` (вручную или с помощью [быстрых действий](https://docs.microsoft.com/visualstudio/ide/quick-actions) Visual Studio).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Использование быстрых действий для добавления отсутствующего выражения](media/sdk/speechsdk-18-vs-cs-add-using.png "Решение проблемы IntelliSense")

4. Убедитесь, что IntelliSense подсвечено и исправлено, затем сохраните изменения в проекте.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок:

    ![Сборка успешно выполнена](media/sdk/speechsdk-20-vs-cs-build.png "Сборка успешно выполнена.")

2. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**. 

    ![Запуск отладки приложения](media/sdk/speechsdk-21-vs-cs-f5.png "Запуск отладки приложения")

3. Появится всплывающее окно консоли для речи (на английском языке).
Результат распознавания будет отображаться на экране.

    ![Выходные данные консоли после успешного распознавания](media/sdk/speechsdk-22-cs-vs-console-output.png "Выходные данные консоли после успешного распознавания")

## <a name="download-code"></a>Скачать код

Актуальный набор примеров доступен в [репозитории GitHub с примерами для пакета SDK для распознавания текста в Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Дополнительная информация

- [Translate speech using Speech service](how-to-translate-speech.md) (Перевод речи с помощью службы распознавания речи)
- [Tutorial: Create a custom acoustic model](how-to-customize-speech-models.md) (Руководство. Создание пользовательской акустической модели)
