---
title: Центр Интернета вещей Azure — подключение устройств Интернета вещей к облаку | Документация Майкрософт
description: Сведения о подключении плат и начальных наборов для Интернета вещей к Центру Интернета вещей Azure. Устройства могут отправлять данные телеметрии в Центр Интернета вещей, который, в свою очередь, может отслеживать эти устройства и управлять ими.
author: dominicbetts
manager: timlt
keywords: Руководство по работе с Центром Интернета вещей Azure
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125198"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Руководства по началу работы с Центром Интернета вещей и реальными устройствами

В этих практических руководствах рассматривается Центр Интернета вещей Azure и запуск пакетов SDK для устройств на реальных устройствах.

## <a name="set-up-your-device"></a>Настройка устройства

Подключите устройство или шлюз Интернета вещей к Центру Интернета вещей:

| Устройство IoT                       | Язык программирования |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino в VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Онлайн-симулятор устройства         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
