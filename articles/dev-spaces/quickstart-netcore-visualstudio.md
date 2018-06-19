---
title: Создание пространства разработки Kubernetes в облаке | Документация Майкрософт
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824644"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Краткое руководство по созданию пространства разработки Kubernetes с помощью Azure Dev Spaces (.NET Core и Visual Studio)

Из этого руководства вы узнаете, как выполнить следующие задачи:

- настраивать Azure Dev Spaces с помощью управляемого кластера Kubernetes в Azure;
- итеративно разрабатывать код в контейнерах с помощью Visual Studio.
- выполнять отладку кода, запущенного в кластере.

> [!Note]
> Если на каком-то этапе у вас **возникли трудности**, см. статью об [устранении неполадок](troubleshooting.md) или оставьте комментарий на этой странице. Можно также использовать более подробное [руководство](get-started-netcore-visualstudio.md).

## <a name="prerequisites"></a>предварительным требованиям

- Кластер Kubernetes, работающий с Kubernetes 1.9.6, в регионах "Восток США", "Западная Европа" или "Восточная Канада", с включенным параметром "Маршрутизация приложений HTTP".

  ![Не забудьте включить параметр "Маршрутизация приложений HTTP".](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio 2017 с установленной рабочей нагрузкой "Веб-разработка". Если эта среда не установлена, скачайте ее [с этой страницы](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Настройка Azure Dev Spaces

Установите [расширение Visual Studio для Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Подключение к кластеру

Теперь вам предстоит создать и настроить проект для Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Создание веб-приложения ASP.NET

Создайте новый проект в Visual Studio 2017. В настоящее время проект должен быть **веб-приложением ASP.NET Core**. Присвойте проекту имя **webfrontend**.

Выберите шаблон **Веб-приложение (модель-представление-контроллер)** и выберите **.NET Core** и **ASP.NET Core 2.0**.

### <a name="create-a-dev-space-in-azure"></a>Создание пространства разработки в Azure

В только что созданном проекте из раскрывающегося списка параметров запуска выберите **Azure Dev Spaces**, как показано ниже.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

В появившемся диалоговом окне убедитесь, что вы вошли в систему с соответствующей учетной записью, а затем выберите существующий кластер.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

В раскрывающемся списке **Пространство** пока сохраните значение `default`. Установите флажок **Publicly Accessible** (Общедоступное), чтобы веб-приложение было доступно через общедоступную конечную точку.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Нажмите кнопку **ОК**, чтобы выбрать или создать кластер.

Если выбран кластер, который еще не был настроен для работы с Azure Dev Spaces, вы увидите сообщение с вопросом, хотите ли вы настроить его.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Нажмите кнопку **ОК**. 

### <a name="look-at-the-files-added-to-project"></a>Просмотр файлов, добавленных в проект
Пока вы ожидаете создания пространства разработки, просмотрите файлы, которые были добавлены в ваш проект при выборе Azure Dev Spaces.

- В проекте появилась папка с именем `charts`, в которой создана [диаграмма Helm](https://docs.helm.sh) для вашего приложения. Эти файлы используются для развертывания приложения в пространство разработки.
- Файл `Dockerfile` содержит сведения, необходимые для упаковки приложения в стандартный формат Docker.
- Файл `azds.yaml` содержит сведения о конфигурации, необходимые для пространства разработки, например, доступность приложения через общедоступную конечную точку.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Отладка контейнера в Kubernetes
После успешного создания пространства разработки вы можете приступить к отладке приложения. Установите точку останова в коде, например, в строке 20 в файле `HomeController.cs`, где установлена ​​переменная `Message`. Нажмите клавишу **F5**, чтобы запустить отладку. 

Visual Studio будет взаимодействовать с пространством разработки для создания и развертывания приложения, а затем откроет браузер с работающим веб-приложением. Может показаться, что контейнер работает локально, но на самом деле он выполняется в пространстве разработки в Azure. В адресе указано localhost, потому что Azure Dev Spaces создает временный туннель SSH к контейнеру, работающему в Azure.

Перейдите по ссылке **About** (Сведения) в верхней части страницы, чтобы вызвать точку останова. У вас есть полный доступ к отладочной информации, как если бы код выполнялся локально, например к стеку вызовов, локальным переменным, информации об исключениях и т. д.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Работа с несколькими контейнерами и командной разработкой](get-started-netcore-visualstudio.md#call-another-container)