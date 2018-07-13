---
title: Создание приложения-контейнера Windows в Service Fabric в Azure | Документы Майкрософт
description: В этом кратком руководстве вы создадите первое приложение-контейнер Windows в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 081b2be82b15c36566e8eb9fe4af0037804d0e7e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951201"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Краткое руководство. Развертывание контейнеров Windows в Service Fabric

Azure Service Fabric — это платформа распределенных систем для развертывания масштабируемых надежных микрослужб и контейнеров и управления ими.

Чтобы запустить существующее приложение в контейнере Windows кластера Service Fabric, не требуется вносить изменения в приложение. В этом кратком руководстве показано, как развернуть готовый образ контейнера Docker в приложении Service Fabric. По окончании вы получите рабочий контейнер для IIS и Nano Server в Windows Server 2016. В этом кратком руководстве объясняется, как развернуть контейнер Windows. Чтобы узнать, как развернуть контейнер Linux, ознакомьтесь с [этим кратким руководством](service-fabric-quickstart-containers-linux.md).

![Страница служб IIS по умолчанию][iis-default]

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

* упаковка контейнера образов Docker;
* настройка обмена данными;
* создание и упаковка приложений Service Fabric;
* развертывание приложения-контейнера в Azure.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Компьютер для разработки, на котором установлено ПО, перечисленное ниже.
  * Visual Studio 2015 или Visual Studio 2017.
  * [Пакет SDK и средства для Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Упаковка контейнера образов Docker с помощью Visual Studio

Пакет SDK и средства для Service Fabric предоставляют шаблон службы для развертывания контейнера в кластере Service Fabric.

Запустите Visual Studio от имени администратора.  Выберите **Файл** > **Создать** > **Проект**.

Выберите **Приложение Service Fabric**, назовите его MyFirstContainer и нажмите кнопку **ОК**.

Выберите **Контейнер** в шаблонах **размещенных контейнеров и приложений**.

В поле **Имя образа** укажите microsoft/iis:nanoserver — [базовый образ Nano Server Windows Server и IIS](https://hub.docker.com/r/microsoft/iis/).

Настройте сопоставление порта контейнера с портом узла, чтобы входящие запросы к службе через порт 80 сопоставлялись с портом 80 в контейнере.  Укажите **порт контейнера** (80) и **узла** (80).  

Присвойте службе имя MyContainerService и нажмите кнопку **ОК**.

![Диалоговое окно создания службы][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Укажите сборку операционной системы для образа контейнера
Контейнеры, созданные с помощью одной версии Windows Server могут не работать на узле под управлением другой версии Windows Server. Например, созданные с помощью Windows Server версии 1709 контейнеры, не работают в Windows Server 2016. Дополнительные сведения см. в разделе [ОС контейнера Windows Server и совместимость ОС узлов](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

В версии 6.1 среды выполнения Service Fabric и более поздних можно указать несколько образов операционной системы на контейнер и отметить каждый из них, указав версию сборки операционной системы, для которой он будет развертываться. Это помогает обеспечить запуск приложения на узлах под управлением других версий ОС Windows. Дополнительные сведения см. в разделе [Указание сборок ОС для образов контейнеров](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Корпорация Майкрософт публикует различные образы для версий IIS, встроенных в разных версиях Windows Server. Чтобы убедиться в том, что Service Fabric развертывает контейнер, совместимый с версией Windows Server на узлах кластера, где он развертывает приложение, добавьте следующие строки в файл *ApplicationManifest.xml*. Версия сборки для Windows Server 2016 — 14393, а версия сборки для Windows Server версии 1709 — 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Манифест служб продолжает указывать только один образ для наносервера, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Создание кластера

Для развертывания приложения в кластере Azure можно использовать кластер сообщества. Кластеры сообщества — это бесплатные кластеры Service Fabric, которые доступны в течение ограниченного времени. Эти кластеры размещены в Azure и поддерживаются командой Service Fabric. Любой пользователь может развертывать приложения на этих кластерах и знакомиться с платформой.  Кластер использует один самозаверяющий сертификат для обмена данными между узлами и обеспечения безопасности при взаимодействии между клиентом и узлом. Контейнеры с поддержкой кластеров сообщества. Чтобы настроить и использовать собственный кластер, связанный с ним номер SKU должен поддерживать контейнеры (например, Windows Server Datacenter 2016 с контейнерами).

Войдите в систему и [присоедините кластер Windows](http://aka.ms/tryservicefabric). Скачайте сертификат PFX на компьютер, щелкнув ссылку **PFX**. Щелкните ссылку **How to connect to a secure Party cluster?** (Как подключиться к безопасному кластеру сообщества) и скопируйте пароль сертификата. Сертификат, пароль сертификата и значение **конечной точки подключения** будут использоваться в дальнейшем.

![Конечная точка подключения и файл PFX](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> В течение одного часа доступно ограниченное число кластеров сообщества. Если при попытке регистрации в кластере сообщества поступает сообщение об ошибке, подождите немного и повторите попытку. Или следуйте инструкциям из раздела о [развертывании приложения .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application), чтобы создать кластер Service Fabric в подписке Azure и развернуть в нем приложение. Кластер, созданный с помощью Visual Studio, поддерживает контейнеры. После развертывания и проверки приложения в кластере можно сразу перейти к работе с [полными примерами манифестов службы и приложений Service Fabric](#complete-example-service-fabric-application-and-service-manifests).
>

На компьютере Windows установите PFX в хранилище сертификатов: *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Помните об отпечатке для следующего шага.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Развертывание приложения в Azure с помощью Visual Studio

Теперь, когда приложение готово, можно развернуть его в кластер напрямую из Visual Studio.

Щелкните правой кнопкой мыши **MyFirstContainer** в обозревателе решений и выберите команду **Опубликовать**. Появится диалоговое окно "Опубликовать".

Скопируйте **конечную точку подключения** со страницы кластера сообщества в поле **Конечная точка подключения**. Например, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Щелкните **Расширенные параметры подключения** и проверьте сведения о параметрах подключения.  Значения *FindValue* и *ServerCertThumbprint* должны соответствовать отпечатку сертификата, который установлен на предыдущем шаге.

![Диалоговое окно "Публикация"](./media/service-fabric-quickstart-containers/publish-app.png)

Щелкните **Опубликовать**.

Имя каждого приложения в кластере должно быть уникальным.  Кластеры сообщества — это открытая общедоступная среда. Но они могут конфликтовать с существующим приложением.  В случае конфликта имен переименуйте проект Visual Studio и повторите развертывание.

Откройте браузер и перейдите к **конечной точки подключения**, указанной на странице кластера сообщества. При необходимости можно добавить в начало URL-адреса идентификатор схемы `http://` и порт `:80`. Например, http://zwin7fh14scd.westus.cloudapp.azure.com:80. Откроется веб-страница IIS по умолчанию. ![Веб-страница IIS по умолчанию][iis-default]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие действия:

* упаковка контейнера образов Docker;
* настройка обмена данными;
* создание и упаковка приложений Service Fabric;
* развертывание приложения-контейнера в Azure.

Дополнительные сведения о работе с контейнерами Windows в Service Fabric см. в руководстве для приложений-контейнеров Windows.

> [!div class="nextstepaction"]
> [Создание приложения-контейнера Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
