---
title: Управлением кластером Azure Kubernetes с помощью пользовательского веб-интерфейса
description: Узнайте, как использовать панель мониторинга пользовательского веб-интерфейса с помощью Службы Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307931"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Подключение панели мониторинга Kubernetes с помощью Службы Azure Kubernetes (AKS)

Служба Kubernetes включает в себя веб-панель мониторинга, которая может использоваться для базовых операций управления. В этом документе рассматривается запуск панели мониторинга Kubernetes с помощью Azure CLI, а также некоторые основные операции с ней. Дополнительные сведения о панели мониторинга Kubernetes см. в статье [Web UI (Dashboard)][kubernetes-dashboard] (Панель мониторинга пользовательского веб-интерфейса).

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что создан кластер AKS и с ним установлено подключение `kubectl`. Если необходимо создать кластер AKS, см. раздел [AKS quickstart][aks-quickstart] (Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)).

Кроме того, нужно установить и настроить Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Запуск панели мониторинга Kubernetes

Выполните команду [az aks browse][az-aks-browse] для запуска панели мониторинга Kubernetes. В следующем примере создается панель мониторинга для кластера с именем *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Эта команда создает прокси-сервер между системой разработки и API Kubernetes и открывает панель мониторинга Kubernetes в веб-браузере.

### <a name="for-rbac-enabled-clusters"></a>Кластеры с поддержкой RBAC

Если кластер AKS использует RBAC, необходимо создать *ClusterRoleBinding*(связи кластерных ролей) прежде чем правильно подключиться к панели мониторинга. По умолчанию панель мониторинга Kubernetes развертывается с минимальными правами доступа на чтение и отображает сообщения об ошибках доступа RBAC. Сейчас панель мониторинга Kubernetes не поддерживает пользовательские учетные данные для определения уровня доступа. Вместо этого она использует роли, предоставленные учетной записи службы. Администратор кластера может предоставить дополнительный доступ к учетной записи службы *kubernetes-dashboard*, однако это может послужить вектором для эскалации привилегий. Вы также можете интегрировать аутентификацию Azure Active Directory, чтобы предоставить более детальный уровень доступа.

Чтобы создать связь, используйте команду [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding], как показано в следующем примере. 

> [!WARNING]
> Этот пример привязки не применяется к дополнительной проверке подлинности и может привести к небезопасному использованию. Панель мониторинга Kubernetes доступна для любого пользователя, имеющего доступ к URL-адресу. Не предоставляйте публичный доступ к панели мониторинга Kubernetes.
>
> Дополнительные сведения об использовании разных методов проверки подлинности см. в статье о панели мониторинга Kubernetes [Access control][dashboard-authentication](Контроль доступа).

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Теперь можно просматривать панели мониторинга Kubernetes в кластере с поддержкой RBAC. Для запуска панели мониторинга Kubernetes выполните команду [az aks browse][az-aks-browse], как описано на предыдущем шаге.

## <a name="run-an-application"></a>Запуск приложения

В правом верхнем углу панели мониторинга Kubernetes нажмите кнопку **Создать**. Присвойте развертыванию имя `nginx` и введите `nginx:latest` в качестве имени образа контейнера. В разделе **Служба** выберите **External** (Внешний) и введите `80` для порта и целевого порта.

Когда все будет готово, щелкните **Развернуть** для создания развертывания.

![Диалоговое окно создания службы Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Просмотр приложения

Сведения о состоянии приложения можно просмотреть на панели мониторинга Kubernetes. Когда приложение выполняется, рядом с каждым компонентом стоит зеленый флажок.

![Модули Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Чтобы просмотреть дополнительные сведения о модулях приложения, в левом меню щелкните **Pods** (Модули), а затем выберите модуль **NGINX**. Здесь можно просмотреть сведения о модуле, например данные о потреблении ресурсов.

![Ресурсы Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Чтобы получить IP-адрес приложения, в левом меню щелкните **Службы**, а затем выберите службу **NGINX**.

![Представление nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Изменение приложения

Помимо создания и просмотра приложений панель мониторинга Kubernetes можно использовать для изменения и обновления развертываний приложений.

Чтобы изменить развертывание, в левом меню щелкните **Развернутые приложения**, а затем выберите развертывание **NGINX**. Наконец, выберите **Изменить** на верхней правой панели навигации.

![Окно изменения в Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Найдите значение `spec.replica`, которое должно быть равно 1. Измените это значение на 3. При этом счетчик реплики развертывания NGINX увеличится с 1 до 3.

Выберите **Обновить** по окончании.

![Окно изменения в Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о панели мониторинга Kubernetes см. в документации по Kubernetes.

> [!div class="nextstepaction"]
> [Web UI (Dashboard)][kubernetes-dashboard] (Панель мониторинга веб-интерфейса)

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
