---
title: Создание частной зоны Azure DNS с помощью Azure CLI
description: В этом руководстве описано, как создать и проверить частную зону и запись DNS в Azure DNS. В нем пошагово описывается создание первой частной зоны и записи DNS, а также управление ими с помощью Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/25/2018
ms.author: victorh
ms.openlocfilehash: 023a1ecb6afc49dd20a14d57558d72a44779dbe9
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257571"
---
# <a name="create-an-azure-dns-private-zone-using-azure-cli"></a>Создание частной зоны Azure DNS с помощью Azure CLI

Это руководство поможет создать первую частную зону и запись DNS с помощью Azure CLI.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Они называются *виртуальными сетями разрешения*. Кроме того, можно указать виртуальную сеть, для которой служба Azure DNS будет поддерживать записи имен узлов всякий раз, когда виртуальная машина создается, меняет IP-адрес или удаляется.  Такая сеть называется *виртуальной сетью регистрации*.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание частной зоны DNS
> * Создание тестовых виртуальных машин
> * Создание дополнительной записи DNS
> * Проверка частной зоны

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

При необходимости инструкции из этого руководства можно выполнить с помощью [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала нужно создать группу ресурсов, которая будет включать зону DNS. 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Создание частной зоны DNS

Зона DNS создается с помощью команды `az network dns zone create` со значением *Private* для параметра **ZoneType**. В следующем примере создается зона DNS под именем **contoso.local** в группе ресурсов **MyAzureResourceGroup**. Эта зона становится доступной для виртуальной сети под названием **MyAzureVnet**.

Если опустить параметр **ZoneType**, созданная зона будет общедоступной, поэтому требуется создать частную зону.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefix 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n contoso.local \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Если необходимо создать зону только для разрешения имен (без автоматического создания имен узлов), можно использовать параметр *resolution-vnets* вместо *registration-vnets*.

> [!NOTE]
> Увидеть автоматически созданные имена узлов нельзя. Но позже можно будет убедиться, что они существуют.

### <a name="list-dns-private-zones"></a>Список частных зон DNS

Чтобы перечислить зоны DNS, используйте `az network dns zone list`. Чтобы получить справку, см. `az network dns zone list --help`.

Если указать группу ресурсов, то будут перечислены только зоны в этой группе ресурсов.

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Если не указать группу ресурсов, то будут перечислены все зоны в подписке.

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Создание тестовых виртуальных машин

Теперь создайте две виртуальные машины, чтобы иметь возможность проверить частную зону DNS.

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Это может занять несколько минут.

## <a name="create-an-additional-dns-record"></a>Создание дополнительной записи DNS

Чтобы создать запись DNS, используйте команду `az network dns record-set [record type] add-record`. Чтобы получить справку, например по записям типа A, см. `azure network dns record-set A add-record --help`.

 В следующем примере создается запись с относительным именем **db** в зоне DNS **contoso.local** в группе ресурсов **MyAzureResourceGroup**. Полное доменное имя набора записей — **db.contoso.local**. Тип записи — A, а ее IP-адрес — 10.2.0.4.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z contoso.local \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Просмотр записей DNS

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z contoso.local
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

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как развернуть частную зону DNS, создать запись DNS и протестировать зону.
Далее можно получить дополнительные сведения о частных зонах DNS.

> [!div class="nextstepaction"]
> [Использование Azure DNS для частных доменов](private-dns-overview.md)