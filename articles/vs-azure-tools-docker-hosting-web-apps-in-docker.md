---
title: Развертывание контейнера ASP.NET Docker в реестре контейнеров Azure (ACR) | Документация Майкрософт
description: Сведения об использовании средства Visual Studio для Docker для развертывания веб-приложения ASP.NET Core в реестре контейнеров
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 4442c1d763f4ed21a5efeedbe957727254e2a0b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658477"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Развертывание контейнера ASP.NET в реестре контейнеров с помощью Visual Studio
## <a name="overview"></a>Обзор
Docker — это облегченная платформа контейнеров, чем-то похожая на виртуальную машину, которую можно использовать для размещения приложений и служб.
Это руководство покажет как с помощью Visual Studio публиковать контейнерные приложения в [реестре контейнеров Azure](https://azure.microsoft.com/en-us/services/container-registry).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством:

* Установите последнюю версию [Visual Studio 2017](https://azure.microsoft.com/en-us/downloads/) с рабочей нагрузкой "ASP.NET и разработка веб-приложений"
* Установить [Docker для Windows](https://docs.docker.com/docker-for-windows/install/).

## <a name="1-create-an-aspnet-core-web-app"></a>1. Создание веб-приложения ASP.NET Core
Давайте создадим простое приложение ASP.NET Core, которое мы будем использоваться в этом руководстве.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Опубликуйте контейнер в реестре контейнеров Azure
1. В **обозревателе решений** щелкните правой кнопкой проект и выберите **Опубликовать**.
2. В диалоговом окне целевой публикации выберите вкладку **Реестр контейнеров**.
3. Выберите **Создать реестр контейнеров Azure** и щелкните **Опубликовать**.
4. Заполните нужные значения в окне **Создать новый реестр контейнеров Azure**.

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-префикс** | Глобально уникальное имя | Имя, которое однозначно идентифицирует реестр контейнеров. |
    | **Подписка** | Выберите свою подписку | Подписка Azure, которую нужно использовать. |
    | **[Группа ресурсов](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Имя группы ресурсов, в которой создается реестр контейнеров. Чтобы создать группу ресурсов, выберите **Создать**.|
    | **[SKU](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus)** | Стандартная | Уровень обслуживания в реестре контейнеров  |
    | **Расположение реестра** | Расположение рядом с вами | Выберите расположение в ближайшем [регионе](https://azure.microsoft.com/regions/) или в регионе, расположенном рядом с другими службами, которые будут использовать реестр контейнеров. |
    ![Диалоговое окно "Создание реестра контейнеров Azure" Visual Studio][0]
5. Нажмите кнопку **Создать**.

Теперь можно извлечь контейнер из реестра в любой узел, поддерживающий работу образов Docker, например [Экземпляры контейнеров Azure](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
