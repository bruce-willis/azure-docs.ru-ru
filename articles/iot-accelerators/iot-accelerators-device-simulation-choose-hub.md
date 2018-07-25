---
title: Использование существующего Центра Интернета вещей с решением "Симулятор устройств" (Azure) | Документация Майкрософт
description: В этой статье описывается настройка акселератора решений "Симулятор устройств" для существующего Центра Интернета вещей.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967515"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Использование существующего Центра Интернета вещей с акселератором решений "Симулятор устройств"

При подготовке акселератора решений "Симулятор устройств" вы можете развернуть в предназначенной для него группе ресурсов Центр Интернета вещей и использовать его для симуляции.

Если вы откажетесь от необязательного развертывания Центра Интернета вещей, для симуляции придется предоставить собственный концентратор. Если вы согласитесь развернуть дополнительный Центр Интернета вещей, можно будет выбрать для использования этот или собственный концентратор.

При отсутствии Центра Интернета вещей на [портале Azure](https://portal.azure.com) всегда можно создать новый.

Чтобы использовать существующий Центр Интернета вещей, нужно получить строку подключения для политики общего доступа **iothubowner**. Эту строку подключения можно получить на [портале Azure](https://portal.azure.com):

1. На странице конфигурации Центра Интернета вещей на портале щелкните **Политики общего доступа**.
1. Щелкните **iothubowner**.
1. Скопируйте основную или дополнительную строку подключения.

[![Получение строки подключения](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Используйте строку подключения, которую вы скопировали при настройке симуляции:

[![Настройка симуляции](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Дополнительная информация

В этом практическом руководстве вы узнали, как использовать для симуляции существующий Центр Интернета вещей. Теперь вам будет полезно перейти к изучению [настройки пользовательской модели устройства](iot-accelerators-device-simulation-custom-model.md).
