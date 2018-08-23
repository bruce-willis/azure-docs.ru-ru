---
title: Просмотр журналов контроллера Службы Azure Kubernetes (AKS)
description: Узнайте, как включить и просмотреть журналы для главного узла Kubernetes в Службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 04afd71183bcb8001d017b0027f29338b8d67ddb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442373"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Включение и просмотр журналов главного узла Kubernetes в Службе Azure Kubernetes (AKS)

При использовании Службы Azure Kubernetes (AKS) основные компоненты, такие как *kube-apiserver* и *kube-controller-manager*, предоставляются как управляемая служба. Вы создадите узлы, где будут запущены *kubelet* и среда выполнения контейнера, и сможете управлять ими, а также развернете свои приложения через управляемый сервер API Kubernetes. Чтобы устранить неполадки в приложении и службах, вам может потребоваться просмотреть журналы, созданные этими основными компонентами. В этой статье описано, как использовать Azure Log Analytics для включения и запроса журналов из основных компонентов Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей у вас должен быть кластер AKS, выполняющийся в учетной записи Azure. Если у вас еще нет кластера AKS, создайте его с помощью [Azure CLI][cli-quickstart] или [портала Azure][portal-quickstart]. Log Analytics работает в кластерах AKS с поддержкой и без поддержки RBAC.

## <a name="enable-diagnostics-logs"></a>Включение журналов диагностики

Чтобы собрать и просмотреть данные из нескольких источников, Log Analytics предоставляет язык запросов и механизм аналитики, обеспечивающий когнитивные аналитические сведения о среде. Рабочая область используется для сопоставления и анализа данных и может интегрироваться с другими службами Azure, такими как Application Insights и центр безопасности Azure. Чтобы использовать другую платформу для анализа журналов, вы можете отправить журналы диагностики в учетную запись хранения Azure или концентратор событий. Дополнительные сведения см. в статье [Что такое служба Log Analytics в Azure?][log-analytics-overview]

Включение Log Analytics и управление ею выполняется на портале Azure. Чтобы включить сбор журналов для основных компонентов Kubernetes в своем кластере AKS, откройте портал Azure в веб-браузере и выполните шаги ниже.

1. Выберите группу ресурсов для кластера AKS, например *myResourceGroup*. Не выбирайте группу ресурсов, которая содержит отдельные ресурсы кластера AKS, например *MC_myResourceGroup_myAKSCluster_eastus*.
1. В области слева выберите **Параметры диагностики**.
1. Выберите свой кластер AKS, например *myAKSCluster*, а затем — **Включить диагностику**.
1. Введите имя, например *myAKSLogs*, а затем выберите **Отправить в Log Analytics**.
    * Выберите команду *настройки* Log Analytics, а затем — имеющуюся рабочую область или щелкните **Создать рабочую область**.
    * Если вам нужно создать рабочую область, укажите имя, группу ресурсов и расположение.
1. В списке доступных журналов выберите журналы, которые нужно включить, например *kube-apiserver*, *kube-controller-manager* и *kube-scheduler*. Когда служба Log Analytics будет включена, вы сможете вернуть или изменить собранные журналы.
1. Когда настройка будет завершена, нажмите кнопку **Сохранить**, чтобы включить сбор выбранных журналов.

На следующем снимке экрана портала показано окно *параметров диагностики* и параметр создания рабочей области OMS:

![Включение рабочей области OMS Log Analytics кластера AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Планирование запуска тестового модуля pod в кластере AKS

Чтобы создать несколько журналов, создайте модуль pod в своем кластере AKS. Следующий пример манифеста YAML можно использовать для создания базового экземпляра NGINX. Создайте файл с именем `nginx.yaml` в удобном редакторе и вставьте следующее содержимое:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: myfrontend
    image: nginx
    ports:
    - containerPort: 80
```

Создайте модуль pod с помощью команды [kubectl create][kubectl-create] и укажите свой файл YAML, как показано в следующем примере:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Просмотр собранных журналов

Включение журналов диагностики и их появление в рабочей области OMS может занять несколько минут. На портале Azure выберите группу ресурсов для рабочей области Log Analytics, например *myResourceGroup*, а затем выберите ресурс Log Analytics, например *myAKSLogs*.

![Выбор рабочей области Log Analytics для кластера AKS](media/view-master-logs/select-log-analytics-workspace.png)

В области слева выберите **Поиск по журналу**. Чтобы просмотреть *kube-apiserver*, введите следующий запрос в текстовом поле:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| project log_s
```

Многие журналы возвращаются для сервера API. Чтобы сузить запрос до просмотра журналов о модуле pod NGINX, созданном на предыдущем шаге, добавьте дополнительную инструкцию *where* для поиска *pods/nginx*, как показано в следующем примере запроса:

```
search *
| where Type == "AzureDiagnostics"
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Появятся конкретные журналы для модуля pod NGINX, как показано в следующем примере снимка экрана:

![Результаты запроса Log Analytics для примера модуля pod NGINX](media/view-master-logs/log-analytics-query-results.png)

Чтобы просмотреть дополнительные журналы, вы можете обновить запрос, указав для имени *категории* *kube-controller-manager* или *kube-scheduler*, в зависимости от того, какие дополнительные журналы вы активируете. Затем вы можете использовать дополнительные инструкции *where* для уточнения событий, которые вы ищете.

Дополнительные сведения о запросе и фильтрации данных журнала см. в статье [Просмотр и анализ данных, собранных с помощью поиска по журналам Log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Схема событий журналов

Чтобы проанализировать данные журнала, в следующей таблице представлена схема, используемая для каждого события:

| Имя поля               | ОПИСАНИЕ |
|--------------------------|-------------|
| *resourceId*             | Ресурс Azure, с помощью которого был создан журнал |
| *time*                   | Метка времени — время передачи журнала |
| *category*               | Имя контейнера или компонента, генерирующего журнал |
| *operationName*          | Всегда *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *properties.log*         | Полный текст журнала из компонента |
| *properties.stream*      | *stderr* или *stdout* |
| *properties.pod*         | Имя модуля pod, из которого получен журнал |
| *properties.containerID* | Идентификатор контейнера Docker, из которого получен этот журнал |

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы узнали, как включить и просмотреть журналы для основных компонентов Kubernetes в своем кластере AKS. Для выполнения дальнейших действий мониторинга и устранения неполадок вы также можете [просмотреть журналы Kubelet][kubelet-logs] и [разрешить доступ к узлу SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md