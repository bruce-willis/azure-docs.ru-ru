---
title: Подключение Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье объясняется, как подключить и настроить Azure Monitor для контейнеров, чтобы получать сведения о работе контейнера и выявленных проблемах с его производительностью.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 6b1de03814017ca4eb811362b2aaf5ac24f5ea86
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999806"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Подключение Azure Monitor для контейнеров
В этой статье объясняется, как настроить Azure Monitor для контейнеров, чтобы отслеживать производительность рабочих нагрузок, развернутых в средах Kubernetes и размещенных в [Службе Azure Kubernetes](https://docs.microsoft.com/azure/aks/).

## <a name="prerequisites"></a>Предварительные требования 
Чтобы начать, у вас должны быть следующие компоненты:

- Новый или имеющийся кластер AKS.
- Версия контейнерного агента Log Analytics для Linux: microsoft/oms:ciprod04202018 или более поздняя. Номер версии представлен датой в формате: *ммддгггг*. Агент устанавливается автоматически во время подключения этого компонента. 
- Рабочая область Log Analytics. Ее можно создать при включении мониторинга нового кластера AKS. Кроме того, средство подключения может создать рабочую область по умолчанию в стандартной группе ресурсов подписки кластера AKS. Если вы хотите создать ее самостоятельно, используйте [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Пользователь с ролью "Участник" Log Analytics, позволяющий включить мониторинг контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Компоненты 

Возможность отслеживать производительность зависит от контейнерного агента Log Analytics для Linux, который собирает данные о событиях и производительности из всех узлов в кластере. Этот агент автоматически развертывается и регистрируется в указанной рабочей области Log Analytics после включения мониторинга контейнеров. 

>[!NOTE] 
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью Azure CLI или предоставив шаблон Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента. 
>

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
Войдите на [портале Azure](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Включение мониторинга для нового кластера
Во время развертывания вы можете включить мониторинг нового кластера AKS на портале Azure или с помощью Azure CLI. Чтобы включить мониторинг на портале, следуйте указаниям в статье [Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)](../aks/kubernetes-walkthrough-portal.md). На странице **Мониторинг** для параметра **Включить мониторинг** выберите **Да**, а затем создайте рабочую область Log Analytics или выберите имеющуюся. 

Чтобы включить мониторинг нового кластера AKS, созданного с помощью Azure CLI, следуйте указаниям в разделе [Создание кластера AKS](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.43 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

После включения мониторинга и успешного выполнения всех задач настройки можно отслеживать производительность кластера одним из двух способов:

* Непосредственно в кластере AKS, выбрав **Работоспособность** в левой области.
* Выбрав плитку **Monitor Container insights** (Мониторинг аналитических сведений о контейнере) на странице кластеров AKS для выбранного кластера. В Azure Monitor выберите **Работоспособность** в левой области. 

  ![Параметры для выбора Azure Monitor для контейнеров в AKS](./media/monitoring-container-insights-onboard/kubernetes-select-monitoring-01.png)

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Включение мониторинга для существующих управляемых кластеров
Включить мониторинг уже развернутого кластера AKS можно с помощью Azure CLI, портала или шаблона Azure Resource Manager, используя командлет PowerShell `New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI
Ниже показано, как включить мониторинг кластера AKS с помощью Azure CLI. В этом примере вы не обязаны предварительно создавать или указывать имеющуюся рабочую область. Эта команда упрощает процесс включения за счет создания рабочей области по умолчанию в стандартной группе ресурсов подписки кластера AKS, если эта область еще не создана в регионе.  Имя созданной рабочей области по умолчанию имеет формат *DefaultWorkspace-<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Результат должен выглядеть так:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-from-azure-monitor"></a>Включение мониторинга из Azure Monitor
Чтобы включить мониторинг кластера AKS на портале Azure из раздела Azure Monitor, выполните следующие действия:

1. На портале Azure выберите **Монитор**. 
2. В списке выберите **Контейнеры (предварительная версия)**.
3. На странице **Контейнеры (предварительная версия)** выберите **Non-monitored clusters** (Неотслеживаемые кластеры).
4. В списке неотслеживаемых кластеров выберите контейнер и щелкните **Включить**.   
5. Если рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Container Health and Logs** (Подключение к службе работоспособности контейнеров и журналам).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке. 

    ![Включение мониторинга аналитических сведений о контейнере AKS](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Если вы хотите создать рабочую область Log Analytics для хранения данных мониторинга из кластера, выполните инструкции, описанные в статье [Создание рабочей области Log Analytics на портале Azure](../log-analytics/log-analytics-quick-create-workspace.md). Создайте рабочую область в той же подписке, в которой развернут контейнер AKS. 
 
После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>Включение мониторинга из кластера AKS на портале
Чтобы включить мониторинг контейнера AKS с помощью портала Azure, сделайте следующее:

1. На портале Azure щелкните **Все службы**. 
2. В списке ресурсов введите **Контейнеры**.  
    Как только вы начнете вводить данные, список отфильтруется соответствующим образом. 
3. Выберите **Службы Kubernetes**.  

    ![Ссылка на Службы Kubernetes](./media/monitoring-container-insights-onboard/portal-search-containers-01.png)

4. В списке контейнеров выберите нужный контейнер.
5. На странице обзора контейнеров выберите **Работоспособность контейнера монитора**.  
6. Если имеющаяся рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Onboarding to Container Health and Logs** (Подключение к службе работоспособности контейнеров и журналам).  
    В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое контейнер AKS развертывается в подписке. 

    ![Включение мониторинга работоспособности контейнера AKS](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-02.png)

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
        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]", 
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
                        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
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
           "value": "/subscriptions/<SubscriptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. Замените значения **aksResourceId** и **aksResourceLocation** значениями, которые можно найти на странице **обзора AKS** кластера AKS. Значение для **workspaceResourceId** — это полный идентификатор ресурса рабочей области Log Analytics, который включает имя рабочей области. Также укажите расположение рабочей области для **workspaceRegion**. 
5. Сохраните этот файл как **existingClusterParam.json** в локальную папку.
6. Теперь вы можете развернуть этот шаблон. 

    * Выполните следующие команды PowerShell в папке с шаблоном:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Выполните следующую команду с помощью Azure CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

        ```azurecli
        provisioningState       : Succeeded
        ```
После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

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

Чтобы проверить, правильно ли развернут агент Log Analytics версии более ранней, чем *06072018*, выполните следующую команду:  

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Просмотр конфигурации с помощью CLI
С помощью команды `aks show` можно получить определенные сведения, например включено ли решение, идентификатор ресурса рабочей области Log Analytics, а также сводные данные о кластере.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

Через несколько минут выполнение команды завершается и отображаются сведения о решении в формате JSON.  В результатах выполнения команды должен отобразиться профиль надстройки наблюдения. Вы должны увидеть результат, аналогичный следующему:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>Дополнительная информация

* Если при попытке подключить решение у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](monitoring-container-insights-troubleshoot.md).

* Если включен мониторинг для сбора метрик работоспособности узлов и контейнеров pod кластера AKS, эти метрики доступны на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в статье о [просмотре состояния работоспособности Службы Azure Kubernetes](monitoring-container-insights-analyze.md).