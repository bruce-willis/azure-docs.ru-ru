---
title: Отправка сообщений на сервер MQTT с помощью клиентской библиотеки Azure MQTT | Документация Майкрософт
description: Использование DevKit в качестве клиента для отправки сообщений на сервер MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: fc74613e00adc459f7a7b0a16c6f773fe4bf601d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593661"
---
# <a name="send-messages-to-an-mqtt-server"></a>Отправка сообщений на сервер MQTT

Подключение систем Интернета вещей к Интернету зачастую бывает нестабильным, медленным или низкого качества. Протокол MQTT для связи между компьютерами (M2M) специально разработан с учетом этих трудностей. 

Используемая в нашем примере клиентская библиотека MQTT входит в проект [Eclipse Paho](http://www.eclipse.org/paho/), который предоставляет API для применения MQTT в разных транспортных средах.

## <a name="what-you-learn"></a>Что вы узнаете

В этом проекте вы узнаете:
- как использовать клиентскую библиотеку MQTT для отправки сообщений на брокер MQTT;
- как настроить MXChip Iot DevKit в качестве клиента MQTT.

## <a name="what-you-need"></a>Необходимые элементы

Выполните указания в [руководстве по началу работы](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started), чтобы перейти к таким этапам:

* Подключение DevKit к сети Wi-Fi.
* Подготовка среды разработки

## <a name="open-the-project-folder"></a>Открытие папки проекта

1. Если устройство DevKit подключено к компьютеру, отключите его.

2. Запустите VSCode.

3. Подключите DevKit на компьютере.

## <a name="open-the-mqttclient-sample"></a>Открытие примера MQTTClient

Разверните раздел **Arduino Examples** (Примеры Arduino) слева, перейдите в папку **Examples for MXCHIP AZ3166 > MQTT** (Примеры для MXCHIP AZ3166 > MQTT) и выберите **MQTTClient**. Откроется новое окно VS Code с папкой проекта.

> [!NOTE]
> Пример также можно открыть из палитры команд. Нажмите `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Создание эскиза Arduino и его передача на DevKit

Нажмите клавиши `Ctrl+P` (macOS: `Cmd+P`), чтобы выполнить команду `task device-upload`. По завершении передачи устройство DevKit перезапускается и запускает эскиз.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Вы можете увидеть сообщение об ошибке "Error: AZ3166: Unknown package" (Ошибка AZ3166: неизвестный пакет). Такая ошибка возникает, если индекс пакета платы неправильно обновлен. Чтобы устранить эту ошибку, ознакомьтесь с [разделом по разработке](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development) в статье с часто задаваемыми вопросами об IoT DevKit.

## <a name="test-the-project"></a>Тестирование проекта

В VS Code выполните следующую процедуру, чтобы открыть и настроить средство Serial Monitor:

1. Щелкните слово `COM[X]` в строке состояния, чтобы задать нужный COM-порт с помощью `STMicroelectronics`. ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Щелкните значок вилки питания в строке состояния, чтобы открыть Serial Monitor. ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. В строке состояния щелкните число, представляющее скорость в бит/с, и укажите значение `115200`. ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Serial Monitor отобразит все сообщения, отправленные из примененного эскиза. Этот эскиз подключается к DevKit по Wi-Fi. После успешного подключения по Wi-Fi эскиз отправляет сообщение на брокер MQTT. Затем в примере поочередно отправляются два сообщения iot.eclipse.org с параметрами QoS 0 и QoS 1, соответственно.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, ознакомьтесь с [вопросами и ответами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) об IoT DevKit или используйте один из следующих каналов связи:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>См. также

* [Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure в облаке](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake: получение сообщений из Twitter](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как настроить MXChip Iot DevKit в качестве клиента MQTT и применить клиентскую библиотеку MQTT для отправки сообщений на брокер MQTT. Мы предлагаем вам продолжить обучение, изучив следующие статьи.

* [Общие сведения об акселераторе решений для удаленного мониторинга Интернета вещей Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Azure IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Подключение устройства MXChip IoT DevKit к приложению Azure IoT Central)
