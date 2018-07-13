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
ms.openlocfilehash: 95877f8b81641dd70434fc167003cfcce07d9e84
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110816"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Краткое руководство. Развертывание контейнеров Windows в Service Fabric

Azure Service Fabric — это платформа распределенных систем для развертывания масштабируемых надежных микрослужб и контейнеров и управления ими.

Чтобы запустить существующее приложение в контейнере Windows кластера Service Fabric, не требуется вносить изменения в приложение. В этом кратком руководстве показано, как развернуть готовый образ контейнера Docker в приложении Service Fabric. По окончании вы получите рабочий контейнер для IIS и Nano Server в Windows Server 2016. В этом кратком руководстве объясняется, как развернуть контейнер Windows. Чтобы узнать, как развернуть контейнер Linux, ознакомьтесь с [этим кратким руководством](service-fabric-quickstart-containers-linux.md).

![Страница служб IIS по умолчанию][iis-default]

Из этого краткого руководства вы узнаете, как выполнять следующие задачи:

* упаковка контейнера образов Docker;
* Настройка обмена данными
* Создание и упаковка приложений Service Fabric
* развертывание приложения-контейнера в Azure.

## <a name="prerequisites"></a>Предварительные требования

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

<<<<<<< Обновленный вышестоящий источник ![Диалоговое окно создания службы][new-service]
=======
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Настройка обмена данными и сопоставления порта контейнера с портом узла

Для обмена данными службе требуется конечная точка.  В этом кратком руководстве служба контейнеров ожидает передачи данных на порту 80.  В обозревателе решений откройте *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*.  Обновите имеющуюся строку `Endpoint` в файле ServiceManifest.xml и добавьте протокол, порт и схему URI.

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```

Если указать `UriScheme`, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Полный пример файла ServiceManifest.xml приведен в конце этой статьи.

Настройте сопоставление порта контейнера с портом узла, чтобы входящие запросы к службе через порт 80 сопоставлялись с портом 80 в контейнере.  В обозревателе решений откройте *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* и укажите политику `PortBinding` в строке `ContainerHostPolicies`.  В этом кратком руководстве для параметра `ContainerPort` задано значение 80, а для `EndpointRef` — MyContainerServiceTypeEndpoint (конечная точка, определенная в манифесте службы).

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

Полный пример файла ApplicationManifest.xml приведен в конце этой статьи.
>>>>>>> Скрытые изменения

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

Войдите в систему и [присоедините кластер Windows](http://aka.ms/tryservicefabric). Загрузите сертификат PFX на компьютер, щелкнув ссылку **PFX**. Щелкните ссылку **How to connect to a secure Party cluster?** (Как подключиться к безопасному кластеру сообщества) и скопируйте пароль сертификата. Сертификат, пароль сертификата и значение **конечной точки подключения** будут использоваться в дальнейшем.

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
<<<<<<< Updated upstream
``` 
=======
```

Remember the thumbprint for the following step.
>>>>>>> Stashed changes

## Deploy the application to Azure using Visual Studio

Now that the application is ready, you can deploy it to a cluster directly from Visual Studio.

Right-click **MyFirstContainer** in the Solution Explorer and choose **Publish**. The Publish dialog appears.

<<<<<<< Updated upstream
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 
=======
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Click **Advanced Connection Parameters** and verify the connection parameter information.  *FindValue* and *ServerCertThumbprint* values must match the thumbprint of the certificate installed in the previous step.

![Publish Dialog](./media/service-fabric-quickstart-containers/publish-app.png)
>>>>>>> Stashed changes

Click **Publish**.

Each application in the cluster must have a unique name.  Party clusters are a public, shared environment however and there may be a conflict with an existing application.  If there is a name conflict, rename the Visual Studio project and deploy again.

Open a browser and navigate to the **Connection endpoint** specified in the Party cluster page. You can optionally prepend the scheme identifier, `http://`, and append the port, `:80`, to the URL. For example, http://zwin7fh14scd.westus.cloudapp.azure.com:80. You should see the IIS default web page:
![IIS default web page][iis-default]

<<<<<<< Updated upstream
=======
## Complete example Service Fabric application and service manifests

Here are the complete service and application manifests used in this quickstart.

### ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

>>>>>>> Скрытые изменения
## <a name="next-steps"></a>Дальнейшие действия

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
