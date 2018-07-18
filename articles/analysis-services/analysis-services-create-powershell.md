---
title: Краткое руководство по созданию сервера Azure Analysis Services с помощью PowerShell | Документы Майкрософт
description: Узнайте, как создать сервер Azure Analysis Services с помощью PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9149f15d0503b9a39ac67d9c3f6fc1ddde7e03bd
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952772"
---
# <a name="quickstart-create-a-server---powershell"></a>Краткое руководство по созданию сервера с помощью PowerShell

В этом кратком руководстве объясняется, как с помощью PowerShell и командной строки создать сервер Azure Analysis Services в подписке Azure.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**: откройте ссылку на [бесплатную пробную версию Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), чтобы создать учетную запись.
- **Azure Active Directory**: ваша подписка должна быть связана с клиентом Azure Active Directory, а учетная запись настроена в этом каталоге. Дополнительные сведения см. в руководстве по [аутентификации и настройке пользовательских разрешений](analysis-services-manage-users.md).
- **Модуль Azure PowerShell 4.0 или более поздней версии**. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Чтобы выполнить установку или обновление, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="import-azurermanalysisservices-module"></a>Импорт модуля AzureRm.AnalysisServices

Чтобы создать сервер в подписке, используйте модуль компонентов [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Загрузите модуль AzureRm.AnalysisServices в сеансе PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Выполните инструкции на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

[Группа ресурсов Azure](../azure-resource-manager/resource-group-overview.md) — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. При создании сервера необходимо указать группу ресурсов в вашей подписке. Если у вас еще нет группы ресурсов, вы можете создать ее с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем `myResourceGroup` и в регионе "Западная часть США".

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Создание сервера

Создайте сервер с помощью команды [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). В следующем примере создается сервер с именем MyServer в группе myResourceGroup, в регионе "Западная часть США", на уровне D1 и указывается philipc@adventureworks.com в качестве администратора сервера.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить сервер из подписки, используйте команду [AzureRmAnalysisServicesServer удаление](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Не удаляйте сервер, если вы планируете использовать другие шаблоны для быстрого начала работы и руководства в этой коллекции. В следующем примере показано, как удалить сервер, созданный на предыдущем шаге.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать сервер в своей подписке Azure с помощью PowerShell. Теперь, когда у вас есть сервер, вы можете защитить его, настроив брандмауэр сервера (необязательно). Можно также добавить базовый образец модели данных на сервер непосредственно с портала. Наличие образца модели полезно для изучения настройки ролей модели базы данных и тестирования клиентских подключений. Для получения дополнительных сведений перейдите к руководству по добавлению образца модели.

> [!div class="nextstepaction"]
> [Краткое руководство. Настройка брандмауэра сервера с помощью портала](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Руководство. Добавление образца модели на сервер](analysis-services-create-sample-model.md)