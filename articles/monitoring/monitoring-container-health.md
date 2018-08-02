---
title: Мониторинг работоспособности Службы Azure Kubernetes (AKS) (предварительная версия) | Документация Майкрософт
description: В этой статье описывается, как можно легко просмотреть производительность контейнера AKS, чтобы быстро получить представление об использовании размещенной среды Kubernetes.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: magoedte
ms.openlocfilehash: 806487ec731a1b7fe02ccdfe6b285f5b2e119787
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249103"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Мониторинг работоспособности контейнеров Службы Azure Kubernetes (AKS) (предварительная версия)

В этой статье описывается, как настроить получение данных о работоспособности контейнера из Azure Monitor и использовать их для мониторинга производительности рабочих нагрузок, развернутых в средах Kubernetes, которые размещены в Службе Azure Kubernetes (AKS). Мониторинг кластера Kubernetes и контейнеров крайне важен, особенно если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

С помощью данных о работоспособности контейнера можно собирать данные метрик памяти и процессора из контроллеров, узлов и контейнеров, доступных в Kubernetes, используя API метрик. После включения службы работоспособности контейнеров эти метрики собираются автоматически с помощью контейнерной версии Агента Operations Management Suite (OMS) для Linux и сохраняются в вашей рабочей области [Log Analytics](../log-analytics/log-analytics-overview.md). Включенные предопределенные представления отображают размещенные в контейнерах рабочие нагрузки и факторы, влияющие на производительность кластера Kubernetes. Благодаря этому вы можете:  

* Определить, какие контейнеры запущены на узле, а также данные о среднем использовании их ресурсов процессора и памяти. Эта информация может помочь выявить узкие места ресурсов.
* Определить, где находится контейнер в контроллере или модуле pod. Эта информация может помочь просмотреть общую производительность контроллера или pod. 
* Просмотреть данные об использовании ресурсов рабочих нагрузок на узле, не относящихся к стандартным процессам, поддерживающим pod.
* Понять поведение кластера при средних и высоких нагрузках. Эта информация может помочь определить потребности в емкости, а также максимальную нагрузку, которую может выдержать кластер. 

Если вы заинтересованы мониторинге узлов контейнеров Docker и Windows и управлении ими для просмотра конфигурации, аудита и изучения данных об использовании ресурсов, ознакомьтесь с разделом [Решение для мониторинга контейнеров в Log Analytics](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Предварительные требования 
Чтобы начать, у вас должны быть следующие компоненты:

- Новый или имеющийся кластер AKS.
- Версия контейнерного Агента OMS для Linux: microsoft/oms:ciprod04202018 или более поздняя версия. Номер версии представлен датой в формате: *ммддгггг*. Этот агент устанавливается автоматически во время подключения службы работоспособности контейнеров. 
- Рабочая область Log Analytics. Ее можно создать при включении мониторинга нового кластера AKS. Ее можно также создать с помощью [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) или [портала Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Пользователь с ролью "Участник" Log Analytics, позволяющий включить мониторинг контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Компоненты 

Возможность отслеживать производительность зависит от контейнерного Агента OMS для Linux, который собирает данные о событиях и производительности из всех узлов в кластере. Этот агент автоматически развертывается и регистрируется в указанной рабочей области Log Analytics после включения мониторинга контейнеров. 

>[!NOTE] 
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью предоставленного шаблона Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента. 
>

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
Войдите на [портале Azure](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Включение мониторинга работоспособности контейнеров для нового кластера
На портале Azure во время развертывания вы можете включить мониторинг для нового кластера AKS. Следуйте указаниям в статье [Краткое руководство. Развертывание кластера Службы контейнеров Azure (AKS)](../aks/kubernetes-walkthrough-portal.md). На странице **Мониторинг** для параметра **Включить мониторинг** выберите **Да**, а затем создайте рабочую область Log Analytics или выберите имеющуюся. 

После включения мониторинга и успешного выполнения всех задач настройки можно отслеживать производительность кластера одним из двух способов:

* Непосредственно в кластере AKS, выбрав **Работоспособность** в левой области.
* Щелкнув элемент **Работоспособность контейнера монитора** на странице кластера AKS для выбранного кластера. В Azure Monitor выберите **Работоспособность** в левой области. 

  ![Параметры для выбора работоспособности контейнеров в AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

После включения мониторинга может потребоваться подождать около 15 минут, прежде чем вы сможете просмотреть операционные данные кластера. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Включение мониторинга работоспособности контейнеров для существующих управляемых кластеров
Включить мониторинг уже развернутого кластера AKS можно с помощью портала Azure или предоставленного шаблона Azure Resource Manager, используя командлет PowerShell `New-AzureRmResourceGroupDeployment` или Azure CLI. 

### <a name="enable-monitoring-in-the-azure-portal"></a>Включение мониторинга на портале Azure
Чтобы включить мониторинг контейнера AKS с помощью портала Azure, сделайте следующее:

1. На портале Azure щелкните **Все службы**. 
2. В списке ресурсов введите **Контейнеры**.  
    Как только вы начнете вводить данные, список отфильтруется соответствующим образом. 
3. Выберите **Службы Kubernetes**.  

    ![Ссылка на Службы Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. В списке контейнеров выберите нужный контейнер.
5. На странице обзора контейнеров выберите **Работоспособность контейнера монитора**.  
6. Если имеющаяся рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Container Health and Logs** (Подключение к службе работоспособности контейнеров и журналам).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке. 

    ![Включение мониторинга работоспособности контейнера AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../log-analytics/log-analytics-quick-create-workspace.md). Создайте рабочую область в той же подписке, в которой развернут контейнер AKS. 
 
После включения мониторинга может потребоваться подождать около 15 минут, прежде чем вы сможете просмотреть операционные данные кластера. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Включение мониторинга с помощью шаблона Azure Resource Manager
В этом методе используются два шаблона JSON. Один шаблон задает конфигурацию для включения мониторинга, а другой содержит значения параметров, которые можно настроить, чтобы указать следующее:

* идентификатор ресурса контейнера AKS; 
* группу ресурсов, в которой развернут кластер;
* рабочую область Log Analytics и регион для создания рабочей области. 

Рабочую область Log Analytics нужно создавать вручную. Для создания рабочей области можно использовать [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Сохраните этот файл как **existingClusterOnboarding.json** в локальную папку.
3. Вставьте в него следующий синтаксис JSON:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Замените значения **aksResourceId** и **aksResourceLocation** значениями, которые можно найти на странице **обзора AKS** кластера AKS. Значение для **workspaceResourceId** — это полный идентификатор ресурса рабочей области Log Analytics, который включает имя рабочей области. Также укажите расположение рабочей области для **workspaceRegion**. 
5. Сохраните этот файл как **existingClusterParam.json** в локальную папку.
6. Теперь вы можете развернуть этот шаблон. 

    * Выполните следующие команды PowerShell в папке с шаблоном:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI в Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

        ```azurecli
        provisioningState       : Succeeded
        ```
После включения мониторинга может потребоваться подождать около 15 минут, прежде чем вы сможете просмотреть операционные данные кластера. 

## <a name="verify-agent-and-solution-deployment"></a>Проверка развертывания агента и решения
В версии агента *06072018* и выше есть возможность удостовериться, что агент и решение были успешно развернуты. В более ранних версиях агента возможна только проверка развертывания агента.

### <a name="agent-version-06072018-or-later"></a>Версия агента 06072018 или более поздняя
Выполните следующую команду, чтобы проверить успешное развертывание агента. 

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Чтобы проверить развертывание решения, выполните следующую команду:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Агент версии более ранней, чем 06072018

Чтобы проверить, правильно ли развернут агент OMS версии более ранней, чем *06072018*, выполните следующую команду:  

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Просмотр данных об использовании производительности
При открытии страницы работоспособности контейнера можно сразу просмотреть данные об использовании производительности всего кластера. Просмотреть сведения о кластере AKS можно посредством четырех перспектив:

- HDInsight
- Nodes 
- Controllers;  
- Контейнеры

На вкладке **Кластер** на четырех диаграммах производительности показаны ключевые метрики производительности кластера. 

![Пример диаграммы производительности на вкладке "Кластер"](./media/monitoring-container-health/container-health-cluster-perfview.png)

На диаграмме производительности отображаются четыре метрики производительности:

- **Node CPU Utilization&nbsp;%** (Доля загрузки ЦП узла): статистическая перспектива загрузки ЦП для всего кластера. Результаты для диапазона времени можно отфильтровать, выбрав **Avg**, **Min**, **Max**, **50**, **90** и **95** в селекторе процентилей над диаграммой (по отдельности или вместе). 
- **Node memory utilization&nbsp;%** (Доля использования памяти узла): статистическая перспектива использования памяти для всего кластера. Результаты для диапазона времени можно отфильтровать, выбрав **Avg**, **Min**, **Max**, **50**, **90** и **95** в селекторе процентилей над диаграммой (по отдельности или вместе). 
- **Число узлов**: число узлов и сведения о состоянии, полученные из Kubernetes. Состояние узлов кластера может быть: *Все*, *Готово* и *Не готово*. Используя селектор над диаграммой, их можно отфильтровать по отдельности или вместе. 
- **Activity pod count** (Число модулей pod): число модулей pod и сведения о состоянии, полученные из Kubernetes. Состояние модулей pod может быть: *Все*, *Ожидание*, *Выполняется* и *Неизвестно*. Используя селектор над диаграммой, их можно отфильтровать по отдельности или вместе. 

Если перейти на вкладку **Узлы**, иерархия строк будет соответствовать объектной модели Kubernetes, начиная с узла в кластере. Разверните узел, и вы увидите один или несколько запущенных на нем модулей pod. Если в модуль pod сгруппировано несколько контейнеров, они будут отображаться как последняя строка в иерархии. Вы также можете увидеть количество рабочих нагрузок, отличных от модулей pod, которые выполняются на узле, если узел испытывает нехватку ресурсов процессора или памяти.

![Пример иерархии узлов Kubernetes в представлении производительности](./media/monitoring-container-health/container-health-nodes-view.png)

Можно выбрать контроллеры или контейнеры в верхней части страницы и просмотреть для них состояние и данные об использовании ресурсов. Раскрывающиеся списки в верхней области позволяют отфильтровать данные по пространству имен, службе и узлу. Если вместо этого требуется просмотреть данные об использовании памяти, из раскрывающегося списка **метрики** выберите **Memory RSS** (Память (RSS)) или **рабочий набор памяти**. Параметр **Memory RSS** (Память (RSS)) поддерживается только для Kubernetes версии 1.8 и более поздних. В противном случае значения **Min&nbsp;%** отображаются как *NaN&nbsp;%*. Это значение числового типа данных, представляющее неопределенное значение или значение, которое невозможно представить. 

![Представление производительности узлов контейнера](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

По умолчанию используются данные о производительности за последние шесть часов, но вы можете изменить этот период с помощью раскрывающегося списка **Диапазон времени**, который расположен в правом верхнем углу страницы. В настоящее время эта страница не обновляется автоматически, поэтому ее необходимо обновить вручную. Также в диапазоне времени можно отфильтровать результаты, выбрав **Avg**, **Min**, **Max**, **50**, **90** и **95** в селекторе процентилей. 

![Выбор процентилей для фильтрации данных](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

В следующем примере обратите внимание, что для узла *aks-nodepool-3977305* значение параметра **контейнеров** равно 5. Это сводное общее число развернутых контейнеров.

![Пример сводного числа контейнеров на узел](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Это поможет быстро выявить соответствующее распределение контейнеров между узлами в кластере. 

В следующей таблице описаны сведения, отображаемые при просмотре узлов.

| столбец | ОПИСАНИЕ | 
|--------|-------------|
| ИМЯ | Имя узла. |
| Status | Представление Kubernetes о состоянии узла. |
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Средний процент для узла на основе процентиля в течение выбранного периода времени. |
| Avg, Min, Max, 50, 90 | Среднее фактическое значение для узла на основе процентиля в течение выбранного периода времени. Среднее значение измеряется на основании лимита ЦП и памяти, установленного для узла. Для модулей pod и контейнеров это среднее значение, сообщаемое узлом. |
| Контейнеры | Число контейнеров. |
| Время доступности | Представляет время с момента запуска или перезапуска узла. |
| Controllers; | Только для контейнеров и модулей pod. Показывает, в каком контроллере размещен контейнер или pod. Не все модули pod будут размещаться в контроллере, поэтому для некоторых может отображаться значение **Н/Д**. | 
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Гистограмма тренда, представляющая процент метрики процентиля контроллера. |


Выберите **Контроллеры** в селекторе.

![Представление выбора контроллеров](./media/monitoring-container-health/container-health-controllers-tab.png)

Здесь можно просмотреть состояние производительности контроллеров.

![Представление производительности контейнеров <Имя>](./media/monitoring-container-health/container-health-controllers-view.png)

Иерархия строк начинается с контроллера, развернув который, можно увидеть один или несколько контейнеров. Разверните pod, и в последней строке отобразится контейнер, сгруппированный в этот pod. 

В следующей таблице описаны сведения, отображаемые при просмотре контроллеров.

| столбец | ОПИСАНИЕ | 
|--------|-------------|
| ИМЯ | Имя контроллера.|
| Status | Состояние сводного числа контейнеров после завершения выполнения, например *ОК*, *Завершено*, *Сбой*, *Остановлено* или *Приостановлено*. Если контейнер выполняется, но его состояние было неправильно представлено или не было выбрано агентом и не передавалось более 30 минут, то значением состояния будет *Неизвестно*. В таблице ниже приведены дополнительные сведения о значке состояния.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Сводное среднее значение среднего процента каждой сущности для выбранной метрики и процентиля. |
| Avg, Min, Max, 50, 90  | Сводное среднее значение производительности ЦП (в миллиядрах) или памяти для контейнера для выбранного процентиля. Среднее значение измеряется на основании лимита ресурсов ЦП и памяти, установленного для pod. |
| Контейнеры | Общее число контейнеров для контроллера или pod. |
| "Restarts" (Число перезапусков) | Сводное количество перезапусков из контейнеров. |
| Время доступности | Представляет время с момента запуска контейнера. |
| Узел | Только для контейнеров и модулей pod. Показывает, в каком контроллере размещен контейнер или pod. | 
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;%| Гистограмма тренда, представляющая метрику процентиля контроллера. |

Значки в поле состояния указывают состояние контейнеров:
 
| Значок | Status | 
|--------|-------------|
| ![Значок состояния выполнения (готовности)](./media/monitoring-container-health/container-health-ready-icon.png) | Выполняется (готово)|
| ![Значок состояния ожидания или паузы](./media/monitoring-container-health/container-health-waiting-icon.png) | Ожидается или приостановлено|
| ![Значок состояния последнего зарегистрированного выполнения](./media/monitoring-container-health/container-health-grey-icon.png) | Последнее выполнение зарегистрировано, но нет ответа более 30 минут|
| ![Значок состояния успешного выполнения](./media/monitoring-container-health/container-health-green-icon.png) | Успешно остановлено или не удалось остановить|

Значок состояния отображает число на основе сведений, предоставляемых модулем pod. Он показывает два худших состояния и, при наведении указателя мыши на значок состояния, сводный показатель состояния всех модулей pod в контейнере. Если это не состояние готовности, отображается значение состояния **(0)**. 

Выберите **Контейнеры** в селекторе.

![Представление выбора контейнеров](./media/monitoring-container-health/container-health-containers-tab.png)

Здесь можно просмотреть состояние производительности контейнеров.

![Представление производительности контейнеров <Имя>](./media/monitoring-container-health/container-health-containers-view.png)

В следующей таблице описаны сведения, отображаемые при просмотре контейнеров.

| столбец | ОПИСАНИЕ | 
|--------|-------------|
| ИМЯ | Имя контроллера.|
| Status | Состояния контейнеров (при наличии). В таблице ниже приведены дополнительные сведения о значке состояния.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Сводное значение среднего процента каждой сущности для выбранной метрики и процентиля. |
| Avg, Min, Max, 50, 90  | Сводное среднее значение производительности ЦП (в миллиядрах) или памяти контейнера для выбранного процентиля. Среднее значение измеряется на основании лимита ресурсов ЦП и памяти, установленного для pod. |
| Pod | Контейнер, в котором находится pod.| 
| Узел |  Узел, на котором размещен контейнер. | 
| "Restarts" (Число перезапусков) | Представляет время с момента запуска контейнера. |
| Время доступности | Представляет время с момента запуска или перезапуска контейнера. |
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Гистограмма тренда, представляющая средний процент показателей контейнера. |

Значки в поле состояния указывают состояния модулей pod в режиме реального времени, как описано в следующей таблице:
 
| Значок | Status | 
|--------|-------------|
| ![Значок состояния выполнения (готовности)](./media/monitoring-container-health/container-health-ready-icon.png) | Выполняется (готово)|
| ![Значок состояния ожидания или паузы](./media/monitoring-container-health/container-health-waiting-icon.png) | Ожидается или приостановлено|
| ![Значок состояния последнего зарегистрированного выполнения](./media/monitoring-container-health/container-health-grey-icon.png) | Последнее выполнение зарегистрировано, но ответа нет более 30 минут|
| ![Значок состояния "Завершено"](./media/monitoring-container-health/container-health-terminated-icon.png) | Успешно остановлено или не удалось остановить|
| ![Значок состояния "Сбой"](./media/monitoring-container-health/container-health-failed-icon.png) | Состояние сбоя |

## <a name="container-data-collection-details"></a>Сведения о сборе данных из контейнеров
Служба работоспособности контейнеров собирает данные различных метрик производительности и данные журналов из узлов контейнеров и контейнеров. Эти данные собираются каждые три минуты.

### <a name="container-records"></a>Записи контейнеров

В таблице ниже приведены примеры записей, собранных службой работоспособности контейнеров, и типов данных, которые отображаются в результатах поиска по журналам.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Список образов контейнеров | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Журнал контейнеров | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Журнал службы контейнеров | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Список узлов контейнеров | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Процесс контейнера | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Список модулей pod в кластере Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Список узлов кластера Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| События Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Службы в кластере Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Метрики производительности для узлов кластера Kubernetes | Perf &#124; where ObjectName == "K8SNode" | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Метрики производительности контейнеров кластера Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Поиск по журналам для анализа данных
Log Analytics помогает выявлять тренды, диагностировать узкие места, составлять прогнозы и сопоставлять данные, с помощью которых можно определить, оптимальна ли текущая конфигурация кластера. Вам доступны предопределенные запросы поиска по журналам, которые можно использовать без изменений или настроить для получения сведений нужным вам образом. 

Можно выполнить интерактивный анализ данных в рабочей области, выбрав параметр **Просмотреть журнал**, доступный справа на экране при развертывании контроллера или контейнера. Страница **поиска по журналам** появится над страницей портала Azure, которую вы использовали.

![Анализ данных в Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Выходные данные журналов контейнеров, которые передаются в Log Analytics: STDOUT и STDERR. Так как служба работоспособности контейнеров отслеживает среду Kubernetes (AKS) под управлением Azure, сейчас сведения о системе Kube не собираются ввиду большого объема создаваемых данных. 

### <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам
При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии со своими требованиями. Можно поэкспериментировать с приведенными ниже примерами запросов, чтобы научиться создавать более сложные запросы.

| Запрос | ОПИСАНИЕ | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Вывод всех сведений о жизненном цикле контейнера| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | События Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Инвентаризация образа | 
| **В службе "Расширенная аналитика" выберите графики**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Ресурсы ЦП контейнера. | 
| **В службе "Расширенная аналитика" выберите графики**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Память контейнера |

## <a name="how-to-stop-monitoring-with-container-health"></a>Как остановить мониторинг работоспособности контейнеров
Если после включения мониторинга контейнера AKS вы решили, что мониторинг больше не требуется, то вы можете *отказаться* от него с помощью предоставленных шаблонов Azure Resource Manager и командлета PowerShell **New-AzureRmResourceGroupDeployment** или Azure CLI. В одном шаблоне JSON указана конфигурация, чтобы *отказаться* от мониторинга. В другом содержатся значения параметров, которые можно настроить для указания идентификатора ресурса кластера AKS и группы ресурсов, в которой развернут кластер. 

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Создание и выполнение шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Сохраните этот файл как **OptOutTemplate.json** в локальной папке.
3. Вставьте в него следующий синтаксис JSON:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Замените значения **aksResourceId** и **aksResourceLocation** значениями кластера AKS, которые можно найти на странице **свойств** выбранного кластера.

    ![Страница свойств контейнера](./media/monitoring-container-health/container-properties-page.png)

    На странице **Свойства** также скопируйте **идентификатор ресурса рабочей области**. Это значение потребуется, если позже вы решите удалить рабочую область Log Analytics, что выходит за рамки этого процесса. 

5. Сохраните этот файл как **OptOutParam.json** в локальной папке.
6. Теперь вы можете развернуть этот шаблон. 

    * Выполните следующие команды PowerShell в папке с шаблоном:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Вот как можно выполнить следующую команду с помощью Azure CLI в Linux.

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Изменение конфигурации может занять несколько минут. По завершении выполнения вы увидите сообщение, подобное приведенному ниже, с возвращенным результатом:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Если рабочая область была создана только для мониторинга кластера и больше не требуется, ее необходимо удалить вручную. Если вам неизвестно, как удалить рабочую область, прочитайте раздел [Удаление рабочей области Azure Log Analytics на портале Azure](../log-analytics/log-analytics-manage-del-workspace.md). Не забывайте об **идентификаторе ресурса рабочей области**, скопированном ранее на шаге 4, он вам еще понадобится. 

## <a name="troubleshooting"></a>Устранение неполадок
В этом разделе представлены сведения об устранении неполадок при использовании службы работоспособности контейнеров.

Если служба работоспособности контейнеров была успешно включена и настроена, но вы не видите в Log Analytics сведения о состоянии или результаты при поиске по журналам, то вы можете выполнить приведенные ниже действия, чтобы диагностировать проблему. 

1. Проверьте состояние агента, выполнив следующую команду: 

    `kubectl get ds omsagent --namespace=kube-system`

    Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Проверьте состояние развертывания решения при использовании версии агента *06072018* или более поздней версии, выполнив следующую команду:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Проверьте состояние модуля pod, чтобы убедиться в его работоспособности. Выполните следующую команду: `kubectl get pods --namespace=kube-system`

    Выходные данные должны выглядеть, как в примере ниже, и содержать состояние omsagent *Running* (Выполняется).

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Проверьте журналы агента. При развертывании контейнерного агента он выполняет быструю проверку, используя команды OMI, и отображает версию агента и поставщика. 

5. Чтобы проверить успешное подключение агента, выполните следующую команду: `kubectl logs omsagent-484hw --namespace=kube-system`

    Должно отобразиться состояние, как в примере ниже.

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Дополнительная информация

Чтобы получить подробные сведения о работоспособности контейнеров и производительности приложений, [выполните поиск по журналам](../log-analytics/log-analytics-log-search.md). 
