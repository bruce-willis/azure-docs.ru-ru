---
title: Выполните первый запрос графика ресурсов с помощью Azure PowerShell
description: Эта статья поможет выполнить шаги по включению модуля "График ресурсов" для Azure PowerShell и выполнению первого запроса.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7a706c65fbdd64103854b02e891c96cbf927f8a1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962544"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Выполните первый запрос графика ресурсов с помощью Azure PowerShell

Первым шагом при использовании графика ресурсов Azure необходимо убедиться, что установлен модуль для Azure PowerShell. В этом кратком руководстве описывается процесс добавления модуля к установке Azure PowerShell. Можно использовать модуль Azure PowerShell, установленный локально или через [Azure Cloud Shell](https://shell.azure.com).

В конце этого процесса будет добавлен модуль к установке Azure PowerShell, по выбору и выполнению самого первого запроса графика ресурсов.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="add-the-resource-graph-module"></a>Добавить модуль "График ресурсов"

Чтобы позволить Azure PowerShell выполнять запрос графических ресурсов Azure, необходимо добавить модуль. Этот модуль работает везде, где может использоваться оболочка Azure PowerShell, включая [Cloud Shell](https://shell.azure.com) (как автономно, так и внутри портала), [образ Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/) или установленную локально.

1. Убедитесь, что установлена Azure PowerShell версии 6.3.0 или более поздней. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-azurerm-ps).

1. Убедитесь, что установлена PowerShellGet. Если она еще не установлена или не обновлена, выполните [эти инструкции](/powershell/gallery/installing-psget).

1. Запустите следующие команды из командной строки PowerShell **с правами администратора**.

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module AzureRm.ResourceGraph
   ```

1. Проверьте, что модуль был импортирован и имеет требуемую версию (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported AzureRm.Graph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Выполните первый запрос график ресурсов

Теперь, когда модуль PowerShell для Azure был добавлен в выбранную среду, настала пора попробовать простой запрос графика ресурсов. Запрос возвращает первые пять ресурсов Azure с **Именем** и **Типом ресурса** для каждого ресурса.

1. Запустите ваш первый запрос графика ресурсов Azure, используя командлет `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Так как этот пример запроса не меняет сортировку, например `order by`, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса.

1. Обновите запрос свойством `order by` **Имя**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Как и с первым запросом, выполнение этого запроса несколько раз может получить различные наборы ресурсов для каждого запроса. Важен порядок команд запроса. В этом примере `order by` следует после `limit`. Это сначала ограничивает результаты запроса, а затем располагает их в нужном порядке.

1. Обновите запрос сначала `order by` свойства **Имя**, а затем `limit` 5 лучшими результатами:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Когда окончательный запрос выполняется несколько раз, при условии, что ничего в вашей среде не изменяется, возвращаемые результаты будут последовательны и ожидаемы — упорядочены по свойству **Имя** и ограничены 5 лучшими результатами.

## <a name="clean-up"></a>Очистка

Если вы хотите удалить модуль "График ресурсов" из среды Azure PowerShell, это можно сделать с помощью следующей команды:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Это не приводит к удалению скачанного ранее файла модуля. Он удаляется только из открытого сеанса PowerShell.

## <a name="next-steps"></a>Дополнительная информация

- Получите более подробную информацию о [языке запросов](./concepts/query-language.md)
- Подробнее о [просмотре ресурсов](./concepts/explore-resources.md)
- Выполните первый запрос с помощью [Azure CLI](first-query-azurecli.md)
- См. примеры в разделе [начальных запросов](./samples/starter.md)
- См. примеры в разделе [продвинутых запросов](./samples/advanced.md)