---
title: Изменения, вносимые в проект ASP.NET при подключении к Azure Key Vault | Документация Майкрософт
description: Описывает, что происходит с проектом ASP.NET при подключении к Key Vault с помощью подключенных служб Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a74e4e10681f6b91e028067d8985408b0745dcd2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143181"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?

> [!div class="op_single_selector"]
> - [Приступая к работе](vs-key-vault-aspnet-get-started.md)
> - [Что произошло?](vs-key-vault-aspnet-what-happened.md)

В этой статье описаны конкретные изменения, которые вносятся в проект ASP.NET при добавлении [подключенной службы Key Vault с помощью Visual Studio](vs-key-vault-add-connected-service.md).

Сведения о работе с подключенной службой см. в статье [Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi)](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Добавленные ссылки

Это изменение влияет на ссылки на файлы проекта \*.NET и `packages.config` (ссылки NuGet).

| type | Справочные материалы |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Добавленные файлы

- Добавлен файл ConnectedService.json, в который записываются некоторые сведения о поставщике подключенной службы, версия и ссылка на документацию.

## <a name="project-file-changes"></a>Изменения в файле проекта

- Добавлены элемент ItemGroup "Подключенные службы" и файл ConnectedServices.json.
- Ссылки на сборки .NET, описанные в разделе [Добавленные ссылки](#added-references).

## <a name="webconfig-or-appconfig-changes"></a>Изменения в файлах web.config или app.config

- Добавлены следующие записи конфигурации:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Изменения в Azure

- Создана группа ресурсов или использована существующая.
- В указанной группе ресурсов создано хранилище ключей.

