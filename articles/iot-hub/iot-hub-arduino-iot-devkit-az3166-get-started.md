---
title: 'IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure | Документация Майкрософт'
description: В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 360937d335eadb6d235eb52c0d7df42f896a0de0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344737"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Он содержит совместимую с Arduino плату со множеством периферийных устройств и датчиков, пакет ПО для платы с открытым кодом и расширяющийся [каталог проектов](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Что нужно сделать

Подключим [плату DevKit](https://microsoft.github.io/azure-iot-developer-kit/) к созданному Центру Интернета вещей Azure, чтобы собирать данные о температуре и влажности с датчиков и отправлять эти данные в Центр Интернета вещей.

У вас еще нет платы DevKit? Воспользуйтесь [симулятором платы DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) или [приобретите ее](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Что вы узнаете

* Как подключить плату IoT DevKit к точке беспроводного доступа и подготовить среду разработки.
* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как собирать данные датчиков, запустив пример приложения на MXChip IoT DevKit.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT DevKit с кабелем micro USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10 или macOS 10.10 (или более поздней версии).
* Активная подписка Azure. Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Подготовка оборудования

Подключите к компьютеру следующее оборудование.

* Плата DevKit.
* Кабель micro USB.

![Необходимое оборудование](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Чтобы подключить плату DevKit к компьютеру, сделайте следующее:

1. Подключите разъем USB кабеля к компьютеру.

2. Подключите разъем micro USB кабеля к плате DevKit.

3. Зеленый светодиодный индикатор питания будет означать, что подключение установлено.

   ![Подключение оборудования](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Настройка Wi-Fi

Для работы проектов Центра Интернета вещей требуется подключение к Интернету. Следуйте приведенным ниже инструкциям, чтобы настроить плату DevKit для подключения к сети Wi-Fi.

### <a name="enter-ap-mode"></a>Переключение в режим точки беспроводного доступа

Удерживая нажатой кнопку B, нажмите и отпустите кнопку Reset (Сброс), а затем отпустите кнопку B. Плата DevKit переключится в режим точки беспроводного доступа для настройки Wi-Fi. На снимке экрана ниже отображается идентификатор SSID платы DevKit и IP-адрес портала настройки.

![Кнопка Reset (Сброс), кнопка B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Подключение к точке беспроводного доступа DevKit

Теперь используйте другое устройство с модулем Wi-Fi (компьютер или мобильный телефон), чтобы подключиться к плате DevKit по идентификатору SSID (выделен на снимке экрана выше). Поле пароля оставьте пустым.

![Сведения о сети и кнопка "Подключить"](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Настройка Wi-Fi для платы DevKit

Откройте IP-адрес, отображенный на экране DevKit в браузере на компьютере или в мобильном телефоне, выберите сеть Wi-Fi для подключения платы DevKit, а затем введите пароль. Нажмите кнопку **Подключиться**.

![Поле с паролем и кнопка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Через несколько секунд после успешного подключения плата DevKit перезагрузится. На экране появится имя Wi-Fi и IP-адрес.

![Имя Wi-Fi и IP-адрес](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP-адрес на фотографии может не совпадать с фактическим IP-адресом, который будет назначен и отображен на экране DevKit. Это нормально, так как сеть Wi-Fi использует протокол DHCP для динамического назначения IP-адресов.

После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено. Например, если вы настроили плату DevKit для домашней сети Wi-Fi и затем принесли ее на работу, потребуется перенастроить режим точки беспроводного доступа (начиная с шага в разделе "Переключение в режим точки беспроводного доступа"), чтобы подключить плату DevKit к сети Wi-Fi в офисе. 

## <a name="start-using-the-devkit"></a>Начало использования платы DevKit

Приложение по умолчанию, запущенное на плате DevKit, проверяет наличие последней версии встроенного ПО и отображает некоторые диагностические данные датчиков.

### <a name="upgrade-to-the-latest-firmware"></a>Обновление до последней версии встроенного ПО

> [!NOTE]
> Начиная с версии 1.1, DevKit поддерживает ST-SAFE в загрузчике. Если используется версия ниже 1.1, необходимо обновить встроенное ПО.

При необходимости обновить встроенное ПО на экране будут показаны текущая и последняя версии. Следуйте указаниям по [обновлению встроенного ПО](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).

![Отображение текущей и последней версии встроенного ПО](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Это действие выполняется один раз. Когда вы начнете разработку с помощью платы DevKit и передадите свое приложение, вместе с ним будет передана последняя версия встроенного ПО.

### <a name="test-various-sensors"></a>Тестирование различных датчиков

Нажмите кнопку B, чтобы проверить датчики. Нажимайте и отпускайте кнопку B, чтобы обойти по очереди все датчики.

![Отображение кнопки B и датчиков](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

### <a name="install-azure-iot-workbench"></a>Установка Azure IoT Workbench

Мы рекомендуем установить расширение [Azure IoT Workbench](https://aka.ms/iot-workbench) для Visual Studio Code, чтобы выполнять разработку с использованием платы DevKit.

Azure IoT Workbench предоставляет возможности интеграции для разработки решений для Интернета вещей. Это приложение помогает как в разработке на устройстве, так и в облаке с использованием Центра Интернета вещей и других служб. Вы можете просмотреть видео Channel9, чтобы получить общие сведения о выполняемых действиях.

Выполните инструкции ниже, чтобы подготовить среду разработки для DevKit.

1. Скачайте и установите [IDE Arduino](https://www.arduino.cc/en/Main/Software). Она предоставляет необходимую цепочку инструментов для компиляции и передачи кода Arduino.
    * **Windows**. Используйте версию установщика Windows.
    * **macOS**. Перетащите и отпустите извлеченное приложение **Arduino.app** в папку `/Applications`.
    * **Ubuntu**. Распакуйте его в папку, например `$HOME/Downloads/arduino-1.8.5`.

1. Установите кроссплатформенный редактор исходного кода [Visual Studio Code](https://code.visualstudio.com/) с мощными средствами разработки, такими как отладка и завершение кода IntelliSense.

1. Найдите **Azure IoT Workbench** в расширениях marketplace и установите его.
    ![Установка Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png). Вместе с IoT Workbench установятся и другие зависимые расширения.

1. Последовательно щелкните **Файл > Предпочтения > Параметры** и добавьте следующие строки, чтобы настроить Arduino.
    * **Windows**:
    ```javascript
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **MacOS**:
    ```javascript
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **Ubuntu**:
    ```javascript
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

1. Нажмите клавишу `F1`, чтобы открыть палитру команд, а затем введите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino). Выполните поиск **AZ3166** и установите последнюю версию.
    ![Установка пакета SDK для DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Установка драйверов ST-Link

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) — это интерфейс USB, который IoT DevKit использует для взаимодействия с вашим компьютером разработки. Выполните действия для определенной ОС, чтобы разрешить компьютеру доступ к устройству.

* **Windows**. Скачайте и установите USB-драйвер с веб-сайта [STMicroelectronics](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**. Для macOS не требуется драйвер.
* **Ubuntu**. Запустите следующий код в терминале, затем выйдите и войдите, чтобы изменение группы вступило в силу:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Теперь подготовка и настройка среды разработки завершены. Давайте создадим образец приложения "Hello, World!" для Интернета вещей, которое отправляет данные телеметрии температуры в Центр Интернета вещей Azure.

## <a name="build-your-first-project"></a>Создание первого проекта

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. В нижней правой строке состояния проверьте, выбрана ли плата **MXCHIP AZ3166** и используется ли последовательный порт с **STMicroelectronics**.
    ![Выбор платы и модели COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Нажмите клавишу `F1`, чтобы открыть палитру команд, потом введите и выберите **IoT Workbench: Examples** (Примеры IoT Workbench). Затем выберите **IoT DevKit** в качестве платы.

1. На странице с примерами IoT Workbench найдите **Get Started** (Приступить к работе) и щелкните **Open Sample** (Открыть пример). Затем выберите путь по умолчанию, чтобы загрузить пример кода.
    ![Открытие примера](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Если расширение Arduino не установлено в VS Code, нажмите кнопку **Установить** в области уведомлений.
    ![Установка расширения Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. В открывшемся окне проекта нажмите клавишу `F1`, чтобы открыть палитру команд, введите и выберите **IoT Workbench: Cloud** (Облако IoT Workbench), а затем щелкните **Azure Provision** (Подготовка Azure). Выполните пошаговые инструкции для завершения подготовки Центра Интернета вещей Azure и создания устройства.
    ![Подготовка в облаке](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Нажмите клавишу `F1`, чтобы открыть палитру команд, введите и выберите **IoT Workbench: Device** (Устройство IoT Workbench), а затем последовательно выберите **Config Device Settings > Select IoT Hub Device Connection String** (Настроить параметры устройства > Выбрать строку подключения к устройству Центра Интернета вещей).

1. В DevKit удерживайте нажатой **кнопку A**, нажмите и отпустите кнопку **Reset** (Сброс), а затем отпустите **кнопку А**. DevKit перейдет в режим настройки и сохранит строку подключения.
    ![Строка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Нажмите клавишу `F1` снова, введите и выберите **IoT Workbench: Device** (Устройство IoT Workbench), а затем щелкните **Device Upload** (Отправка устройства).
    ![Отправка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Если есть ошибки или прерывания, всегда можно восстановить данные, выполнив команду еще раз.

## <a name="test-the-project"></a>Тестирование проекта

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Просмотр данных телеметрии, отправленных в Центр Интернета вещей Azure

Щелкните значок вилки питания в строке состояния, чтобы открыть Serial Monitor. ![Serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает сообщение, отправленное в Центр Интернета вещей.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Выходные данные Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Просмотр данных телеметрии, полученных Центром Интернета вещей Azure

Можно использовать [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) для мониторинга сообщений, отправленных из устройства в облако (D2C) в Центре Интернета вещей.

1. В Visual Studio Code найдите **Azure IoT Toolkit** в расширениях marketplace и установите его.

1. Войдите на [портал Azure](https://portal.azure.com/) и найдите созданный Центр Интернета вещей.
    ![портала Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. В области **Политики общего доступа** выберите политику **iothubowner** и запишите строку подключения Центра Интернета вещей.
    ![Строка подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. В Visual Studio Code в левом нижнем углу разверните **Azure Iot Hub Devices** (Устройства Центра Интернета вещей), а затем выберите **Set IoT Hub Connection String** (Задать строку подключения Центра Интернета вещей).
    ![Установка строки подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Выберите команду **IoT: Start monitoring D2C message** (Начать мониторинг сообщений D2C в Центре Интернета вещей) в контекстном меню.

1. На панели **выходных данных** вы можете просмотреть сообщения D2C, поступающие в Центр Интернета вещей.
    ![Сообщения, отправляемые с устройства в облако](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, вы можете найти решение в [часто задаваемых вопросах по IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам с помощью [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Вы также можете оставить нам свой отзыв на этой странице.

## <a name="next-steps"></a>Дополнительная информация

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
