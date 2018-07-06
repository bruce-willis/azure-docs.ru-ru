---
title: Начало работы с пакетом SDK для речевых устройств | Документация Майкрософт
description: Обязательные требования и инструкции по началу работы с пакетом SDK для речевых устройств.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 32dd295132e26e46d64b01b194b557cae26575ad
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750801"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Начало работы с пакетом SDK для речевых устройств

В этой статье описывается, как настроить компьютер, где будет проводиться разработка, и набор средств для разработки звуковых устройств с помощью пакета SDK для речевых устройств. Затем вы создадите и развернете пример приложения на устройстве. 

Исходный код для примера приложения входит в состав пакета SDK для речевых устройств, а также доступен на сайте [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом разработки с помощью пакета SDK для речевых устройств соберите нужные сведения и программное обеспечение.

* Получить набор средств разработки можно на сайте [Roobo](http://ddk.roobo.com/). В доступности пакеты с конфигурациями линейной или кольцевой микрофонной решетки. Выберите необходимую вам.

    |Конфигурация набора средств разработки|Местонахождение говорящего|
    |-----------------------------|------------|
    |Кольцевая|С любой стороны устройства|
    |Линейная|Перед устройством|

* Получите последнюю версию пакета SDK для речевых устройств, включая пример приложения для Android, с [сайта загрузки](https://shares.datatransfer.microsoft.com/). Извлеките ZIP-файл в локальную папку (например, `C:\SDSDK`).

* Установите [Android Studio](https://developer.android.com/studio/) и [Vysor](http://vysor.io/download/) на вашем компьютере.

* Получите ваш [ключ подписки](get-started.md) службы "Речь". Вы можете получить 30-дневную бесплатную пробную версию или же скопировать ключ на вашей панели мониторинга Azure.

* Если вы хотите использовать распознавание намерений речевой службы, подпишитесь на [Language Understanding Intelligent Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) и [получите ключ подписки](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Вы можете [создать простую модель LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) или использовать пример модели LUIS`LUIS-example.json`, доступный в пакете SDK для речевых устройств [на сайте загрузки](https://shares.datatransfer.microsoft.com/). Загрузите файл JSON вашей модели на портал [LUIS](https://www.luis.ai/home), щелкнув **Import new app** (Импорт нового приложения) и выбрав файл JSON.

## <a name="set-up-the-development-kit"></a>Настройка набора средств разработки

1. Вставьте вилку адаптера питания набора средств разработки в розетку. Под верхней панелью должен засветиться зеленый индикатор питания.

1. С помощью кабеля Mini-USB подключите набор средств разработки к компьютеру.

    ![Подключение набора средств разработки](media/speech-devices-sdk/qsg-1.jpg)

1. Разместите набор разработки соответствующим образом.

    |Конфигурация набора средств разработки|Ориентация|
    |-----------------------------|------------|
    |Кольцевая|Вертикальное положение, микрофоны направлены к потолку|
    |Линейная|На боку, микрофоны направлены к вам (показано ниже)|

    ![Расположение набора средств разработки с линейной решеткой](media/speech-devices-sdk/qsg-2.jpg)

1. Установите сертификаты и табличный файл со словами для пробуждения (ключевое слово) и задайте разрешения для звукового устройства. Введите следующие команды в командном окне.

    > [!NOTE]
    > Эти команды используют средство Android Debug Bridge (`adb.exe`), которое является частью установки Android Studio. Этот инструмент можно найти в каталоге `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Вы можете добавить этот каталог в путь, чтобы было удобнее вызывать `adb`. В противном случае нужно указывать полный путь к установке `adb.exe` в каждой команде, вызывающей `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Отключите звук микрофона и динамики вашего компьютера. Таким образом, вы гарантируете работу с микрофонами набора средств разработки и случайно не запустите устройство с использованием аудио на компьютере.
    
1.  Запустите Vysor на компьютере.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Устройство должно быть указано в разделе Choose a device (Выберите устройство). Нажмите кнопку **View** (Просмотреть) рядом с ним. 
 
1.  Подключитесь к беспроводной сети, нажав кнопку **Параметры**, затем выбрав **WLAN**.

    ![Беспроводная локальная сеть Vysor](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Запуск примера приложения

Для запуска тестов Roobo и проверки настройки набора средств разработки создайте и установите пример приложения.

1.  Запустите Android Studio.

1.  Выберите команду открытия существующего проекта Android Studio.

    ![Android Studio. Команда открытия существующего проекта](media/speech-devices-sdk/qsg-5.png)
 
1.  Перейдите к `C:\SDSDK\Android-Sample-Release\example`, а затем нажмите кнопку **ОК**, чтобы открыть пример проекта.
 
1.  Добавьте ключ подписки службы "Речь" в исходном коде. Если вы хотите попробовать распознать намерения, также добавьте свой ключ подписки [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) и идентификатор приложения. 

    Ключи и сведения о приложении должны содержаться в следующих строках в исходном файле `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. По умолчанию слово для пробуждения (ключевое слово) — "Компьютер".  При желании можно попробовать одно из других предоставленных слов — "Машина" или "Помощник". Файлы ресурсов для этих альтернативных слов находятся в пакете SDK для речевых устройств в папке "keyword". Например, `C:\SDSDK\Android-Sample-Release\keyword\Computer` содержит файлы, используемые для слова "Компьютер".

    Кроме того, вы можете [создать пользовательское слово для пробуждения](speech-devices-sdk-create-kws.md).

    Чтобы установить желаемое слово для пробуждения, выполните следующие действия:
 
    * Создайте папку `keyword` в папке \data\ на устройстве, выполнив следующие команды в командном окне.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Скопируйте файлы `kws.table`, `kws_g.fst`, `kws_k.fst` и `words_kw.txt` в папку устройства \data\keyword\. Выполните следующие команды в командном окне.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Добавьте ссылки на эти файлы в примере приложения. Добавьте следующие строки в файл `MainActivity.java`. Убедитесь, что используется указанное ключевое слово и что путь указывает на файл `kws.table`, отправленный на устройство.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > В коде можно использовать файл `kws.table` для создания экземпляра модели ключевого слова и начать распознавание следующим образом.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Обновите следующие строки, содержащие параметры геометрии микрофонной решетки.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Переменная|Значение|Доступные значения|
    |--------|-------|----------------|
    |`DeviceGeometry`|Физическая конфигурация микрофона|`Circular6+1` для кольцевого набора средств разработки|
    ||| `Linear4` для линейного набора средств разработки|
    |`SelectedGeometry`|Программная конфигурация микрофона|`Circular6+1` для кольцевого набора средств разработки, использующего все микрофоны|
    |||`Circular3+1` для кольцевого набора средств разработки, использующего четыре микрофона|
    |||`Linear4` для линейного набора средств разработки, использующего все микрофоны|
    |||`Linear2` для линейного набора средств разработки, использующего два микрофона|


1.  Создайте приложение, выбрав **запуск приложения** в меню запуска. Появится диалоговое окно Select Deployment Target (Выбор цели развертывания). Выберите ваше устройство и нажмите кнопку **ОК** для развертывания приложения на устройстве.

    ![Выбор цели развертывания](media/speech-devices-sdk/qsg-7.png)
 
1.  Запускается пример приложения пакета SDK для речевых устройств, отображающий показанные здесь параметры.

    ![Пример приложения для речевого устройства](media/speech-devices-sdk/qsg-8.png)

1. Поэкспериментируйте с приложением!

## <a name="troubleshooting"></a>Устранение неполадок

Если при использовании службы "Речь" происходят ошибки сертификата, убедитесь, что на устройстве правильно установлены дата и время.

Подробнее о разработке см. в [руководстве по разработке](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) на сайте Roobo.

Сайт Roobo предоставляет инструмент, который собирает все аудио во флэш-память, что может помочь в устранении проблем с аудио. Для каждой конфигурации набора разработки предусмотрена отдельная версия инструмента. Выберите устройство на сайте [Roobo](http://ddk.roobo.com/), а затем щелкните ссылку **ROOBO Tools** (Средства ROOBO) в нижней части страницы.
