---
title: Развертывание приложения Service Fabric в кластер в Azure | Документы Майкрософт
description: Сведения о развертывании приложения в кластере из Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: dc105c02354e0e83c4f9a1bad7c94a98643effd0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "41921005"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Руководство по развертыванию приложения Service Fabric в кластере в Azure

Это руководство представляет собой вторую часть цикла. Здесь показано, как развернуть приложение Azure Service Fabric в новый кластер в Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание кластера сообщества.
> * Развертывание приложения на удаленный кластер с помощью Visual Studio.

Из этой серии руководств вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * Развертывание приложения в удаленном кластере.
> * [Добавление конечной точки HTTPS к интерфейсной службе ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Настройка непрерывной интеграции и непрерывного развертывания с помощью Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Настройка мониторинга и диагностики приложения](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **Разработка Azure** и **ASP.NET и веб-разработка**.
* [Установите пакет SDK для Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования

Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-dotnet-app.md), вы можете скачать его. В окне терминала выполните следующий код, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

## <a name="publish-to-a-service-fabric-cluster"></a>Выполните публикацию в кластер Service Fabric

Теперь, когда приложение готово, можно развернуть его в кластер напрямую из Visual Studio. [Кластер Service Fabric](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-deploy-anywhere) — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими.

В этом руководстве предложено два варианта развертывания приложения для голосования в кластер Service Fabric с помощью Visual Studio.

* Выполните публикацию в пробной версии кластера (сообщества). 
* Выполните публикацию в имеющийся кластер в своей подписке. Кластеры Service Fabric можно создавать с помощью [портала Azure](https://portal.azure.com), скриптов [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) или [Azure CLI](./scripts/cli-create-cluster.md) либо [шаблона Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Многие службы используют обратный прокси-сервер для взаимодействия между собой. У кластеров, созданных из Visual Studio, и сторонних кластеров есть обратный прокси-сервер, включенный по умолчанию. При использовании имеющегося кластера [в нем необходимо включить обратный прокси-сервер](service-fabric-reverseproxy-setup.md#).


### <a name="find-the-voting-web-service-endpoint-for-your-azure-subscription"></a>Поиск конечной точки веб-службы для голосования для своей подписки Azure

Чтобы опубликовать приложение для голосования в подписке Azure, найдите конечную точку интерфейсной веб-службы. Если используется кластер сообщества, подключитесь к порту 8080, используя автоматически открытый пример для голосования. Его не нужно настраивать в подсистеме балансировки нагрузки кластера сообщества.

Интерфейсная веб-служба ожидает передачи данных через определенный порт. Если приложение развертывается в кластере в Azure, то приложение и кластер запускаются под контролем Azure Load Balancer. Порт приложения должен открываться с помощью правила в Azure Load Balancer для кластера. Открытый порт отправляет входящий трафик в веб-службу. Порт указан в файле **VotingWeb/PackageRoot/ServiceManifest.xml** в элементе **Endpoint**. Например, порт 8080.

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Чтобы создать подписку в Azure, откройте этот порт, используя правила балансировки нагрузки в Azure с помощью [сценария PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) или подсистемы балансировки нагрузки для этого кластера на [портале Azure](https://portal.azure.com).

### <a name="join-a-party-cluster"></a>Присоединение кластера сообщества

> [!NOTE]
>  Чтобы опубликовать приложение в своем кластере в рамках подписки Azure, перейдите к разделу [Публикация приложения с помощью Visual Studio](#publish-the-application-by-using-visual-studio). 

Общедоступные кластеры — это размещенные в Azure бесплатные временные кластеры Service Fabric, которыми управляет команда Service Fabric. Любой пользователь может развертывать приложения и изучать возможности платформы. Кластер использует один самозаверяющий сертификат для обеспечения безопасности при взаимодействии и между узлами, и между клиентом и узлом.

Войдите в систему и [присоедините кластер Windows](http://aka.ms/tryservicefabric). Скачайте сертификат PFX на компьютер, выбрав ссылку **PFX**. Выберите ссылку **How to connect to a secure Party cluster?** (Как подключиться к безопасному кластеру сообщества?) и скопируйте пароль сертификата. Сертификат, пароль сертификата и значение **конечной точки подключения** будут использоваться в дальнейшем.

![Конечная точка подключения и файл PFX](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> В течение одного часа доступно ограниченное число общедоступных кластеров. Если при попытке зарегистрироваться в общедоступном кластере появляется сообщение об ошибке, подождите и повторите попытку. Или следуйте инструкциям из раздела о [создании кластера Service Fabric в Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application), чтобы создать кластер Service Fabric в подписке Azure и развернуть в нем приложение. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
>

На компьютере Windows установите PFX в хранилище сертификатов **CurrentUser\My**.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Помните об отпечатке для следующего шага.

> [!Note]
> По умолчанию служба веб-интерфейса ожидает передачи данных через порт 8080 для входящего трафика. Порт 8080 открыт в кластере сообщества. Если нужно изменить порт приложения, замените его одним из портов, открытых в кластере сообщества.
>

### <a name="publish-the-application-by-using-visual-studio"></a>Публикация приложения с использованием Visual Studio

Теперь, когда приложение готово, можно развернуть его в кластер напрямую из Visual Studio.

1. Щелкните правой кнопкой мыши **Voting** (Голосование) в обозревателе решений. Выберите **Опубликовать**. Откроется диалоговое окно **Publish** (Опубликовать).

2. Скопируйте **конечную точку подключения** со страницы кластера сообщества или из подписки Azure в поле **Конечная точка подключения**. Например, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Выберите **Advanced Connection Parameters** (Расширенные параметры подключения).  Убедитесь, что значения **FindValue** и **ServerCertThumbprint** соответствуют отпечатку сертификата, установленному на предыдущем шаге для кластера сообщества или сертификата, который соответствует подписке Azure.

    ![Публикация приложения Service Fabric](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Имя каждого приложения в кластере должно быть уникальным. Кластеры сообщества — это открытая общедоступная среда, а значит они могут конфликтовать с имеющимся приложением. В случае конфликта имен переименуйте проект Visual Studio и повторите развертывание.

3. Нажмите кнопку **Опубликовать**.

4. Чтобы получить доступ к приложению в кластере, откройте браузер и введите адрес кластера, за которым следует **:8080**. Или укажите другой порт, если он настроен. Например, `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Вы увидите приложения, выполняющиеся в кластере Azure. На веб-странице для голосования попробуйте добавить и удалить параметры голосования, а также проголосовать за один или несколько из этих параметров.

    ![Пример для голосования Service Fabric](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Включение HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
