---
title: Создание Центра Интернета вещей Azure с помощью командлета PowerShell | Документация Майкрософт
description: Узнайте, как с помощью командлетов PowerShell создать Центр Интернета вещей.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 7ecd35ba33d2860ba052aa27286c69985c2f7dd9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190236"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Создание Центра Интернета вещей с помощью командлета New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение

Для создания Центров Интернета вещей и управления ими можно использовать командлеты Azure PowerShell. В этом руководстве показано, как создать Центр Интернета вещей с помощью PowerShell.

Для работы с этим руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Подключение к подписке Azure

Если вы используете Cloud Shell, вы уже вошли в свою подписку. Если вместо этого вы используете PowerShell в локальной среде, введите следующую команду, чтобы войти в подписку Azure.

```powershell
# Log into Azure account.
Login-AzureRMAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для развертывания Центра Интернета вещей необходима группа ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую.

Чтобы создать группу ресурсов для Центра Интернета вещей, выполните команду [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В этом примере создается группа ресурсов с именем **MyIoTRG1**, размещенная в регионе **Восточная часть США**:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Чтобы создать Центр Интернета вещей в группе ресурсов, созданной на предыдущем шаге, используйте команду [New-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/New-AzureRmIotHub). В этом примере создается центр категории **S1** с именем **MyTestIoTHub**, размещенный в регионе **Восточная часть США**:

```azurepowershell-interactive
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Имя Центра Интернета вещей должно быть глобально уникальным.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Для вывода списка всех Центров Интернета вещей в подписке используйте команду [Get-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/Get-AzureRmIotHub):

```azurepowershell-interactive
Get-AzureRmIotHub
```

Это пример стандартного Центра Интернета вещей S1, который вы создали на предыдущем шаге.

Чтобы удалить Центр Интернета вещей, используйте команду [Remove-AzureRmIotHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/remove-azurermiothub):

```azurepowershell-interactive
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Вы также можете удалить группу ресурсов и все входящие в нее ресурсы, используя команду [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Remove-AzureRmResourceGroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Дополнительная информация

После развертывания Центра Интернета вещей с помощью командлета PowerShell вам могут понадобиться дополнительные сведения, с которыми можно ознакомиться в следующих статьях:

* [AzureRM.IotHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/).

* [Iot Hub Resource](https://docs.microsoft.com/rest/api/iothub/iothubresource) (Ресурс Центра Интернета вещей)

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [Пакеты SDK для Центра Интернета вещей Azure](iot-hub-devguide-sdks.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)