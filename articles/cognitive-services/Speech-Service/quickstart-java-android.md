---
title: Краткое руководство. Распознавание речи в приложении Java для Android с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении Java для Android с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: bbf10d9bd0912dab22c81e2bb850ede52a3bd9e3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929967"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении Java для Android с помощью пакета SDK для службы "Речь"

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Android с помощью пакета SDK службы "Речь" в Cognitive Services создать приложение Java, которое будет преобразовывать речь в текст.
Приложение основано на пакете SDK службы "Речь" в Microsoft Cognitive Services для Maven версии 0.6.0 и Android Studio 3.1.

> [!NOTE]
> Описание пакета SDK для устройств распознавания речи и устройства Roobo доступно на странице [пакета SDK для устройств распознавания речи](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер (Windows, Linux, Mac), на котором можно запустить Android Studio.
* [Android Studio](https://developer.android.com/studio/) версии 3.1.
* Устройство Android на основе ARM (API 23: Android 6.0 Marshmallow или более поздняя версия), [настроенное для разработки](https://developer.android.com/studio/debug/dev-options), с рабочим микрофоном.

## <a name="create-an-android-studio-project"></a>Создание проекта Android Studio

Запустите Android Studio и выберите **Start a new Android Studio project** (Создать проект Android Studio).

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

В мастере **создания проекта** мастер, который отобразится, выберите следующие параметры.

1. На экране **Create Android Project** (Создание проекта Android) введите в полн **Application name** (Имя приложения) **Quickstart**, укажите **samples.speech.cognitiveservices.microsoft.com** в поле **Company domain** (Домен компании) и выберите расположение проекта. Не устанавливайте флажки и нажмите кнопку **Next** (Далее).

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. На экране **Target Android Devices** (Целевые устройства Android) выберите только параметр **Phone and Tablet** (Телефон и планшет), выберите **API 23: Android 6.0 (Marshmallow)** из раскрывающегося списка ниже и нажмите кнопку **Next** (Далее).

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. На экране **Add an Activity to Mobile** (Добавление действия для мобильных устройств) выберите **Empty Activity** (Пустое действие) и нажмите кнопку **Next** (Далее).

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. На экране **Configure Activity** (Настройка действия) введите **MainActivity** в качестве имени действия и **activity\_main** — в качестве имени макета. Установите оба флажка и нажмите кнопку **Finish** (Готово).

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Программа будет работать некоторое время, после чего должен появиться созданный проект Android Studio.

## <a name="configure-your-project-for-the-speech-sdk"></a>Настройка проекта для пакета SDK для службы "Речь"

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `0.6.0`.

Пакет SDK службы "Речь" для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит библиотеки и разрешения Android, необходимые для его использования.
Она размещена в репозитории Maven в https://csspeechstorage.blob.core.windows.net/maven/.

Ниже описывается, как настроить проект для использования пакета SDK для службы "Речь".

Откройте окно структуры проекта, выбрав **File** (Файл) \> **Project Structure** (Структура проекта).
В открывшемся окне внесите следующие изменения (нажмите кнопку **ОК** только после выполнения всех шагов).

1. Выберите **Project** (Проект) и измените параметры **Default Library Repository** (Репозиторий библиотек по умолчанию), добавив запятую и URL-адрес репозитория Maven в одиночных кавычках `'https://csspeechstorage.blob.core.windows.net/maven/'`.

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. На том же экране слева выберите модуль **App** (Приложение) и вверху выберите вкладку **Dependencies** (Зависимости). Затем щелкните зеленый знак "плюс" в правом верхнем углу и выберите **Library dependency** (Зависимость от библиотеки).

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. В появившемся окне введите имя и версию нашего пакета SDK службы "Речь" для Android (`com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`), затем нажмите кнопку **ОК**.
   Пакет SDK для службы "Речь" должен быть добавлен в список зависимостей, как показано ниже.

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. В верхней части экрана выберите вкладку **Properties** (Свойства). Выберите значение **1.8** для параметров **Source Compability** (Совместимость источника) и **Target Compatibility** (совместимость цели).

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Наконец, нажмите кнопку **ОК**, чтобы закрыть окна **Project Structure** (Структура проекта) и применить все изменения.

## <a name="create-a-minimal-ui"></a>Создание минимального пользовательского интерфейса

Измените макет для основного действия, `activity_main.xml`.
По умолчанию он должен отображаться с именем вашего приложения в заголовке окна и элементом TextView, содержащим текст "Hello World!"

* Щелкните элемент TextView. Измените его атрибут ID в правом верхнем углу, указав `hello`.

* На палитре в левом верхнем углу окна `activity_main.xml` перетащите элемент Button на пустое место над текстом.

* В области атрибутов кнопки справа для атрибута `onClick` введите значение `onSpeechButtonClicked`. Это имя обработчика кнопки.
  Измените его атрибут ID в правом верхнем углу, указав `button`.

* Используйте значок волшебной палочки в верхней части окна конструктора, если вы хотите автоматически вывести ограничения макета.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Теперь текстовая и графическая версия пользовательского интерфейса должна выглядеть следующим образом.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Измените исходный файл `MainActivity.java` и замените его содержимое следующим кодом (под инструкцией package).

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Метод `onCreate` содержит код, который запрашивает разрешения для микрофона и подключения к Интернету, а также инициализирует привязки собственной платформы. Настройка привязок собственной платформы требуется только один раз, то есть ее следует выполнить в начале инициализации приложения.
   
   * Метод `onSpeechButtonClicked` был ранее настроен как обработчик нажатия кнопки. Нажатие кнопки активирует распознавание речи.

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

* Для выполнения сборки нажмите клавиши Ctrl+F9 или выберите **Build** (Сборка) \> **Make Project** (Создать проект).

* Подключите устройство Android к компьютеру разработки. Убедитесь, что [включен режим разработки и отладка по USB](https://developer.android.com/studio/debug/dev-options).

* Чтобы запустить приложение, нажмите клавиши Shift+F10 или выберите **Run** (Запуск) \> **Run 'app'** (Запустить "приложение").

* В отобразившихся окнах цели развертывания выберите свое устройство Android.

  ![Запуск приложения для отладки](media/sdk/qs-java-android-12-deploy.png)

* Приложение должно запуститься на устройстве.
  После нажатия кнопки в течение 15 секунд будет выполняться распознавание, результаты которого отобразятся в пользовательском интерфейсе (вы также сможете увидеть результат в окне Logcat в Android Studio).

  ![Пользовательский интерфейс после успешного распознавания](media/sdk/qs-java-android-13-gui-on-device.png)

На этом снимке экрана краткое руководство для Android завершается. Полный код примера проекта можно скачать из репозитория примеров.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/java-android`.

## <a name="next-steps"></a>Дополнительная информация

* [Получить примеры](speech-sdk.md#get-the-samples)
