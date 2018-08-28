---
title: Руководство по работе с Kubernetes в Azure. Создание реестра контейнеров
description: С помощью этого руководства Azure Kubernetes Service (AKS) вы создадите экземпляр Реестра контейнеров Azure и отправите образ контейнера примера приложения.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4f240d346457717c66a6ed189cfd8610c7a764da
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41921042"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Руководство. Развертывание Реестра контейнеров Azure и его использование

Реестр контейнеров Azure (ACR) является частным реестром на базе Azure для образов контейнеров Docker. Частный реестр контейнеров позволяет безопасно выполнять сборку и развертывать приложения, а также пользовательский код. В этом руководстве (здесь представлена вторая его часть из семи) вы развернете экземпляр ACR и отправите образ контейнера в экземпляр. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание экземпляра Реестра контейнеров Azure;
> * добавление тегов к образу контейнера для реестра контейнеров Azure;
> * отправка образа в реестр контейнеров Azure.
> * просмотр образов в реестре.

В последующих руководствах этот экземпляр ACR интегрируется с кластером Kubernetes в AKS, а приложение развертывается из образа.

## <a name="before-you-begin"></a>Перед началом работы

В [предыдущей части руководства][aks-tutorial-prepare-app] мы создали образ контейнера для простого приложения Azure для голосования. Если вы еще не создали образ приложения Azure для голосования, выполните инструкции из статьи [Подготовка приложения для Службы контейнеров Azure (AKS)][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.44 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Создание реестра контейнеров Azure

Для создания Реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте экземпляр Реестра контейнеров Azure с помощью команды [az acr create][az-acr-create] и введите собственное имя реестра. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В дальнейшем в этом руководстве `<acrName>` будет использоваться как заполнитель имени реестра контейнеров. SKU *Базовый* — это оптимизированная по стоимости точка входа для целей разработки, обеспечивающая баланс ресурсов хранения и пропускной способности.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Вход в реестр контейнеров

Чтобы использовать экземпляр ACR, сначала необходимо войти. Используйте команду [az acr login][az-acr-login] и укажите уникальное имя, заданное для реестра контейнеров на предыдущем шаге.

```azurecli
az acr login --name <acrName>
```

После выполнения эта команда возвращает сообщение *Login Succeeded* (Вход выполнен).

## <a name="tag-a-container-image"></a>Добавление тега к образу контейнера

Чтобы просмотреть список сохраненных образов, используйте команду [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Чтобы использовать образ контейнера *azure-vote-front* с ACR, образ нужно отметить тегом с адресом сервера входа в реестр. Данный тег используется для маршрутизации при отправке образов контейнеров в реестр образов.

Чтобы получить адрес сервера входа, используйте команду [az acr list][az-acr-list] и запросите *loginServer*, как показано ниже:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Теперь пометьте тегом локальный образ *azure-vote-front*, используя адрес *acrloginServer* реестра контейнеров. Чтобы указать номер версии образа, добавьте *:v1* в конец имени образа:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Чтобы убедиться, что теги применены, запустите команду [docker images][docker-images] еще раз. Образ помечен адресом экземпляра ACR и номером версии.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Отправка образов в реестр

Теперь вы можете отправить образ *azure-vote-front* в экземпляр ACR. Используйте команду [docker push][docker-push] и предоставьте собственный адрес *acrLoginServer* для имени образа так:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Отправка образа в ACR может занять несколько минут.

## <a name="list-images-in-registry"></a>Перечисление образов в реестре

Чтобы получить список образов, отправленных в экземпляр ACR, выполните команду [az acr repository list][az-acr-repository-list]. Укажите собственное `<acrName>` следующим образом:

```azurecli
az acr repository list --name <acrName> --output table
```

В следующем примере выводятся образы *azure-vote-front* по доступности в реестре:

```
Result
----------------
azure-vote-front
```

Чтобы увидеть теги для конкретного образа, используйте команду [az acr repository show-tags][az-acr-repository-show-tags], как показано ниже:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

В следующем примере показан образ *v1*, отмеченный на предыдущем шаге:

```
Result
--------
v1
```

Теперь у вас есть образ контейнера, сохраненный в частном экземпляре Реестра контейнеров Azure. В следующем руководстве мы развернем этот образ из ACR в кластер Kubernetes.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали Реестр контейнеров Azure и отправили образ для использования в кластере AKS. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создание экземпляра Реестра контейнеров Azure;
> * добавление тегов к образу контейнера для реестра контейнеров Azure;
> * отправка образа в реестр контейнеров Azure.
> * просмотр образов в реестре.

Перейдите к следующему руководству, чтобы узнать, как развертывать кластер Kubernetes в Azure.

> [!div class="nextstepaction"]
> [Развертывание кластера Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-list]: /cli/azure/acr#list
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
