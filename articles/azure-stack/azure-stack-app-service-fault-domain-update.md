---
title: Службы приложений Azure в Azure Stack. Обновление домена сбоя | Документация Майкрософт
description: Распространение Службы приложений Azure в доменах сбоя Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130376"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Распространение Службы приложений Azure в доменах сбоя Azure Stack

*Область применения: интегрированные системы Azure Stack*

Начиная с обновления 1802, Azure Stack поддерживает распределение рабочих нагрузок между доменами сбоя. Эта возможность крайне важна для обеспечения высокого уровня доступности.

>[!IMPORTANT]
>Чтобы включить поддержку доменов сбоя, необходимо обновить интегрированную систему Azure Stack до версии 1802. Этот документ применим только к развертываниям поставщика ресурсов службы приложений, созданным до обновления 1802. Если вы развертывали свою службу приложений в Azure Stack, к которому уже применено обновление 1802, поставщик ресурсов уже распределен между доменами сбоя.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Распределение поставщика ресурсов службы приложений по доменам сбоя

Чтобы повторно распределить масштабируемые наборы, развернутые для поставщика ресурсов службы приложений, необходимо выполнить описанные в этой статье действия для каждого масштабируемого набора. По умолчанию масштабируемые наборы имеют следующие имена:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Если в некоторых из этих масштабируемых наборов у вас нет развернутых экземпляров, повторную балансировку для них выполнять не нужно. Масштабируемые наборы будут правильно сбалансированы, когда вы в будущем примените к ним масштабирование.

Чтобы развернуть масштабируемый набор, сделайте следующее:

1. Войдите на портал администратора Azure Stack.
2. Выберите **Больше служб**.
3. В разделе "Вычислительные ресурсы" выберите **Масштабируемые наборы виртуальных машин**. Здесь указаны все существующие масштабируемые наборы, развернутые одновременно со службой приложений, а также число экземпляров в них. На следующем снимке экрана показан пример масштабируемых наборов.

      ![Список масштабируемых наборов виртуальных машин Microsoft Azure в пользовательском интерфейсе масштабируемых наборов виртуальных машин][1]

4. Выполните горизонтальное масштабирование для каждого набора. Например, если в масштабируемом наборе у вас есть три существующих экземпляра, увеличьте число экземпляров в нем до шести, чтобы развернуть в доменах сбоя три новых экземпляра. В следующем примере PowerShell показано, как выполнить горизонтальное масштабирование масштабируемого набора.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Этот процесс может занять несколько часов в зависимости от типа роли и числа экземпляров.

5. Состояние новых экземпляров роли можно отслеживать в разделе **Роли администрирования для службы приложений**. Чтобы проверить состояние экземпляра роли, выберите тип роли в списке.

    ![Служба приложений Azure для ролей Azure Stack][2]

6. Когда все новые экземпляры перейдут в состояние **Готово**, вернитесь в раздел **Масштабируемый набор виртуальных машин** и **удалите** старые экземпляры роли.

7. Повторите эти шаги для **каждого** масштабируемого набора виртуальных машин.

## <a name="next-steps"></a>Дополнительная информация

Можно также попробовать другие [службы PaaS (платформа как услуга)](azure-stack-tools-paas-services.md).

* [Use SQL databases on Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) (Использование баз данных SQL в Microsoft Azure Stack)
* [Use MySQL databases on Microsoft Azure Stack](azure-stack-mysql-resource-provider-deploy.md) (Использование баз данных MySQL в Microsoft Azure Stack)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
