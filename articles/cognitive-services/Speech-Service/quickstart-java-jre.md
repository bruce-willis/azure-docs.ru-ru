---
title: Краткое руководство. Распознавание речи в Java (Windows или Linux)
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознать речь в Java (Windows или Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234279"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Краткое руководство. Распознавание речи в Java (Windows или Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этом документе описывается создание консольного приложения на платформе Java для среды выполнения Java (JRE), которое использует пакет SDK для распознавания речи.
Приложение основано на пакете SDK Microsoft Cognitive Services для Maven.
Мы используем Eclipse как интегрированную среду разработки (IDE).

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* ПК с рабочим микрофоном (Windows x64, Ubuntu 16.04 x64), на котором можно запустить Eclipse.
* 64-разрядная версия JRE/JDK для Java 8.
* [Eclipse](https://www.eclipse.org) 4.8, 64-разрядная версия.
* Для установки необходимых пакетов выполните следующие команды в Ubuntu 16.04:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Создание и настройка проекта

1. Запустите Eclipse.

1. В средстве запуска Eclipse введите имя нового каталога в поле **Workspace** (Рабочая область).
   Нажмите кнопку **Launch** (Запуск).

   ![Создание рабочей области Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Через некоторое время отобразится главное окно интегрированной среды разработки Eclipse.
   Если отображается экран приветствия, закройте это окно.

1. Выберите **File** \> **New** \> **Project** ( Файл > Создать > Проект).

1. В открывшемся мастере **создания проекта** выберите **Java Project** (Проект Java) и щелкните **Next** (Далее).

   ![Выбор мастера](media/sdk/qs-java-jre-02-select-wizard.png)

1. В следующем окне введите в качестве имени проекта **quickstart** и выберите **JavaSE-1.8** (или выше) в качестве среды выполнения.
   Нажмите кнопку **Готово**

   ![Выбор мастера](media/sdk/qs-java-jre-03-create-java-project.png)

1. Щелкните **Open Perspective** (Открыть перспективу), если появится окно с названием **Open Associated Perspective?** (Открыть связанную перспективу?).

1. В **обозревателе пакетов** щелкните правой кнопкой мыши проект **quickstart**, а затем выберите **Configure** \> **Convert to Maven Project** (Настроить > Преобразовать в проект Maven).

   ![Преобразование в проект Maven](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. В открывшемся окне введите **com.microsoft.cognitiveservices.speech.samples** в качестве **идентификатора группы** и **quickstart** в качестве **идентификатора артефакта**. Выберите **Готово**.

   ![Настройка файла POM Maven](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Отредактируйте файл **pom.xml**.

  * В конце файла перед закрывающим тегом `</project>` создайте раздел репозиториев со ссылкой на репозиторий Maven для пакета SDK для службы "Речь":

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Также добавьте после этого раздел зависимостей с пакетом SDK для службы "Речь" версии 0.6.0 в качестве зависимости:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Сохраните изменения.

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Выберите **File** \> **New** \> **Class** (Файл > Создать > Класс), чтобы добавить пустой класс в проект Java.

1. В окне **New Java Class** (Новый класс Java) введите **speechsdk.quickstart** в поле **Package** (Пакет) и **Main** в поле **Name** (Имя).

   ![Создание класса Main](media/sdk/qs-java-jre-06-create-main-java.png)

1. Замените весь код в файле `Main.java` на следующий фрагмент:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Сохраните внесенные в проект изменения.

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Нажмите клавишу F11 или щелкните **Run** \> **Debug** (Запуск > Отладка).
Слова, произносимые в микрофон, в течение следующих 15 секунд будут распознаны и записаны в окне консоли.

![Вывод на консоль после успешного распознавания](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/java-jre`.

## <a name="next-steps"></a>Дополнительная информация

* [Получить примеры](speech-sdk.md#get-the-samples)
