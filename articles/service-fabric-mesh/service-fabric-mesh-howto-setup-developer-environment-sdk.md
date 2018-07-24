---
title: Настройка среды разработки Windows для приложений Сетки Service Fabric | Документация Майкрософт
description: Настройте среду разработки Windows таким образом, чтобы можно было создавать приложение Сетки Service Fabric и разворачивать его в Сетке Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125167"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Чтобы создавать приложения Service Fabric, настройте среду разработки Windows

Чтобы создавать и запускать приложения Service Fabric на компьютере для разработки Windows, установите среду выполнения Service Fabric, пакет SDK и средства.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Поддерживаемые версии операционных систем

Для разработки поддерживаются следующие операционные системы:

* Windows 10 (Корпоративная, Профессиональная или Образование)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Включение Hyper-V

Чтобы создать приложения Service Fabric, необходимо включить Hyper-V. 

### <a name="windows-10"></a>Windows 10

Запустите PowerShell с правами администратора и выполните следующую команду.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Перезагрузите компьютер. Дополнительные сведения о включении Hyper-V см. в статье [Установка Hyper-V в Windows10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Запустите PowerShell с правами администратора и выполните следующую команду.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Перезагрузите компьютер. Дополнительные сведения о включении Hyper-V см. в статье [Install the Hyper-V role on Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server) (Установка роли в Hyper-V для Windows Server 2016).

## <a name="visual-studio"></a>Visual Studio

Для развертывания приложений Service Fabric требуется Visual Studio 2017. [Его версия должна быть не ниже 15.6.0][download-visual-studio] и включать следующие рабочие нагрузки.

- ASP.NET и веб-разработка.
- разработка Azure.

## <a name="docker"></a>Docker

Чтобы поддерживать контейнеризованные приложения Service Fabric, используемые Сеткой Service Fabric, установите Docker.

### <a name="windows-10"></a>Windows 10

Загрузите и установите последнюю версию [Docker Community Edition для Windows][download-docker]. 

Во время процесса установки при появлении запроса выберите **Использовать контейнеры Windows вместо контейнеров Linux**. Затем необходимо будет выйти из системы и снова войти. Если Hyper-V не был ранее включен, то после входа может потребоваться его включить. Необходимо включить Hyper-V и затем перезагрузить компьютер.

После перезагрузки Docker предложит включит такую функцию, как **Контейнеры**. Включите ее и повторно перезагрузите компьютер.

### <a name="windows-server-2016"></a>Windows Server 2016

Для установки Docker используйте следующие команды PowerShell. Дополнительные сведения см. в статье [Install Docker Enterprise Edition for Windows Server][download-docker-server] (Установка Docker Enterprise Edition для Windows).

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Перезагрузите компьютер.

## <a name="sdk-and-tools"></a>Пакет SDK и средства

Установите Сетку Service Fabric, среду выполнения, пакет SDK и средства в зависимом порядке.

1. Установите [пакет SDK Сетки Service Fabric][download-sdkmesh] с помощью установщика веб-платформы. Это позволит установить пакет SDK, средства Microsoft Azure Service Fabric и среду выполнения.
2. Установите [расширение средств Visual Studio Service Fabric (предварительная версия)][download-tools] из Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Создание кластера

Для повышения производительности во время процесса отладки при создании и запуске приложений Service Fabric рекомендуется создать кластер локальной разработки с одним узлом. Этот кластер должен быть запущен при развертывании или при отладке проекта Сетки Service Fabric.

Перед созданием кластера **необходимо** запустить Docker. Проверьте, работает ли Docker, открыв окно терминала и выполнив команду `docker ps`, которая укажет, возникает ли ошибка. Если полученный ответ не является ошибкой, это значит что Docker работает и можно начинать создание кластера.

После установки среды выполнения, пакета SDK и средства Visual Studio создайте кластер разработки.

1. Закройте окно PowerShell.
2. Откройте окно PowerShell с повышенными правами от имени администратора. Чтобы загрузить установленные модули Service Fabric, этот шаг является обязательным для выполнения.
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
