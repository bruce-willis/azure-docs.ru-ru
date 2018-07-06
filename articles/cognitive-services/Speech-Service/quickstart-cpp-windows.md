---
title: Краткое руководство по пакету SDK для распознавания речи в C++ для Windows | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Сведения и примеры кода для быстрого начала использования пакета SDK для распознавания речи в Windows и C++ в Cognitive Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049236"
---
# <a name="quickstart-for-c-and-windows"></a>Краткое руководство для C++ и Windows

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `0.4.0`.

Здесь описано, как создать консольное приложение на C ++ для Windows Desktop, которое использует пакет SDK для распознавания речи.
Приложение основано на [пакете NuGet Microsoft Cognitive Services](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) и Microsoft Visual Studio 2017.

> [!NOTE]
> Если вам необходимо краткое руководство для C++ и Windows, см. [эту статью](quickstart-cpp-linux.md).<br>
> Если вам необходимо краткое руководство для C# и Windows, см. [эту статью](quickstart-csharp-windows.md).

> [!NOTE]
> Для этого краткого руководства необходим ПК с рабочим микрофоном.<br>
> См. [пример](speech-to-text-sample.md#speech-recognition-from-a-file), который распознает речь из заданного входного аудио файла.

> [!NOTE]
> Убедитесь, что установка Visual Studio включает рабочую нагрузку **Разработка классических приложений на C++**.
> Если вы не уверены, что это так, выполните следующие действия для проверки и исправления. В Visual Studio 2017 выберите **Инструменты** \> **Get Tools and Features** (Получить инструменты и функции) и подтвердите запрос контроля учетных записей, выбрав **Да**.
> Если на вкладке **Рабочие нагрузки** нет установленного флажка **Разработка классических приложений на C++**, установите его и щелкните **Изменить**, чтобы сохранить изменения.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Создание пустого проекта консольного приложения

В Visual Studio 2017 создайте новое консольное приложение Desktop Visual C++ Windows с названием "CppHelloSpeech".

![Создание консольного приложения Desktop Visual C++ Windows](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Если вы работаете с 64-разрядной версией установки Windows, необязательно переключать платформу сборки на `x64`.

![Переключение платформы сборки на 64-разрядную версию](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Установите и создайте ссылку на пакет SDK для распознавания речи для NuGet

> [!NOTE]
> Убедитесь, что менеджер пакетов NuGet включен для установки Visual Studio 2017.
> В Visual Studio 2017 выберите **Инструменты** \> **Get Tools and Features** (Получить инструменты и функции) и подтвердите запрос управления учетной записью пользователя, выбрав **Да**. Затем выберите вкладку **​​Индивидуальные компоненты** и найдите **менеджер пакетов NuGet** в **Инструменты кода**.
> Если флажок слева от него не установлен, обязательно установите его и щелкните **Изменить**, чтобы сохранить изменения.
>
> ![Включение диспетчера пакетов NuGet в Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

В обозревателе решений щелкните решение правой кнопкой мыши и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

![Нажатие правой кнопкой мыши пункт Manage NuGet Packages for Solution (Управление пакетами NuGet для решения)](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

В верхнем правом углу в поле **Источник пакета** выберите "Nuget.org".
На вкладке **Обзор** найдите пакет "Microsoft.CognitiveServices.Speech", выберите его и установите флажки **Проект** и **CppHelloSpeech** справа и выберите **Установить**, чтобы установить его в проект CppHelloSpeech.

![Установка пакета Microsoft.CognitiveServices.Speech NuGet](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Примите лицензию во всплывающем окне.

![Принятие лицензии](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Добавление примеров кода

Замените свой по умолчанию начальный код следующим.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Замените ключ подписки на тот, который вы получили. <br>
> Замените регион своим регионом из [Speech Service REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis) (REST API службы распознавания речи), например, замените на "westus".

![Добавление ключа подписки](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Теперь код должен компилироваться без ошибок:

![Удачная сборка](media/sdk/speechsdk-16-vs-cpp-build.png)

Запустите программу в отладчике с помощью кнопки запуска или клавишей F5.

![Запуск приложения для отладки](media/sdk/speechsdk-17-vs-cpp-f5.png)

Появится всплывающее окно консоли, предлагающее вам что-то сказать (на английском языке).
Результат распознавания будет отображаться на экране.

![Вывод на консоль после успешного распознавания](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Скачивание примера

Актуальный набор примеров доступен в [репозитории GitHub с примерами для пакета SDK для распознавания текста в Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные примеры можно найти на [странице с примерами](samples.md).
