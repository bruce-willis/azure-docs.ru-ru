---
title: Запросы к Azure Resource Graph для начинающих
description: Используйте Azure Resource Graph для выполнения нескольких простых запросов.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: b0d8466f1deaa82260aaebee5398f1b8fbea851d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965557"
---
# <a name="starter-resource-graph-queries"></a>Запросы к Resource Graph для начинающих

Первым шагом на пути к пониманию запросов к Azure Resource Graph является общее понимание [языка запросов](../concepts/query-language.md). Если вы еще не знакомы с языком запросов KQL, рекомендуется просмотреть основные сведения, чтобы понять, как составлять запросы к ресурсам, которые вы ищете.

Мы рассмотрим следующие запросы для начинающих:

> [!div class="checklist"]
> - [Подсчет ресурсов Azure](#count-resources)
> - [Вывод списка ресурсов, отсортированных по имени](#list-resources)
> - [Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания](#show-vms)
> - [Отображение первых пяти виртуальных машин по имени и типу ОС](#show-sorted)
> - [Подсчет виртуальных машин по типу ОС](#count-os)
> - [Отображение ресурсов, которые содержат хранилище](#show-storage)
> - [Вывод списка общедоступных IP-адресов](#list-publicip)
> - [Подсчет ресурсов с IP-адресами, настроенными посредством подписки](#count-resources-by-ip)
> - [Вывод списка ресурсов с определенным значением тега](#list-tag)
> - [Вывод списка всех учетных записей хранения с определенным значением тега](#list-specific-tag)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="language-support"></a>Поддержка языков

Azure CLI (с помощью расширения) и Azure PowerShell (с помощью модуля) поддерживают Azure Resource Graph. Перед выполнением любого из следующих запросов убедитесь, что среда готова. В разделах об [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) приведены инструкции по установке и проверке выбранной вами среды оболочки.

## <a name="count-resources"></a>Подсчет ресурсов Azure

Этот запрос возвращает количество ресурсов Azure в доступных вам подписках. Этот запрос также удобно использовать, чтобы проверить, установлены ли в выбранной оболочке соответствующие компоненты Azure Resource Graph и работают ли они.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Вывод списка ресурсов, отсортированных по имени

Если не указан определенный тип ресурса или сопоставляемое свойство, этот запрос возвращает только **имя**, **тип** и **расположение** ресурсов Azure, но использует `order by`, чтобы отсортировать их по свойству **name** в порядке возрастания (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Отображение всех виртуальных машин, упорядоченных по имени в порядке убывания

Если вместо всех ресурсов Azure требуется получить только список виртуальных машин (относящихся к типу `Microsoft.Compute/virtualMachines`), то мы можем использовать свойство **type** в результатах.
Аналогично предыдущему запросу, `desc` изменяет `order by` для сортировки по убыванию. `=~` в сопоставлении типа указывает Resource Graph не учитывать регистр.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Отображение первых пяти виртуальных машин по имени и типу ОС

Этот запрос будет использовать `limit` для извлечения только пяти соответствующих записей, упорядоченных по имени. Типом ресурса Azure является `Microsoft.Compute/virtualMachines`. `project` указывает Azure Resource Graph, какие свойства следует включить.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Подсчет виртуальных машин по типу ОС

Основываясь на предыдущем запросе, мы по-прежнему ограничиваем результаты типом ресурса Azure `Microsoft.Compute/virtualMachines`, но больше не ограничиваем количество возвращаемых записей.
Вместо этого мы использовали `summarize` и `count()`, чтобы определить способ группирования и объединения значений по свойству (`properties.storageProfile.osDisk.osType` в этом примере). Чтобы получить представление о том, как эта строка выглядит в полном объекте, ознакомьтесь с [просмотром ресурсов и обнаружением виртуальных машин](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Этот запрос можно составить по-другому. Можно расширить (`extend`) свойство и присвоить ему временное имя для использования в запросе, в этом случае — **os**. Затем **os** используется для `summarize` и `count()`, как в предыдущем примере.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Имейте в виду, что, хотя `=~` позволяет сопоставление без учета регистра, использование свойств (таких как **properties.storageProfile.osDisk.osType**) в запросе требует соблюдения регистра. Если свойство указано в неправильном регистре, значение по-прежнему может возвращаться, но группирование или сведение данных будет выполняться неправильно.

## <a name="show-storage"></a>Отображение ресурсов, которые содержат хранилище

Вместо явного сопоставления определенного типа этот пример запроса будет искать все ресурсы Azure, содержащие (`contains`) слово **storage**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Вывод списка общедоступных IP-адресов

Аналогично предыдущему примеру, этот запрос ищет все ресурсы, тип которых содержит слово **publicIPAddresses**. Этот запрос расширяет данный шаблон, чтобы скрыть ресурсы, у которых **properties.ipAddress** имеет значение NULL, и возвращать только ресурсы с действительным значением **properties.ipAddress**. Кроме того, число результатов ограничено (`limit`) первыми 100. В зависимости от выбранной оболочки может потребоваться экранировать кавычки.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Подсчет ресурсов с IP-адресами, настроенными посредством подписки

Используя предыдущий пример запроса и добавив `summarize` и `count()`, мы можем получить список ресурсов с настроенными IP-адресами для подписки.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Вывод списка ресурсов с определенным значением тега

Мы можем ограничить результаты не только по типу ресурса Azure, а, например, по тегу. В этом примере мы применяем фильтр для получения ресурсов Azure с тегом **Environment** со значением **Internal**.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name"
```

Если необходимо также получить другие теги такого ресурса и их значения, пример можно расширить, добавив свойство **tags** в `project` ключевое слово.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Вывод списка всех учетных записей хранения с определенным значением тега

Совмещая фильтрацию из предыдущего примера с фильтрацией по типу ресурса Azure с помощью свойства **type**, мы можем уточнить поиск, чтобы искать определенные типы ресурсов Azure с определенным тегом и его значением.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> В этом примере для сопоставления используется условный оператор `==` вместо `=~`. При использовании `==` учитывается регистр.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [языке запросов](../concepts/query-language.md).
- Узнайте больше о [просмотре ресурсов](../concepts/explore-resources.md).
- Изучите примеры в разделе [Усложненные запросы](advanced.md).