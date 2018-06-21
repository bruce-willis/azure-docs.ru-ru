---
title: Создание виртуальной машины Linux с помощью Azure REST API | Документация Майкрософт
description: Сведения о создании виртуальной машины Linux, которая использует управляемые диски и проверку подлинности SSH с Azure REST API, в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826364"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Создание виртуальной машины Linux, в которой используется проверка подлинности по SSH с интерфейсом REST API

Для определения виртуальной машины (VM) в Azure используется несколько параметров (расположение, размер оборудования, образ операционной системы и учетные данные для входа). В этой статье показано, как использовать REST API для создания виртуальной машины Linux, использующей проверку подлинности SSH.

Для создания или обновления виртуальной машины используйте следующую операцию *PUT*.

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Создание запроса

Для создания запроса *PUT* требуется параметр `{subscription-id}`. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Для ваших ресурсов параметры `{resourceGroupName}` и `{vmName}` определяются вместе с параметром `api-version`. Для этой статьи используется `api-version=2017-12-01`.

Ниже приведены обязательные заголовки.

| Заголовок запроса   | ОПИСАНИЕ |
|------------------|-----------------|
| *Content-Type:*  | Обязательный элемент. Задайте значение `application/json`. |
| *Authorization:* | Обязательный элемент. Задайте в качестве значения [допустимый токен доступа](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Дополнительные сведения по созданию запросов см. в разделе [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Компоненты запроса или ответа REST API).

## <a name="create-the-request-body"></a>Создание текста запроса

Для создания текста запроса используются следующие общие определения.

| ИМЯ                       | Обязательно | type                                                                                | ОПИСАНИЕ  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | Истина     | строка                                                                              | Расположение ресурса. |
| name                       |          | строка                                                                              | Имя виртуальной машины. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Указывает параметры оборудования виртуальной машины. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Указывает параметры хранилища дисков виртуальной машины. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Указывает параметры операционной системы виртуальной машины. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Указывает сетевые интерфейсы виртуальной машины. |

Полный список доступных определений для текста запроса см. в статье [Virtual Machines - Create Or Update](/rest/api/compute/virtualmachines/createorupdate#definitions) (Виртуальные машины — создание или обновление).

### <a name="example-request-body"></a>Примеры текста запроса

В следующем примере текста запроса определяется образ ОС Ubuntu 18.04-LTS, в котором используются управляемые диски уровня "Премиум". После использования проверки подлинности по открытому ключу SSH, виртуальная машина использует существующую виртуальную сетевую карту, которая была [создана ранее](../../virtual-network/virtual-network-network-interface.md). Укажите открытый ключ SSH в поле *osProfile.linuxConfiguration.ssh.publicKeys.keyData*. При необходимости можно [создать пары ключей SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Ответы

Существует два успешных ответа для операции по созданию или обновлению виртуальной машины.

| ИМЯ        | type                                                                              | ОПИСАНИЕ |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 ОК      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | ОК          |
| 201 Создано | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Создано     |

Дополнительные сведения об ответах REST API можно узнать в разделе [Process the response message](/rest/api/azure/#process-the-response-message) (Обработка ответного сообщения).

### <a name="example-response"></a>Пример ответа

Сокращенный ответ *201 Создано*, полученный из предыдущего примера текста запроса, который создает виртуальную машину, показывает, что *vmId* был назначен, и что *ProvisionState* находится в состоянии *Создание*.

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure REST API или других средствах управления (например, Azure CLI 2.0 или Azure PowerShell) см. в статьях:

- [Azure Compute](/rest/api/compute/) (Служба вычислений Azure)
- [Начало работы с Azure REST API](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Overview of Azure PowerShell](/powershell/azure/overview) (Общие сведения об Azure PowerShell)
