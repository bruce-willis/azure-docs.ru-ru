---
title: Пакеты SDK для службы "Сетка событий Azure"
description: Описывает пакеты SDK для службы "Сетка событий Azure". Эти пакеты SDK обеспечивают управление, публикацию и использование.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 06/29/2018
ms.author: tomfitz
ms.openlocfilehash: 3c085074863aa166a5766116b6c63b7dc341ad96
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130841"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Пакеты SDK для управления службой "Сетка событий Azure" и публикации в ней

В службе "Сетка событий Azure" доступны пакеты SDK, которые позволяют управлять ресурсами и публиковать события с помощью программных средств.

## <a name="management-sdks"></a>Пакеты SDK для управления

Пакеты SDK для управления позволяют создавать, обновлять и удалять разделы и подписки сетки событий. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Пакеты SDK для плоскости данных

Пакеты SDK для плоскости данных позволяют публиковать события в разделы. Эти пакеты обеспечивают аутентификацию, формирование события и асинхронную публикацию на указанной конечной точке. Они также позволяют использовать события первой стороны. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Дополнительная информация

* Примеры приложений см. на странице [с примерами кода для Сетки событий](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).
* Команды Azure CLI для службы "Сетка событий Azure" см. в статье об [Azure CLI](/cli/azure/eventgrid).
* Команды PowerShell для службы "Сетка событий Azure" см. в статье об [PowerShell](/powershell/module/azurerm.eventgrid).
