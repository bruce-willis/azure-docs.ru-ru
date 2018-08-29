---
title: Использование мониторинга и ожидаемые затраты в Azure Monitor
description: Обзор процесса использования страницы использования и ожидаемых затрат в Azure Monitor
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.component: ''
ms.openlocfilehash: 35e7d36043defd236252f86facf4b9e2ed945d67
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42143720"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Мониторинг использования и ожидаемых затрат

> [!NOTE]
> В этой статье описывается просмотр использования и ожидаемых затрат в различных функциях мониторинга Azure для разных моделей ценообразования.  Дополнительные сведения приведены в статьях ниже.
> - В статье [Управление затратами на хранение путем регулирования объема и срока хранения данных в Log Analytics](../log-analytics/log-analytics-manage-cost-storage.md) описано, как контролировать затраты, изменяя срок хранения данных.
> - В статье [Анализ использования данных в службе Log Analytics](../log-analytics/log-analytics-usage.md) описано, как анализировать и оповещать об использовании данных.
> - В статье [Управление ценами и объемом данных в Application Insights](../application-insights/app-insights-pricing.md) описано, как анализировать использование данных в Application Insights.

На странице **Usage and estimated costs** (Использование и ожидаемые затраты) в центре мониторинга на портале Azure объясняется, как использовать такие основные функции мониторинга, как [оповещения, метрики и уведомления](https://azure.microsoft.com/pricing/details/monitor/), [Служба анализа журналов Azure](https://azure.microsoft.com/pricing/details/log-analytics/) и [Azure Log Analytics](https://azure.microsoft.com/pricing/details/application-insights/). Для клиентов с тарифными планами, доступными до апреля 2018 года, это также включает использование службы Log Analytics, приобретенной в соответствии с предложением "Анализ и аналитика".

На этой странице пользователи могут просматривать использование этих ресурсов за последние 31 день, выведенное для каждой подписки. А также перейти к представлению тенденции за этот период. Для этой оценки необходимо собрать много данных, поэтому наберитесь терпения при загрузке страницы.

Этот пример показывает мониторинг использования и оценку возникших затрат:

![Снимок экрана: страница использования и ожидаемых затрат на портале](./media/monitoring-usage-and-estimated-costs/001.png)

Чтобы открыть диаграмму, которая показывает тенденции использования в течение последних 31 дня, выберите ссылку в столбце ежемесячного потребления.

![Снимок экрана: "Включено на каждый узел линейчатой диаграммы"](./media/monitoring-usage-and-estimated-costs/002.png)

Вот другая аналогичная сводка по использованию и затратам. В этом примере показана подписка в новой модели ценообразования на основе потребления, выпущена в апреле 2018 г. Обратите внимание, что выставление счетов на основе узла отсутствует. А сведения о приеме и хранении данных в Log Analytics и Application Insights теперь отображаются в новом общем счетчике.

![Снимок экрана: страница использования и ожидаемых затрат на портале — апрель 2018 г.](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Новая модель ценообразования

В апреле 2018 года [была выпущена новая модель ценообразования](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Она обеспечивает удобную для облака ценовую политику, основанную на потреблении. Вы платите только за те ресурсы, которые используете, без обязательств по узлам. Подробные сведения о новой модели ценообразования доступны для [оповещений, метрик, уведомлений](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) и [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Для клиентов, подключающихся к Log Analytics или Application Insights после 2 апреля 2018 года, новая модель ценообразования является единственным доступным вариантом. Для клиентов, которые уже использовали эти службы, переход на новую модель ценообразования необязателен.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Оценка влияния новой модели ценообразования
Новая модель ценообразования будет по-разному влиять на каждого клиента, исходя из мониторинга шаблонов использования. Для пользователей, которые использовали Log Analytics или Application Insights до 2 апреля 2018 г., на странице **Usage and estimated cost** (Данные об использовании и предполагаемые расходы) в Azure Monitor оцениваются любые изменения в стоимости при их переходе на новую модель ценообразования. Это предоставляет возможность перевести подписку на новую модель. Для большинства клиентов новая модель ценообразования будет выгодной. Однако это может быть не так для клиентов с особенно высокими показателями использования данных или в более дорогих регионах.

Чтобы просмотреть оценку затрат по выбранным подпискам на странице **Usage and estimated costs** (Использование и ожидаемые затраты), выберите синий баннер в верхней части страницы. Лучше всего просматривать по одной подписке за раз, потому что это является уровнем, на котором может применяться новая модель ценообразования.

![Снимок экрана: мониторинг использования и ожидаемых затрат в новой модели ценообразования](./media/monitoring-usage-and-estimated-costs/004.png)

На новой странице отображается аналогичная версия этой страницы с зеленым баннером:

![Снимок экрана: мониторинг использования и ожидаемых затрат в текущей модели ценообразования](./media/monitoring-usage-and-estimated-costs/005.png)

На странице также отображается другой набор метрик, которые соответствуют новой модели ценообразования. Этот список является примером.

- "Анализ и аналитика\Избыточное на каждый узел",
- "Анализ и аналитика\Включено на каждый узел",
- "Application Insights\Избыточные данные — ценовая категория "Базовый",
- "Application Insights\Включенные данные",

Новая модель ценообразования не предусматривает основанных на узле распределений данных. Таким образом эти метрики приема данных объединяются в новую общую метрику приема данных **Общие службы\Прием данных**. 

Существует также другая возможность принятия данных в Log Analytics или Application Insights для регионов с более высокими затратами. Для регионов с более высокими затратами данные будут отображены с новыми региональными метриками. Например, **Data Ingestion (US West Central)** (Прием данных (центрально-западная часть США)).

> [!NOTE]
> Ожидаемые затраты на подписку не влияют на назначение данных на узел уровня учетной записи в подписке Operations Management Suite (OMS). Обратитесь к своему представителю учетной записи, чтобы более подробно рассмотреть новую модель ценообразования для этого случая.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Новая модель ценообразования и назначения в подписке Operations Management Suite

Клиенты, которые приобрели Microsoft Operations Management Suite E1 и E2, имеют право на назначение приема данных для [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) и [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Чтобы получить эти назначения для рабочих областей Log Analytics либо ресурсов Application Insights в конкретной подписке, необходимо следующее: 

- для этой подписки должна по-прежнему применяться модель ценообразования, используемая до апреля 2018 г;
- для рабочих областей Log Analytics должна использоваться ценовая категория "За узел (OMS)";
- для ресурсов Application Insights должен использоваться тарифный план "Корпоративный".

В зависимости от количества узлов в наборе, приобретенном организацией, переход некоторых подписок на новую модель ценообразования может быть выгодным, но это требует тщательного анализа. В целом, рекомендуем просто использовать ту же модель, что и до апреля 2018 года, как описано выше.

> [!WARNING]
> Если ваша организация приобрела Microsoft Operations Management Suite E1 и E2, обычно для подписок лучше всего использовать ту же модель ценообразования, что и до апреля 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Изменения при переходе на новую модель ценообразования

Новая модель ценообразования упрощает параметры ценообразования Log Analytics и Application Insights только для одного уровня (или плана). Перемещение подписки на новую модель ценообразования:

- изменит ценовую категорию для каждого экземпляра Log Analytics на новый уровень с оплатой за каждый гигабайт (pergb2018 в Azure Resource Manager);
- изменит все ресурсы Application Insights с планом "Корпоративный" на план "Базовый".

Оценка затрат показывает последствия этих изменений.

> [!WARNING]
> Здесь важно отметить, что если вы используете Azure Resource Manager или PowerShell для развертывания [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) или [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) в подписке, вам необходимо перейти на новую модель ценообразования. Если вы укажете уровень или план ценообразования, отличный от pergb2018 для Log Analytics или "Базовый" для Application Insights, ошибка развертывания из-за указания недопустимого уровня или плана ценообразования не появится, а развертывание выполнится успешно, **однако будет использован только допустимый уровень или план ценообразования** (это не относится к уровню "Бесплатный" для Log Analytics, где создается сообщение о недопустимом уровне ценообразования).
>

## <a name="moving-to-the-new-pricing-model"></a>Переход на новую модель ценообразования

Если необходимо внедрить новую модель ценообразования для подписки, выберите параметр **Выбор модели ценообразования** в верхней части страницы **Usage and estimated costs** (Использование и ожидаемые затраты):

![Снимок экрана: мониторинг использования и ожидаемых затрат в новой модели ценообразования](./media/monitoring-usage-and-estimated-costs/006.png)

Откроется страница **Выбор модели ценообразования**. На странице перечислены все подписки, просматриваемые на предыдущей странице:

![Снимок экрана выбора модели ценообразования](./media/monitoring-usage-and-estimated-costs/007.png)

Чтобы переместить подписку на новую модель ценообразования, установите флажок, а затем выберите **Сохранить**. Аналогичным образом можно вернуться к старой модели ценообразования. Имейте в виду, что для изменения модели ценообразования требуется разрешение владельца либо участника подписки.

## <a name="automate-moving-to-the-new-pricing-model"></a>Автоматизация перехода на новую модель ценообразования

В следующих сценариях требуется модуль Azure PowerShell. Чтобы узнать, как проверить наличие последней версии, см. раздел [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.1.0).

После установки ​​последней версии Azure PowerShell нужно запустить ``Connect-AzureRmAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Результатом значения True в разделе GrandFatherableSubscription является то, что модель ценообразования этой подписки можно переносить между разными моделями ценообразования. Пустое значение в группе optedInDate означает, что эта подписка настроена для старой модели ценообразования.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Чтобы перенести эту подписку для новой модели ценообразования, выполните:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Чтобы проверить, внесены ли изменения, запустите повторно:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

При успешном переносе результат должен выглядеть так:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

OptInDate теперь содержит метку времени, которая показывает, когда эта подписка была выбрана для новой модели ценообразования.

Если требуется вернуться к старой модели ценообразования, выполните:

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Если перезапустить предыдущий сценарий, который имеет ``-Action listmigrationdate``, отобразится пустое значение optedInDate, указывающее возврат подписки к прежней модели ценообразования.

Если у вас несколько подписок в одном клиенте, которые нужно перенести, можно создать собственный вариант, используя фрагменты следующих сценариев:

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzureRMSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Чтобы проверить пригодность всех подписок своего клиента для новой модели ценообразования, можно запустить:

```PowerShell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

Этот сценарий можно улучшить, создав сценарий, который генерирует три массива. Один массив будет состоять из всех идентификаторов подписок, у которых для ```isGrandFatherableSubscription``` задано значение True, а значение optedInDate в данный момент не имеет значения. Второй массив возможных подписок в данный момент на новой модели ценообразования. А третий массив заполняется идентификаторами подписки в клиенте, не подходящими для новой модели ценообразования:

```PowerShell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> В зависимости от количества подписок приведенных выше, выполнение сценария может занять некоторое время. Благодаря использованию метода .add() окно PowerShell будет повторять возрастающие значения по мере добавления элементов в каждый массив.

Теперь, когда подписки разделены на три массива, следует внимательно просмотреть результаты. Можете сделать резервную копию содержимого массивов, чтобы при необходимости легко вернуть изменения в будущем. Если нужно преобразовать все подходящие подписки, которые находятся в данный момент на старой модели ценообразования, в новую модель, эту задачу можно выполнить таким способом:

```PowerShell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```