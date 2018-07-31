---
title: Руководство. Создание частной зоны Azure DNS с помощью Azure PowerShell
description: В этом руководстве описано, как создать и проверить частную зону и запись DNS в Azure DNS. Это пошаговое руководство по созданию первой частной зоны DNS и записи DNS, а также управлению ими с помощью PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/24/2018
ms.author: victorh
ms.openlocfilehash: 872227e0521bd54e6bf7fdbe3626dfca34170863
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257731"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Руководство. Создание частной зоны Azure DNS с помощью Azure PowerShell

Это руководство поможет создать первую частную зону и запись DNS с помощью Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Они называются *виртуальными сетями разрешения*. Кроме того, можно указать виртуальную сеть, для которой служба Azure DNS будет поддерживать записи имен узлов всякий раз, когда виртуальная машина создается, меняет IP-адрес или удаляется.  Такая сеть называется *виртуальной сетью регистрации*.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание частной зоны DNS
> * Создание тестовых виртуальных машин
> * Создание дополнительной записи DNS
> * Проверка частной зоны

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

При необходимости инструкции из этого руководства можно выполнить с помощью [Azure CLI](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала нужно создать группу ресурсов, которая будет включать зону DNS. 

```azurepowershell
New-AzureRMResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Создание частной зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone` со значением *Private* для параметра **ZoneType**. В следующем примере создается зона DNS под именем **contoso.local** в группе ресурсов **MyAzureResourceGroup**. Эта зона становится доступной для виртуальной сети под названием **MyAzureVnet**.

Если опустить параметр **ZoneType**, созданная зона будет общедоступной, поэтому требуется создать частную зону. 

```azurepowershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Если необходимо создать зону только для разрешения имени (без создания автоматического имени узла), можно использовать параметр *ResolutionVirtualNetworkId* вместо *RegistrationVirtualNetworkId*.

> [!NOTE]
> Увидеть автоматически созданные имена узлов нельзя. Но позже можно будет убедиться, что они существуют.

### <a name="list-dns-private-zones"></a>Список частных зон DNS

Если опустить имя зоны в командлете `Get-AzureRmDnsZone`, можно получить список всех зон в группе ресурсов. Эта операция возвращает массив объектов зоны.

```azurepowershell
Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Если опустить имя зоны и имя группы ресурсов в командлете `Get-AzureRmDnsZone`, то можно получить список всех зон в подписке Azure.

```azurepowershell
Get-AzureRmDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Создание тестовых виртуальных машин

Теперь создайте две виртуальные машины, чтобы иметь возможность проверить частную зону DNS.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Ее выполнение может занять несколько минут.

## <a name="create-an-additional-dns-record"></a>Создание дополнительной записи DNS

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. В следующем примере создается запись с относительным именем **db** в зоне DNS **contoso.local** в группе ресурсов **MyAzureResourceGroup**. Полное доменное имя набора записей — **db.contoso.local**. Тип записи — "A", IP-адрес — "10.2.0.4", а срок жизни составляет 3600 секунд.

```azurepowershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Просмотр записей DNS

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurepowershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyAzureResourceGroup
```
Помните, что увидеть автоматически созданные записи А для двух тестовых виртуальных машин нельзя.

## <a name="test-the-private-zone"></a>Проверка частной зоны

Теперь можно проверить разрешение имени для частной зоны **contoso.local**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Настройка виртуальных машин для получения входящего ICMP-трафика

Можно воспользоваться командой проверки связи (ping) для проверки разрешения имени. Для этого настройте брандмауэр для обеих виртуальных машин, чтобы получать входящие пакеты ICMP.

1. Подключитесь к myVM01 и откройте окно Windows PowerShell с правами администратора.
2. Выполните следующую команду:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Повторите действия для myVM02.

### <a name="ping-the-vms-by-name"></a>Проверка связи с виртуальными машинами по имени

1. В командной строке Windows PowerShell на виртуальной машине myVM02 проверьте связь с myVM01, используя автоматически зарегистрированное имя узла.
   ```
   ping myVM01.contoso.local
   ```
   Результат должен выглядеть примерно так:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Теперь проверьте связь с именем **db**, созданным ранее.
   ```
   ping db.contoso.local
   ```
   Результат должен выглядеть примерно так:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Удалите группу ресурсов **MyAzureResourceGroup**, если ресурсы, созданные в этом руководстве, больше не нужны.

```azurepowershell
Remove-AzureRMResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как развернуть частную зону DNS, создать запись DNS и протестировать зону.
Далее можно получить дополнительные сведения о частных зонах DNS.

> [!div class="nextstepaction"]
> [Использование Azure DNS для частных доменов](private-dns-overview.md)




