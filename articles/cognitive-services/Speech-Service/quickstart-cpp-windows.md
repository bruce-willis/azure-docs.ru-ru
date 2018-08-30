---
title: Краткое руководство. Распознавание речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 9fae855de2a746084f4775f194e04c6dbe09f684
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127297"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Здесь описано, как создать консольное приложение на C ++ для Windows Desktop, которое использует пакет SDK для распознавания речи.
Приложение основано на [пакете SDK (NuGet) для службы "Речь" в Microsoft Cognitive Services](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер Windows с рабочим микрофоном.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), выпуск Community Edition или более поздняя версия.
* Рабочая нагрузка **Разработка классических приложений C++** и компонент **Диспетчер пакетов NuGet** в Visual Studio.
  Вы можете включить их, выбрав **Средства** \> **Get Tools and Features** (Получить инструменты и компоненты) и перейдя на вкладки **Рабочие нагрузки** и **Отдельные компоненты** соответственно.

  ![Включение рабочей нагрузки "Разработка классических приложений на C++"](media/sdk/vs-enable-cpp-workload.png)

  ![Включение диспетчера пакетов NuGet в Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

В Visual Studio 2017 создайте консольное классическое приложение Visual C++ для Windows. В диалоговом окне **Создать проект** в левой области разверните элементы **Установлено** \> **Visual C++** \> **Классическое приложение Windows** и выберите **Консольное приложение Windows**. В качестве имени проекта введите *helloworld*.

![Создание консольного приложения Desktop Visual C++ Windows](media/sdk/qs-cpp-windows-01-new-console-app.png)

Если вы работаете с 64-разрядной версией установки Windows, необязательно переключать платформу сборки на `x64`.

![Переключение платформы сборки на 64-разрядную версию](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Установите и создайте ссылку на пакет SDK для распознавания речи для NuGet

В обозревателе решений щелкните решение правой кнопкой мыши и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

![Нажатие правой кнопкой мыши пункт Manage NuGet Packages for Solution (Управление пакетами NuGet для решения)](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

В верхнем правом углу в поле **Источник пакета** выберите "Nuget.org".
На вкладке **Обзор** найдите пакет "Microsoft.CognitiveServices.Speech", выберите его и установите флажки **Проект** и **helloworld** справа и выберите **Установить**, чтобы установить его в проект helloworld.

> [!NOTE]
> Текущая версия пакета SDK для распознавания речи для Cognitive Services — `0.6.0`.

![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Примите лицензию во всплывающем окне.

![Принятие лицензии](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Замените свой по умолчанию начальный код следующим.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок:

   ![Удачная сборка](media/sdk/qs-cpp-windows-06-build.png)

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

   ![Запуск приложения для отладки](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Появится всплывающее окно консоли для речи (на английском языке).
   Результат распознавания будет отображаться на экране.

   ![Вывод на консоль после успешного распознавания](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/cpp-windows`.

## <a name="next-steps"></a>Дополнительная информация

* [Получить примеры](speech-sdk.md#get-the-samples)
