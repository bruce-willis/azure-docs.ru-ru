---
title: Настройка среды разработки Windows для приложений Сетки Service Fabric | Документация Майкрософт
description: Настройте среду разработки Windows таким образом, чтобы можно было создавать приложение Сетки Service Fabric и разворачивать его в Сетке Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/20/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 589bef1894a3bee1e6974a0ea2516200fae2891f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185549"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Чтобы создавать приложения Service Fabric, настройте среду разработки Windows

Чтобы создавать и запускать приложения Service Fabric на компьютере для разработки Windows, установите среду выполнения Service Fabric, пакет SDK и средства.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Поддерживаемые версии операционных систем

Для разработки поддерживаются следующие операционные системы:

* Windows 10 (Корпоративная, Профессиональная или Образование)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

Для развертывания приложений Service Fabric требуется Visual Studio 2017. [Его версия должна быть не ниже 15.6.0][download-visual-studio] и включать следующие рабочие нагрузки.

- ASP.NET и веб-разработка.
- разработка Azure.
 
## <a name="windows-10---install-docker"></a>Windows 10 — установка Docker

Скачайте и установите последнюю версию [Docker Community Edition для Windows][download-docker] для поддержки контейнерных приложений Service Fabric, используемого Сеткой Service Fabric.

Во время процесса установки при появлении запроса выберите **Использовать контейнеры Windows вместо контейнеров Linux**. Если на компьютере не включен Hyper-V, установка Docker предложит его включить. Если будет предложено, щелкните **OK**.

## <a name="windows-server-2016---install-hyper-v-and-docker"></a>Windows Server 2016 — установка Hyper-V и Docker

**Установка Hyper-V**

Чтобы установить Hyper-V, сначала откройте PowerShell как администратор и выполните следующую команду, а затем перезагрузите компьютер. Дополнительные сведения см. в статье [Install Docker Enterprise Edition for Windows Server][download-docker-server] (Установка Docker Enterprise Edition для Windows).

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

**Установка Docker**

Чтобы установить Docker, запустите PowerShell как администратор и выполните следующую команду.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Перезагрузите компьютер.

## <a name="sdk-and-tools"></a>Пакет SDK и средства

Установите Сетку Service Fabric, среду выполнения, пакет SDK и средства в следующем порядке.

1. Установите [пакет SDK Сетки Service Fabric][download-sdkmesh] с помощью установщика веб-платформы. Это позволит установить пакет SDK, средства Microsoft Azure Service Fabric и среду выполнения.
2. Установите [расширение средств Visual Studio Service Fabric (предварительная версия)][download-tools] из Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Создание кластера

Если используется Visual Studio, этот раздел можно пропустить, поскольку Visual Studio создает локальный кластер при его отсутствии.

Для повышения производительности во время процесса отладки при создании и запуске приложений Service Fabric рекомендуется создать кластер локальной разработки с одним узлом. Этот кластер должен быть запущен при развертывании или при отладке проекта Сетки Service Fabric.

Перед созданием кластера **необходимо** запустить Docker. Проверьте, работает ли Docker, открыв окно терминала и выполнив команду `docker ps`, которая укажет, возникает ли ошибка. Если полученный ответ не является ошибкой, это значит что Docker работает и можно начинать создание кластера.

После установки среды выполнения, пакета SDK и средства Visual Studio создайте кластер разработки.

1. Закройте окно PowerShell.
2. Откройте окно PowerShell с повышенными правами от имени администратора. Этот шаг необходим для загрузки модулей Service Fabric, которые были недавно установлены.
3. Чтобы создать кластер разработки, выполните следующую команду PowerShell.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Чтобы запустить локальное средство управления кластером, выполните следующую команду PowerShell.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Теперь можно приступать к созданию приложений Сетки Service Fabric.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с руководством [Tutorial: Create, debug, and deploy a multi-service web application to Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md) (Руководство. Создание, отладка и развертывание многосервисного веб-приложения в Сетке Service Fabric).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/