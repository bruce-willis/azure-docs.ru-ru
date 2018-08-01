---
title: Создание виртуальной машины Linux в Azure с несколькими сетевыми картами | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с несколькими сетевыми картами с помощью Azure CLI 2.0 или шаблонов Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: iainfou
ms.openlocfilehash: aae71dafd3685e44975049c4287c083abc2330bc
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144862"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Как создать виртуальную машину Linux в Azure с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений или когда для решения мониторинга либо архивации используется выделенная сеть. В этой статье описаны способы создания виртуальной машины с несколькими сетевыми картами, присоединенными к ней, и добавления или удаления сетевых адаптеров на существующей виртуальной машине. Различные [размеры виртуальных машин](sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

В этой статье подробно описывается, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Создание вспомогательных ресурсов
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index#az_login).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnetFrontEnd*.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Создайте подсеть для внутреннего трафика с помощью команды [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). В следующем примере создается подсеть с именем *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). В следующем примере создается группа безопасности сети *myNetworkSecurityGroup*.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Создание и настройка нескольких сетевых карт
Создайте две сетевых карты с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create). В следующем примере создаются две сетевые карты — *myNic1* и *myNic2*, — подключенные к группе безопасности сети. Каждая из этих сетевых карт подключена к отдельной подсети.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Создание виртуальной машины и подключение сетевых карт
При создании виртуальной машины укажите сетевые карты, созданные с помощью `--nics`. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](sizes.md).

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем *myVM*.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Добавление сетевой карты к виртуальной машине
На предыдущих шагах вы создали виртуальную машину с несколькими сетевыми картами. С помощью Azure CLI 2.0. можно также добавить сетевые карты к существующей виртуальной машине. Различные [размеры виртуальных машин](sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины. При необходимости вы можете [изменить размер виртуальной машины](change-vm-size.md).

Создайте другую сетевую карту с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create). В следующем примере создается сетевая карта *myNic3*, подключенная к внутренней подсети и группе безопасности сети, созданных на предыдущих шагах:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Чтобы добавить сетевую карту к существующей виртуальной машине, сначала отмените распределение виртуальной машины с помощью команды [az vm deallocate](/cli/azure/vm#az_vm_deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*.


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Добавьте сетевую карту, выполнив команду [az vm nic add](/cli/azure/vm/nic#az_vm_nic_add). В следующем примере сетевая карта *myNic3* добавляется к виртуальной машине *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#az_vm_start).

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Удаление сетевой карты с виртуальной машины
Чтобы удалить сетевую карту с существующей виртуальной машины, сначала отмените распределение виртуальной машины с помощью команды [az vm deallocate](/cli/azure/vm#az_vm_deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Удалите сетевую карту, выполнив команду [az vm nic remove](/cli/azure/vm/nic#az_vm_nic_remove). В следующем примере удаляется сетевая карта *myNic3* с виртуальной машины *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#az_vm_start).

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Дополнительные сведения см. в [обзоре Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy* .

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../../resource-group-create-multiple.md). 

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `myNic1`, `myNic2` и т. д. Ниже показан пример добавления значения индекса.

```json
"name": "[concat('myNic', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../../virtual-network/template-samples.md).

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Настройка гостевой ОС для нескольких сетевых карт

На предыдущих шагах была создана виртуальная сеть и подсеть, подключены сетевые карты, а затем создана виртуальная машина. Общедоступный IP-адрес и правила группы безопасности сети, позволяющие передачу SSH-трафика, созданы не были. Чтобы настроить гостевую ОС для нескольких сетевых карт, необходимо разрешить удаленные подключения и запускать команды локально на виртуальной машине.

Чтобы разрешить передачу SSH-трафика, создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) следующим образом.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) и назначьте его первой сетевой карте с помощью команды [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update).

```azurecli
az network public-ip-address create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip-addres myPublicIP
```

Чтобы просмотреть представление общедоступного IP-адреса виртуальной машины, используйте команду [az vm show](/cli/azure/vm#az-vm-show) следующим образом.

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Теперь подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины. Имя пользователя, по умолчанию заданное на предыдущем шаге, — *azureuser*. Укажите новое имя пользователя и общедоступный IP-адрес.

```bash
ssh azureuser@137.117.58.232
```

Для отправки в дополнительный сетевой интерфейс или из него необходимо вручную добавить постоянные маршруты в операционную систему для каждого дополнительного сетевого интерфейса. В этой статье *eth1* — это дополнительный интерфейс. Инструкции по добавлению постоянных маршрутов в операционную систему отличаются для разных дистрибутивов. См. инструкции в разделе документации по дистрибутиву.

При добавлении маршрута к операционной системе адрес шлюза — *.1* для любой подсети, в которой находится сетевой интерфейс. Например, если сетевому интерфейсу назначен адрес *10.0.2.4*, следует указать шлюз для маршрута *10.0.2.1*. Для назначения маршрута можно определить конкретную сеть или указать назначение *0.0.0.0*, если требуется, чтобы весь трафик для интерфейса проходил через указанный шлюз. Шлюзом для каждой подсети управляет виртуальная сеть.

После добавления маршрута для дополнительного интерфейса убедитесь, что маршрут помещен в таблицу маршрутов с помощью команды `route -n`. В следующем примере выходных данных показаны данные для таблицы маршрутов, которая имеет два сетевых интерфейса, добавленных в этой статье к виртуальной машине.

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Проверив свою таблицу маршрутов после перезагрузки, убедитесь, что добавленный маршрут сохраняется после перезагрузки. Чтобы проверить возможность подключения, можно ввести, например, следующую команду, где *eth1* — имя дополнительного сетевого интерфейса.

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с [размерами виртуальных машин Linux](sizes.md), когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины.

Для дальнейшего обеспечения безопасности виртуальных машин используйте JIT-доступ к виртуальной машине. Эта функция при необходимости открывает правила группы безопасности сети для SSH-трафика на определенный период времени. Дополнительные сведения см. в статье [Управление доступом к виртуальным машинам с помощью JIT](../../security-center/security-center-just-in-time.md).
