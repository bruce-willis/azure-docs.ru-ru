---
title: Памятка по конфигурации XPath для роли облачных служб | Документация Майкрософт
description: Различные параметры XPath можно использовать в конфигурации роли облачной службы, чтобы предоставить их в качестве переменных среды.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2db63be6c6997840f7409a3ca79f1845f30e4ceb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008065"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Предоставление параметров конфигурации ролей как переменной среды с помощью XPath
В файле определения службы рабочей роли или веб-роли облачной службы можно предоставить значения конфигурации среды выполнения как переменные среды. Поддерживаются следующие значения XPath (которые соответствуют значениям API).

Эти значения XPath также можно использовать с помощью библиотеки [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>Приложение, запущенное в эмуляторе
Указывает, что приложение выполняется в эмуляторе.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Код |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Идентификатор развертывания
Получает идентификатор развертывания для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Код |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Идентификатор роли
Получает идентификатор текущей роли для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Код |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Обновление домена
Получает домен обновления для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Код |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Домен сбоя
Получает домен сбоя для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Код |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Имя роли
Получает имя роли для экземпляров.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Код |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Параметр конфигурации
Получает значение указанного параметра конфигурации.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Код |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Путь к локальному хранилищу
Получает путь к локальному хранилищу для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Код |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Размер локального хранилища
Получает размер локального хранилища для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Код |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Протокол конечной точки
Получает протокол конечной точки для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Код |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>IP-адрес конечной точки
Получает IP-адрес указанной конечной точки.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Код |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Порт конечной точки
Получает порт конечной точки для экземпляра.

| type | Пример |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Код |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>Пример
Ниже приведен пример рабочей роли, создающей задачу запуска с переменной среды `TestIsEmulated`, которой присваивается [значение XPath @emulated](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о файле [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Создайте пакет [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Включите [удаленный рабочий стол](cloud-services-role-enable-remote-desktop-new-portal.md) для роли.

