---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850812"
---
## <a name="prepare-your-repository"></a>Подготовка репозитория

Чтобы получать автоматические сборки с сервера сборки Kudu службы приложений, убедитесь, что корень репозитория содержит нужные файлы проекта.

| Среда выполнения | Файлы в корневом каталоге |
|-|-|
| ASP.NET (только для Windows) | _SLN_-файлы, _CSPROJ_-файлы или _default.aspx_ |
| ASP.NET Core; | _SLN_-файлы или _CSPROJ_-файлы |
| PHP | _index.php_ |
| Ruby (только для Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ или _package.json_ со сценарием запуска |
| Python (только для Windows) | _\*PY_-файлы, _requirements.txt_ или _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ или _iisstart.htm_ |
| Веб-задания | _\<имя_задания>/run.\<extension>_ в папке _App\_Data/jobs/continuous_ (для непрерывных веб-заданий) или в папке _App\_Data/jobs/triggered_ (для активируемых веб-заданий). Дополнительные сведения см. в [документации по веб-заданиям Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Функции Azure | Ознакомьтесь с разделом [Непрерывное развертывание для Функций Azure](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Чтобы настроить развертывание, добавьте в корень репозитория _DEPLOYMENT_-файл. Дополнительные сведения см. в статьях [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Настройка развертываний) и [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Настраиваемый сценарий развертывания).

> [!NOTE]
> Если для разработки используется Visual Studio, позвольте [Visual Studio автоматически создать репозиторий](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). Проект немедленно будет готов к развертыванию с помощью Git.
>
>

