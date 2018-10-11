---
title: Краткое руководство. Распознавание речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь" в Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 12faf7ae7e45e6f6ede739b24d9ead28ff26313e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886335"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в классическом приложении C++ для Windows с помощью пакета SDK службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье вы создадите консольное приложение C++ для Windows. Вы будете использовать [пакет SDK для службы "Речь"](speech-sdk.md) в Cognitive Services, чтобы преобразовать речь с микрофона вашего ПК в текст в реальном времени. Приложение создается с использованием [пакета SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget) и Microsoft Visual Studio 2017 (любого выпуска).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **Desktop development with C++** (Разработка классических приложений на C++) доступна. Выберите **Tools**(Средства) > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть установщик Visual Studio. Если эта рабочая нагрузка уже включена, перейдите к следующему шагу. 

    ![Снимок экрана, где отображается вкладка "Рабочие нагрузки" Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    В противном случае поставьте флажок рядом с **Desktop development with C++** (Разработка классических приложений на C++). 

1. Убедитесь, что компонент **Диспетчер пакетов NuGet** доступен. Переключитесь на вкладку **Отдельные компоненты** диалогового окна установщика Visual Studio и выберите **Диспетчер пакетов NuGet**, если он еще не включен.

      ![Снимок экрана вкладки "Отдельные компоненты" Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. При необходимости включить рабочую нагрузку C++ или NuGet выберите **Изменить** (в правом нижнем углу диалогового окна). Для установки новых компонентов нужно некоторое время. Если обе функции уже были включены, закройте диалоговое окно.

1. Создайте новое консольное классическое приложение Visual C++ для Windows. Сначала из меню выберите **Файл** > **Создать** > **Проект**. В диалоговом окне **Создать проект** в левой области разверните **Установлено** > **Visual C++** > **Классическое приложение Windows**. Затем выберите **Консольное приложение Windows**. В качестве имени проекта введите *helloworld*.

    ![Снимок экрана: диалоговое окно нового проекта](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Если вы используете 64-разрядную Windows, можно переключиться на платформу сборки `x64` с помощью раскрывающегося меню на панели инструментов Visual Studio. (64-разрядные версии Windows могут запускать 32-разрядные приложения, так что это не обязательно.)

    ![Снимок экрана панели инструментов Visual Studio с выделенным параметром x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. В обозревателе решений правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

    ![Снимок экрана обозревателя решений, где выделен параметр "Управление пакетами NuGet для решения"](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Снимок экрана диалогового окна "Управление пакетами для решения"](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.0.0`.

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

    ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](media/sdk/qs-cpp-windows-05-nuget-license.png)

После установки пакета в консоли диспетчера пакетов появится подтверждение.

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл *helloworld.cpp*. Замените весь код ниже первого оператора включения (`#include "stdafx.h"` или `#include "pch.h"`) следующим кодом.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Создайте приложение. В строке меню последовательно выберите **Сборка** > **Собрать решение**. Теперь код должен компилироваться без ошибок.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Собрать решение"](media/sdk/qs-cpp-windows-06-build.png)

1. Запустите приложение. В строке меню последовательно выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.

   ![Снимок экрана приложения Visual Studio с выделенным параметром "Начать отладку"](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Появится всплывающее окно консоли с запросом произнести что-либо. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь" и транскрибируется в текст, который отображается в том же окне.

   ![Снимок экрана выходных данных консоли после успешного распознавания](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/cpp-windows`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Распознавание намерений на основе речи с помощью пакета SDK службы "Речь" для C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
