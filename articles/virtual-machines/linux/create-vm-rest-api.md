---
title: Создание виртуальной машины Linux с помощью Azure REST API | Документация Майкрософт
description: Сведения о создании виртуальной машины Linux, которая использует управляемые диски и проверку подлинности SSH с Azure REST API, в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3eeaee9bc6320231f10aa85227e2f43756181806
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433486"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Создание виртуальной машины Linux, в которой используется проверка подлинности по SSH с интерфейсом REST API

Виртуальная машина Linux в Azure состоит из различных ресурсов, таких как диски и сетевые интерфейсы, и определяет параметры, например расположение, размер, образы операционной системы и параметры аутентификации.

Вы можете создать виртуальную машину Linux с помощью портала Azure, Azure CLI 2.0, множества пакетов SDK для Azure, шаблонов Azure Resource Manager и других сторонних инструментов, таких как Ansible или Terraform. В конечном счете все эти инструменты создают виртуальную машину Linux с помощью REST API.

В этой статье показано, как с помощью REST API создать виртуальную машину Linux под управлением Ubuntu 18.04-LTS с управляемыми дисками и аутентификацией SSH.

## <a name="before-you-start"></a>Перед началом работы

Перед созданием и отправкой запроса вам потребуется:

* `{subscription-id}` для вашей подписки.
  * Если у вас несколько подписок, см. [эту статью](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).
* Объект `{resourceGroupName}`, который вы создали заранее.
* [Виртуальный сетевой интерфейс](../../virtual-network/virtual-network-network-interface.md) в той же группе ресурсов.
* Пара ключей SSH (вы можете [создать ее](mac-create-ssh-keys.md), если у вас ее нет).

## <a name="request-basics"></a>Основные сведения о запросе

Для создания или обновления виртуальной машины используйте следующую операцию *PUT*.

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Кроме параметров `{subscription-id}` и `{resourceGroupName}`, необходимо указать параметр `{vmName}` (параметр `api-version` указывать необязательно, но эта статья протестирована с параметром `api-version=2017-12-01`)

Ниже приведены обязательные заголовки.

| Заголовок запроса   | ОПИСАНИЕ |
|------------------|-----------------|
| *Content-Type:*  | Обязательный элемент. Задайте значение `application/json`. |
| *Authorization:* | Обязательный элемент. Задайте в качестве значения [допустимый токен доступа](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Общие сведения о работе с запросами REST API см. в разделе [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Компоненты запроса или ответа REST API).

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

Ознакомьтесь с примером текста запроса ниже. Убедитесь, что вы указали имя виртуальной машины в параметрах `{computerName}` и `{name}`, имя сетевого интерфейса, созданное в разделе `networkInterfaces`, имя пользователя в параметрах `adminUsername` и `path`, и *открытую* часть пары ключей SSH (расположенная, например, в `~/.ssh/id_rsa.pub`) в `keyData`. Может потребоваться изменить другие параметры, которые включают `location` и `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
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
      "computerName": "{vmName}",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Полный список доступных определений для текста запроса см. в статье [Virtual Machines - Create Or Update](/rest/api/compute/virtualmachines/createorupdate#definitions) (Виртуальные машины — создание или обновление).

## <a name="sending-the-request"></a>Отправка запроса

Вы можете отправить HTTP-запроса с помощью предпочитаемого клиента. Кроме того, вы можете также использовать [инструмент в браузере](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate), нажав кнопку **Попробовать**.

### <a name="responses"></a>Ответы

Существует два успешных ответа для операции по созданию или обновлению виртуальной машины.

| ИМЯ        | type                                                                              | ОПИСАНИЕ |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 ОК      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | ОК          |
| 201 Создано | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Создано     |

Сокращенный ответ *201 Создано*, полученный из предыдущего примера текста запроса, который создает виртуальную машину, показывает, что *vmId* был назначен, и что *ProvisionState* находится в состоянии *Создание*.

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Дополнительные сведения об ответах REST API можно узнать в разделе [Process the response message](/rest/api/azure/#process-the-response-message) (Обработка ответного сообщения).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure REST API или других средствах управления (например, Azure CLI или Azure PowerShell) см. в статьях:

- [Azure Compute](/rest/api/compute/) (Служба вычислений Azure)
- [Начало работы с Azure REST API](/rest/api/azure/)
- [интерфейс командной строки Azure](/cli/azure/)
- [Overview of Azure PowerShell](/powershell/azure/overview) (Общие сведения об Azure PowerShell)
