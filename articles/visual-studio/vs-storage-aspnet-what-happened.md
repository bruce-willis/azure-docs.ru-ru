---
title: Что произошло с моим проектом ASP.NET? | Документация Майкрософт
description: Сведения о том, что происходит после добавления службы хранилища Azure в проект ASP.NET с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a6e05d706d54d63695861b03cd9de0e65ebdd8bb
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146579"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Что произошло с моим проектом ASP.NET (подключенными к службе хранилища Azure службами Visual Studio)?
## <a name="references-added"></a>Добавлены ссылки
Пакет NuGet хранилища Azure был добавлен в проект Visual Studio.  
Этот пакет добавляет следующие ссылки .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Добавлена строка подключения к хранилищу Azure
В файле web.config проекта был создан элемент с ключом и строкой подключения выбранной учетной записи хранилища.

Дополнительные сведения см. в разделе [ASP.NET](http://www.asp.net).

