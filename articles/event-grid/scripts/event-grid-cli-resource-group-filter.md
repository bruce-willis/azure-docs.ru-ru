---
title: Пример скрипта Azure CLI. Подписка на события группы ресурсов и фильтрация по ресурсу | Документация Майкрософт
description: Пример скрипта Azure CLI. Подписка на события группы ресурсов и фильтрация по ресурсу
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: 4c3ac2eed6d7304be136fb20e7d9b8322658159a
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39482572"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Подписка на события группы ресурсов и фильтрация событий для ресурса с использованием Azure CLI

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с группой ресурсов. Он использует фильтр, чтобы получить события для указанного ресурса в группе ресурсов.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

```azurecli
#!/bin/bash

# Provide an endpoint for handling the events.
myEndpoint="<endpoint URL>"

# Select the Azure subscription that contains the resource group.
az account set --subscription "Contoso Subscription"

# Get the resource ID to filter events
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint $myEndpoint \
  --subject-begins-with $resourceId
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | создание подписки в службе "Сетка событий"; |


## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в разделе [Запрос к подпискам службы "Сетка событий Azure"](../query-event-subscriptions.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
