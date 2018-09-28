---
title: Обзор Azure Resource Graph
description: Azure Resource Graph — это служба в Azure, которая позволяет выполнять сложные запросы ресурсов в нужном масштабе.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bcfee71af25ccb996f9467922aa38cabe2b252ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959025"
---
# <a name="what-is-azure-resource-graph"></a>Что такое Azure Resource Graph

Azure Resource Graph — это служба в Azure, призванная расширить управление ресурсами Azure за счет действенных средств для оценки ресурсов и возможности масштабных запросов ко всем подпискам и группам управления, которые позволят вам эффективно контролировать вашу среду. Такие запросы предоставляют следующие возможности:

- Возможность запрашивать ресурсы, используя сложные фильтры, группирование и сортировку по свойствам ресурсов.
- Возможность многократно оценивать ресурсы с учетом нормативных требований и конвертировать результирующее выражение в определение политики.
- Возможность оценить эффект от применения политик в обширной облачной среде.

В этой документации мы рассмотрим каждую из этих возможностей подробно.

> [!NOTE]
> Azure Resource Graph используется новой функцией поиска "Все ресурсы" на портале Azure. Она предназначена для клиентов, которым нужно управлять масштабными средами.

## <a name="how-does-azure-resource-graph-complement-azure-resource-manager"></a>Как Azure Resource Graph дополняет Azure Resource Manager

На данный момент Azure Resource Manager отправляет данные в кэш с ограниченными ресурсами, предоставляющий несколько полей ресурсов: имя ресурса, идентификатор, тип, группа ресурсов, подписки и расположение. Сейчас, чтобы работать с дополнительными свойствами ресурсов, нужно вызывать каждый отдельный поставщик ресурсов и запрашивать данные свойств для каждого ресурса.

Azure Resource Graph дает вам доступ к свойствам, возвращаемым поставщиками ресурсов, без необходимости вызывать каждый поставщик ресурсов отдельно.

## <a name="the-query-language"></a>Язык запросов

Теперь, когда вы разобрались, что такое Azure Resource Graph, рассмотрим порядок создания запросов.

Важно понимать, что язык запросов Azure Resource Graph похож на [язык запросов Kusto](https://docs.loganalytics.io/docs/Language-Reference/) (KQL), но все же отличается от него.

Сведения об операциях и функциях, которые можно использовать с Azure Resource Graph, см. в статье [Язык запросов Resource Graph](./concepts/query-language.md). Информацию о ресурсах см. в статье о [просмотре ресурсов](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Разрешения в Azure Resource Graph

Чтобы использовать Resource Graph, необходимо авторизоваться через [управление доступом на основе ролей](../../role-based-access-control/overview.md) (RBAC), получив, как минимум, доступ на чтение к ресурсам, которые вы планируете запрашивать. Если у вас нет разрешений `read` в отношении группы управления, подписки, группы ресурсов или отдельного ресурса, они не будут включаться в результаты запроса Resource Graph.

## <a name="running-your-first-query"></a>Выполнение первого запроса

Resource Graph поддерживает как Azure CLI, так и Azure PowerShell. Компонент запроса имеет одинаковую структуру независимо от того, какой используется язык. Поддержка Azure Resource Graph еще не доступна по умолчанию ни в одном из пакетов SDK, так что для предоставления необходимых команд необходимо загрузить расширение или модуль.
Узнайте, как включить Resource Graph в [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) и [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Дополнительная информация

- Выполните первый запрос с помощью [Azure CLI](first-query-azurecli.md)
- Выполните первый запрос с помощью [Azure PowerShell](first-query-powershell.md)
- Начните с [начальных запросов](./samples/starter.md)
- Переходите к [расширенным запросам](./samples/advanced.md)