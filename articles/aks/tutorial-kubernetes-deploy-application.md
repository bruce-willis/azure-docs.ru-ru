---
title: Руководство по Kubernetes в Azure. Развертывание приложения
description: В этом руководстве по Службе Azure Kubernetes (AKS) выполняется развертывание многоконтейнерного приложения в кластере с помощью пользовательского образа, хранящегося в Реестре контейнеров Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bf817f553250ead449ec0d5db3d33acc2eff23f3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918256"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Руководство. Запуск приложения в службе Azure Kubernetes

Kubernetes предоставляет распределенную платформу для контейнерных приложений. Вы создаете и развертываете собственные приложения и службы в кластере Kubernetes, а также предоставляете кластеру возможность управлять доступностью и подключением. В этом руководстве (часть 4 из 7) выполняется развертывание примера приложения в кластер Kubernetes. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Обновление файлов манифестов Kubernetes.
> * Выполнение приложения в Kubernetes.
> * Тестирование приложения

В следующих руководствах это приложение масштабируется и обновляется.

Для работы с этим руководством требуется понимание основных концепций Kubernetes. Подробные сведения см. в [документации по Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение упаковывалось в образ контейнера, далее этот образ отправлялся в реестр контейнеров Azure, после чего создавался кластер Kubernetes.

Для работы с этим руководством необходимо предварительно создать файл манифеста Kubernetes `azure-vote-all-in-one-redis.yaml`. Этот файл был скачан вместе с исходным кодом приложения в предыдущем руководстве. Проверьте, клонировали ли вы репозиторий и изменили ли каталоги на клонированный репозиторий. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Prepare application for Azure Container Service (AKS)][aks-tutorial-prepare-app] (Подготовка приложений для службы контейнеров Azure (AKS)).

Для выполнения задач из этого руководства требуется Azure CLI 2.0.44 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="update-the-manifest-file"></a>Обновление файла манифеста

В этих руководствах экземпляр Реестра контейнеров Azure (ACR) хранит образ контейнера для примера приложения. Для развертывания приложения необходимо обновить имя образа в файле манифеста Kubernetes, чтобы включить имя сервера входа ACR.

Получите имя сервера входа ACR, выполнив команду [az acr list][az-acr-list] следующим образом:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Пример файла манифеста из репозитория git, клонированного в первом руководстве, использует имя сервера входа *microsoft*. Откройте этот файл манифеста в текстовом редакторе, например `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Замените *microsoft* именем сервера входа ACR. Имя образа можно найти в строке 47 файла манифеста. В следующем примере показано имя образа по умолчанию:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Укажите собственное имя сервера входа ACR таким образом, чтобы файл манифеста выглядел, как в следующем примере:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Сохраните и закройте файл.

## <a name="deploy-the-application"></a>Развертывание приложения

Чтобы развернуть приложение, используйте команду [kubectl apply][kubectl-apply]. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes. Укажите пример файла манифеста, как показано в следующем примере:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Объекты Kubernetes создаются в кластере, как показано в следующем примере:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Тестирование приложения

Создается [служба Kubernetes][kubernetes-service], которая открывает доступ к приложению через Интернет. Это может занять несколько минут. Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`:

```console
kubectl get service azure-vote-front --watch
```

Изначально для параметра *ExternaL-IP* (Внешний IP-адрес) службы *azure-vote-front* отображается состояние *ожидания*, как показано в следующем примере:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Когда значение *внешнего IP-адреса* изменится с состояния *ожидания* на ожидаемый IP-адрес, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания kubectl. В следующем примере показан присваиваемый общедоступный IP-адрес:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Чтобы увидеть приложение в действии, откройте в веб-браузере внешний IP-адрес.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

Если не удалось загрузить приложение, возможно, возникла проблема с авторизацией реестра образов. Чтобы просмотреть состояние контейнеров, используйте команду `kubectl get pods`. Если образы контейнера невозможно извлечь, ознакомьтесь с разделом [Доступ с помощью секрета Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве приложение Azure для голосования было развернуто в кластере Kubernetes Службы контейнеров Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Обновление файлов манифестов Kubernetes.
> * Выполнение приложения в Kubernetes.
> * Тестирование приложения

Перейдите к следующему руководству, чтобы узнать о способах масштабировании приложения Kubernetes и базовой инфраструктуре Kubernetes.

> [!div class="nextstepaction"]
> [Scale application in Azure Container Service (AKS)][aks-tutorial-scale] (Масштабирование приложений в службе контейнеров Azure (AKS))

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli
