---
title: Создание Центра Интернета вещей Azure с помощью набора средств Интернета вещей Azure для VS Code | Документация Майкрософт
description: Использование набора средств Интернета вещей Azure для VS Code при создании Центра Интернета вещей.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 5097d7e1f6a0b9e94919ccc8731702206c0a1568
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047239"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Создание Центра Интернета вещей с помощью набора средств Интернета вещей Azure для Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

В этой статье показано, как с помощью [Azure IoT Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) создать Центр Интернета вещей Azure. 

Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Набор средств Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

1. Откройте в Visual Studio Code представление **Explorer**.

2. В нижней части Explorer разверните раздел **Устройства Центра Интернета вещей Azure**. 

   ![Развернутый раздел "Устройства Центра Интернета вещей Azure"](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, наведите курсор на заголовок. 

4. Выберите **Создать центр IoT**.

5. При первом входе в Azure в правом нижнем углу появится всплывающее окно.

6. Выберите подписку Azure. 

7. Выберите группу ресурсов.

8. Выберите расположение.

9. Выберите ценовой уровень.

10. Введите глобальное уникальное имя вашего Центра Интернета вещей.

11. Подождите несколько минут, пока Центр Интернета вещей не будет создан.

## <a name="next-steps"></a>Дополнительная информация

Вы развернули Центр Интернета вещей с помощью набора средств Интернета вещей Azure для Visual Studio Code. Чтобы продолжить изучение, ознакомьтесь со следующими статьями:

* [Обмен сообщениями между устройством и Центром Интернета вещей с помощью расширения Azure IoT Toolkit для Visual Studio Code](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Управление устройствами Интернета вещей Azure с помощью расширения "Набор средств Интернета вещей Azure" для Visual Studio Code](iot-hub-device-management-iot-toolkit.md)

* [См. вики-страницу по набору средств Интернета вещей Azure](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
