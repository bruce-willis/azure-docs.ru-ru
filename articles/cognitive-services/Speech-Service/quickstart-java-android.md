---
title: Краткое руководство. Распознавание речи в приложении Java для Android с помощью пакета SDK для службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении Java для Android с помощью пакета SDK для службы "Речь" в Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 0a52889ef879aeb8a5a1ed59b74619dc3337e1e9
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432802"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении Java для Android с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Android с помощью пакета SDK службы "Речь" в Cognitive Services создать приложение Java, которое будет преобразовывать речь в текст.
Приложение основано на пакете SDK Maven версии 1.0.0 для службы "Речь" в Microsoft Cognitive Services и Android Studio 3.1.
Пакет SDK службы "Речь" на данное время совместим с устройствами Android, в которых установлены 32-разрядные или 64-разрядные процессоры ARM.

> [!NOTE]
> Ознакомиться с пакетом SDK для речевых устройств и устройством Roobo можно в разделе [Сведения о пакете SDK для речевых устройств (предварительная версия)](speech-devices-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="create-and-configure-a-project"></a>Создание и настройка проекта

1. Запустите Android Studio и в окне приветствия выберите **Start a new Android Studio project** (Создать проект Android Studio).

    ![Снимок экрана окна приветствия Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Откроется мастер **Create New Project** (Создание нового проекта). На экране **Create Android Project** (Создание проекта Android) в поле **Application name** (Имя приложения) введите **Quickstart**, в поле **Company domain** (Домен компании) укажите **samples.speech.cognitiveservices.microsoft.com** и выберите каталог проекта. Оставьте флажки C++ и Kotlin снятыми и выберите **Далее**.

   ![Снимок экрана мастера Create New Project (создание нового проекта)](media/sdk/qs-java-android-02-create-android-project.png)

1. На экране **Target Android Devices** (целевые устройства Android) выберите только пункт **Phone and Tablet** (телефоны и планшеты). В раскрывающемся списке под этим пунктом выберите **API 23: Android 6.0 (Marshmallow)** и нажмите **Далее**.

   ![Снимок экрана мастера Create New Project (создание нового проекта)](media/sdk/qs-java-android-03-target-android-devices.png)

1. На экране **Add an Activity to Mobile** (Добавление действия для мобильных устройств) выберите **Empty Activity** (Пустое действие) и нажмите кнопку **Далее**.

   ![Снимок экрана мастера Create New Project (создание нового проекта)](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. На экране **Configure Activity** (Настройка действия) введите **MainActivity** в качестве имени действия и **activity\_main** — в качестве имени макета. Установите оба флажка и нажмите **Готово**.

   ![Снимок экрана мастера Create New Project (создание нового проекта)](media/sdk/qs-java-android-05-configure-activity.png)

Android Studio требуется несколько минут, чтобы подготовить новый проект Android. Затем настройте для проекта пакет SDK для службы "Речь" и использование Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.0.0`.

Пакет SDK службы "Речь" для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит библиотеки и разрешения Android, необходимые для его использования.
Она размещена в репозитории Maven в https://csspeechstorage.blob.core.windows.net/maven/.

Настройте проект для использования пакета SDK службы "Речь". Откройте окно Project Structure (Структура проекта), выбрав в строке меню Android Studio **Файл** > **Project Structure** (Структура проекта). В окне Project Structure (Структура проекта) внесите следующие изменения: 

1. В списке в левой части окна выберите **Project** (Проект). Измените параметры **Default Library Repository** (Репозиторий библиотек по умолчанию), добавив запятую и URL-адрес репозитория Maven в одиночных кавычках. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Снимок экрана окна Project Structure (Структура проекта)](media/sdk/qs-java-android-06-add-maven-repository.png)

1. В том же экране слева выберите **app** (приложение). Затем в верхней части окна выберите вкладку **Зависимости**. Нажмите зеленый знак плюс (+) и выберите в раскрывающемся меню **Library dependency** (Зависимость библиотек).

   ![Снимок экрана окна Project Structure (Структура проекта)](media/sdk/qs-java-android-07-add-module-dependency.png)

1. В появившемся окне введите имя и версию нашего пакета SDK службы "Речь" для Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`. Нажмите кнопку **ОК**.
   Пакет SDK для службы "Речь" должен быть добавлен в список зависимостей, как показано ниже.

   ![Снимок экрана окна Project Structure (Структура проекта)](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Выберите вкладку **Свойства**. Выберите значение **1.8** для обоих параметров **Source Compability** (Совместимость источника) и **Target Compatibility** (Совместимость цели).

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Чтобы закрыть окно Project Structure (Структура проекта) и применить изменения к проекту, нажмите **OК**.

## <a name="create-user-interface"></a>Создание пользовательского интерфейса

Для этого приложения мы создадим базовый пользовательский интерфейс. Измените макет для основного действия, `activity_main.xml`. В макете должны отображаться имя вашего приложения в заголовке окна и элемент TextView, содержащий текст "Hello World!"

* Щелкните элемент TextView. Измените его атрибут ID в правом верхнем углу, указав `hello`.

* Перетащите элемент button (кнопка) из палитры в левом верхнем углу окна `activity_main.xml` на пустое место над текстом.

* В области атрибутов кнопки справа для атрибута `onClick` введите значение `onSpeechButtonClicked`. Мы создадим метод с таким именем для обработки события кнопки.  Измените его атрибут ID в правом верхнем углу, указав `button`.

* Используйте значок волшебной палочки в верхней части окна конструктора, чтобы вывести ограничения макета.

  ![Снимок экрана со значком "волшебная палочка"](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Теперь текстовая и графическая версии пользовательского интерфейса должны выглядеть следующим образом.

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

## <a name="add-sample-code"></a>Добавление примеров кода

1. Откройте исходный файл `MainActivity.java`. Замените весь код после оператора `package` следующим кодом.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Метод `onCreate` содержит код, который запрашивает разрешения на использование микрофона и подключения к интернету, а также инициализирует привязки собственной платформы. Настроить привязки собственной платформы нужно только один раз. Это следует сделать в начале инициализации приложения.
   
   * Метод `onSpeechButtonClicked`, как упоминалось ранее, является обработчиком нажатия кнопки. Нажатие кнопки активирует преобразование речи в текст.

1. В том же файле замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

1. Подключите устройство Android к компьютеру разработки. Убедитесь, что вы включили на устройстве [режим разработки и отладку по USB](https://developer.android.com/studio/debug/dev-options).

1. Чтобы выполнить сборку приложения, нажмите комбинацию клавиш Ctrl + F9 или выберите в строке меню **Сборка** > **Make Project** (Создать проект).

1. Чтобы запустить приложение, нажмите комбинацию клавиш Shift+F10 или выберите**Запуск** > **Run 'app'** (Запустить "приложение").

1. В отобразившемся окне целей развертывания выберите свое устройство Android.

   ![Снимок экрана окна Select Deployment Target (Выбор цели развертывания)](media/sdk/qs-java-android-12-deploy.png)

Чтобы начать процесс распознавания речи, нажмите кнопку в приложении. Последующие 15 секунд английской речи будут отправлены в службу "Речь" для распознавания. Его результат отображается в приложении Android и в окне "logcat" в Android Studio.

![Снимок экрана приложения Android](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/java-android`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Распознавание намерений из речи с помощью пакета SDK службы "Речь" для Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>См. также

- [Translate speech using Speech service](how-to-translate-speech-csharp.md) (Перевод речи с помощью службы распознавания речи)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
