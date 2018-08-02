---
title: Управление обменом сообщениями между устройством и облаком Центра Интернета вещей Azure с использованием расширения Azure IoT Toolkit для Visual Studio Code | Документация Майкрософт
description: Узнайте, как использовать расширение Azure IoT Toolkit для Visual Studio Code для мониторинга сообщений между облаком и устройством в Центре Интернета вещей Azure.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/20/2018
ms.author: junhan
ms.openlocfilehash: 7bcb6eebdb6ceba6b07aeb19c1a74309fd4227d0
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206687"
---
# <a name="use-azure-iot-toolkit-extension-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Обмен сообщениями между устройством и Центром Интернета вещей с помощью расширения Azure IoT Toolkit для Visual Studio Code

![Комплексная схема](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) — это полезное расширение Visual Studio Code, которое упрощает управление Центром Интернета вещей. В этой статье рассматривается обмен сообщениями между устройством и Центром Интернета вещей с помощью расширения Azure IoT Toolkit для Visual Studio Code.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Новые знания

Здесь вы узнаете о мониторинге обмена сообщениями между облаком и устройством с использованием расширения Azure IoT Toolkit для Visual Studio Code. В сообщениях, отправляемых с устройства в облако, могут содержаться данные датчиков, которые устройство собирает и отправляет в Центр Интернета вещей. Сообщения, отправляемые из облака на устройство, могут содержать команды, которые Центр Интернета вещей отправляет на устройство и которые активируют светодиодный индикатор, подключенный к устройству.

## <a name="what-you-will-do"></a>Выполняемая задача

- Использование расширения Azure IoT Toolkit для Visual Studio Code для мониторинга отправки сообщений с устройства в облако.
- Использование расширения Azure IoT Toolkit для Visual Studio Code для отправки сообщений из облака на устройство.

## <a name="what-you-need"></a>Необходимые элементы

- Активная подписка Azure.
- Центр Интернета вещей Azure в подписке;
- [Visual Studio Code](https://code.visualstudio.com/)
- [Набор средств Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

1. В представлении VS Code **Проводник** разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в нижнем левом углу.
1. Щелкните **Select IoT Hub** (Выбрать Центр Интернета вещей) в контекстном меню.
1. При первом входе в Azure в правом нижнем углу появится всплывающее окно.
1. После входа в систему появится список подписок Azure. Выберите подписку и Центр Интернета вещей.
1. Через несколько секунд на вкладке **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) отобразится список устройств.

   > [!Note]
   > Можно также завершить настройку выбрав **Установка строки подключения Центра Интернета вещей**. Введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваш Центр Интернета вещей Azure.
   
## <a name="monitor-device-to-cloud-messages"></a>Отслеживание сообщений, отправляемых с устройства в облако

Чтобы отслеживать сообщения, отправляемые в Центр Интернета вещей с устройства, выполните следующие действия.

1. Щелкните правой кнопкой мыши устройство и выберите **Start Monitoring D2C Message** (Начать мониторинг сообщений D2C).
1. Отслеживаемые сообщения будут отображаться в представлении **Выходные данные** > **Azure IoT Toolkit**.
1. Чтобы остановить мониторинг, щелкните правой кнопкой мыши представление **Выходные данные** и выберите **Stop Monitoring D2C Message** (Остановить мониторинг сообщений D2C).

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство

Чтобы отправить сообщение из Центра Интернета вещей на устройство, выполните следующие действия.

1. Щелкните правой кнопкой мыши свое устройство и выберите **Send C2D Message to Device** (Отправить сообщение C2D на устройство). 
1. Введите сообщение в поле ввода.
1. Результаты отобразятся в представлении **Выходные данные** > **Azure IoT Toolkit**.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как отслеживать сообщения, отправляемые из устройства Интернета вещей в облако Центра Интернета вещей, и отправлять сообщения из этого облака на устройство.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
