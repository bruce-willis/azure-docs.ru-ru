---
title: Руководство по Kubernetes в Azure. Подготовка приложения
description: В этом руководстве по Службе Azure Kubernetes (AKS) вы узнаете, как подготовить и создать многоконтейнерное приложение с помощью Docker Compose, которое можно затем развернуть в AKS.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b2bb187e5ad55b466da0b9b06ffbb047ac539717
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105005"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Руководство. Подготовка приложения для Службы Azure Kubernetes (AKS)

В этом руководстве (часть 1 из 7) выполняется подготовка многоконтейнерного приложения к использованию в Kubernetes. Имеющиеся средства разработки, такие как Docker Compose, используются для локального создания приложения и его тестирования. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Клонирование источника примера приложения с GitHub.
> * Создание образа контейнера из источника примера приложения.
> * Тестирование многоконтейнерного приложения в локальной среде Docker.

После выполнения всех действий в вашей локальной среде разработки выполняется следующее приложение:

![Схема кластера Kubernetes в Аzure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

В последующих руководствах образ контейнера отправляется в Реестр контейнеров Azure, а затем развертывается в кластере AKS.

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и команды `docker`. [Руководство по началу работы с Docker][docker-get-started] содержит базовые сведения о контейнерах.

Для работы с этим руководством требуется локальная среда разработки Docker. Docker предоставляет пакеты, которые позволяют настроить Docker в системе [Mac][docker-for-mac], [Windows][docker-for-windows] или [Linux][docker-for-linux].

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этих руководств. Таким образом мы рекомендуем полную среду разработки Docker.

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется простое приложение для голосования. Приложение состоит из веб-компонента интерфейсной части и серверного экземпляра Redis. Веб-компонент упаковывается в пользовательский образ контейнера, а для экземпляра Redis используется неизмененный образ из Docker Hub.

Используйте команду [git][], чтобы клонировать пример приложения в среду разработки:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Измените каталоги, чтобы использовать клонированный каталог.

```console
cd azure-voting-app-redis
```

Внутри каталога содержится исходный код приложения, предварительно созданный файл Docker Compose и файл манифеста Kubernetes. Эти файлы используются в руководстве.

## <a name="create-container-images"></a>Создание образов контейнеров

С помощью [Docker Compose][docker-compose] можно автоматизировать создание дополнительных образов контейнеров и развертывание многоконтейнерных приложений.

Используйте пример файла `docker-compose.yaml`, чтобы создать образ контейнера, скачать образ Redis и запустить приложение:

```console
docker-compose up -d
```

После завершения выполните команду [docker images][docker-images], чтобы просмотреть созданные образы. Было создано или скачано три образа. Образ *azure-vote-front* содержит интерфейсное приложение и использует образ `nginx-flask` в качестве основы. Образ `redis` используется для запуска экземпляра Redis.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Выполните команду [docker ps][docker-ps], чтобы просмотреть выполняемые контейнеры:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Чтобы увидеть работающее приложение, введите http://localhost:8080 в локальном веб-браузере. Нагрузки примера приложения, как показано в следующем примере:

![Схема кластера Kubernetes в Аzure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Теперь, когда функциональные возможности приложения проверены, запущенные контейнеры можно остановить и удалить. Не удаляйте образы контейнеров. В следующем руководстве образ *azure-vote-front* будет отправлен в экземпляр Реестра контейнеров Azure.

Остановите и удалите экземпляры контейнеров и ресурсы с помощью команды [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

При удалении локального приложения у вас остается образ Docker, содержащий приложение Azure для голосования, *azure-front-front*, которое используется в следующем руководстве.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы протестировали приложение и создали для него образы контейнеров. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Клонирование источника примера приложения с GitHub.
> * Создание образа контейнера из источника примера приложения.
> * Тестирование многоконтейнерного приложения в локальной среде Docker.

Переходите к следующему руководству, чтобы узнать о том, как хранить образы контейнеров в Реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Принудительная отправка образов в Реестр контейнеров Azure][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
