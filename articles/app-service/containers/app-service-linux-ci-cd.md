---
title: Непрерывное развертывание из реестра контейнеров Docker при помощи платформы "Веб-приложения для контейнеров" в Azur | Документация Майкрософт
description: Как настроить непрерывное развертывание из реестра контейнеров Docker на платформе "Веб-приложения для контейнеров".
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130971"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из управляемых репозиториев [реестра контейнеров Azure](https://azure.microsoft.com/services/container-registry/) или [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портале Azure](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Включение функции непрерывного развертывания

Включите функцию непрерывного развертывания с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), выполнив следующую команду:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

На [портале Azure](https://portal.azure.com/) выберите **Служба приложений** в левой части страницы.

Выберите имя приложения, для которого следует настроить непрерывное развертывание Docker Hub.

Чтобы включить непрерывное развертывание, на странице **Контейнер Docker** выберите **ВКЛ.**, а затем нажмите кнопку **Сохранить**.

![Снимок экрана с параметрами приложения](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Подготовка URL-адреса веб-перехватчика

Получите URL-адрес веб-перехватчика с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), выполнив следующую команду:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Запишите URL-адрес веб-перехватчика. Он понадобится в следующем разделе.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Вы можете получить `publishingusername` и `publishingpwd`, скачав профиль публикации веб-приложения с помощью портала Azure.

![Снимок экрана с добавлением веб-перехватчика (2)](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Добавление веб-перехватчика

Чтобы добавить веб-перехватчик, выполните инструкции в этих руководствах:

- [Реестр контейнеров Azure](../../container-registry/container-registry-webhook.md) (с использованием URL-адреса веб-перехватчика).
- [Веб-перехватчики для центра Docker](https://docs.docker.com/docker-hub/webhooks/).

## <a name="next-steps"></a>Дополнительная информация

* [Вводные сведения о службе приложений Azure на платформе Linux](./app-service-linux-intro.md)
* [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
* [Создание веб-приложения .NET Core в службе приложений на платформе Linux](quickstart-dotnetcore.md)
* [Создание веб-приложения Ruby в службе приложений на платформе Linux](quickstart-ruby.md)
* [Развертывание веб-приложения Docker или Go в Веб-приложении для контейнеров](quickstart-docker-go.md)
* [Служба приложений Azure на платформе Linux: вопросы и ответы](./app-service-linux-faq.md)
* [Управление веб-приложением для контейнеров с помощью Azure CLI](./app-service-linux-cli.md)
