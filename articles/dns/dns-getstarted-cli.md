---
title: Краткое руководство. Создание зоны и записи Azure DNS с помощью Azure CLI
description: Краткое руководство. Создание зоны и записи DNS в Azure DNS. В этом пошаговом руководстве описано, как создать зону и запись DNS, а также управлять ими с помощью Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 3fb39558ff99c35786dedc133a9d1d1a450b5928
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090128"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Краткое руководство. Создание зоны и записи Azure DNS с помощью Azure CLI

В этой статье описано, как создать зону и запись DNS, используя Azure CLI, доступный для Windows, Mac и Linux. Эти действия также можно выполнить с помощью [портала Azure](dns-getstarted-portal.md) или [Azure PowerShell](dns-getstarted-powershell.md).

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Наконец, чтобы опубликовать зону DNS в Интернете, необходимо настроить серверы доменных имен для домена. Каждый из этих шагов описан ниже.

Теперь Azure DNS также поддерживает частные зоны DNS (сейчас в виде общедоступной предварительной версии). Дополнительные сведения об использовании частных зон DNS см. в статье [Using Azure DNS for private domains](private-dns-overview.md) (Использование Azure DNS для частных доменов). Инструкции по созданию частной зоны DNS см. в статье [Get started with Azure DNS private zones using CLI](./private-dns-getstarted-cli.md) (Начало работы с частными зонами Azure DNS с помощью CLI).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создайте группу ресурсов, которая будет включать эту зону DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью команды `az network dns zone create`. Чтобы просмотреть справку для этой команды, введите `az network dns zone create -h`.

В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Создание записи DNS

Чтобы создать запись DNS, используйте команду `az network dns record-set [record type] add-record`. Чтобы получить справку, например по записям типа A, см. `azure network dns record-set A add-record -h`.

В следующем примере создается запись с относительным именем www в зоне DNS contoso.com, в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — www.contoso.com. Используется тип записи A с IP-адресом 1.2.3.4 и сроком жизни по умолчанию 3600 секунд (1 час).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Обновление серверов доменных имен

Когда вы убедитесь, что зона и записи DNS настроены правильно, необходимо настроить доменное имя для использования серверов доменных имен службы DNS Azure. Это позволит другим пользователям в Интернете находить ваши записи DNS.

Для получения списка серверов доменных имен для определенной зоны используется команда `az network dns zone show`. Чтобы просмотреть имена серверов доменных имен, используйте выходные данные JSON, как показано в следующем примере:

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Эти серверы доменных имен необходимо настроить с помощью регистратора доменных имен (у которого было приобретено доменное имя). Регистратор предложит вам вариант настройки серверов доменных имен для домена. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Удаление всех ресурсов
 
Чтобы очистить ненужные ресурсы, созданные при работе с этим кратким руководством, удалите группу ресурсов:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы создали свою первую зону и запись DNS с помощью Azure CLI, можно создать записи для веб-приложения в личном домене.

> [!div class="nextstepaction"]
> [Создание записей DNS для веб-приложения в пользовательском домене](./dns-web-sites-custom-domain.md)
