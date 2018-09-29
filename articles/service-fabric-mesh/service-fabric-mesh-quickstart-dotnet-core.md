---
title: Краткое руководство по созданию и развертыванию веб-приложения в службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: В этом кратком руководстве показано, как создать веб-сайт ASP.NET Core и опубликовать его в службе "Сетка Azure Service Fabric".
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7e324b80968017e0160f41b88fa1824669050ac9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407405"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Краткое руководство по созданию и развертыванию веб-приложения в службе "Сетка Azure Service Fabric"

Сетка Azure Service Fabric — это полностью управляемая служба, которая позволяет разработчикам развертывать приложения для микрослужб без управления виртуальными машинами, хранилищем или сетями.

В этом кратком руководстве вы создадите приложение службы "Сетка Service Fabric", состоящее из веб-приложения ASP.NET Core, запустите его в локальном кластере разработки и затем опубликуете его для запуска в Azure.

Вам понадобится подписка Azure. Если ее у вас еще нет, вы можете легко создать [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем приступить к работе. Также необходимо будет [настроить среду разработки](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Создание проекта службы "Сетка Service Fabric"

Откройте Visual Studio и выберите **Файл** > **Создать** > **Проект**.

В диалоговом окне **Новый проект** вверху в поле **Поиск** введите `mesh`. Выберите шаблон **Service Fabric Mesh Application** (Приложение службы "Сетка Service Fabric"). Если шаблон не отображается, убедитесь, что вы установили пакет SDK для Сетки Service Fabric и предварительную версию средств VS, как описано в статье о [настройке среды разработки](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

В поле **Имя** введите **ServiceFabricMesh1**, а в поле **Расположение** укажите путь к папке, в которой вы хотите хранить файлы проекта.

Установите флажок **Create directory for solution** (Создать каталог для решения) и нажмите кнопку **ОК**, чтобы создать проект службы "Сетка Service Fabric".

![Диалоговое окно нового проекта службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>создание службы;

После нажатия кнопки **ОК** откроется диалоговое окно **Служба структуры для новой службы**. Выберите тип проекта **ASP.NET Core**. В поле **Container OS** (ОС контейнера) выберите **Windows** и нажмите кнопку **ОК**, чтобы создать проект ASP.NET Core. 

![Диалоговое окно нового проекта службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Откроется диалоговое окно **Создать веб-приложение ASP.NET Core**. Выберите **Веб-приложение** и нажмите кнопку **ОК**.

![Новое приложение ASP.NET Core в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio создаст проект приложения службы "Сетка Service Fabric" и проект ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Выполнение сборки и публикация в локальном кластере

Образ Docker автоматически создается и развертывается в локальном кластере во время загрузки проекта. Этот процесс может занять некоторое время. При необходимости ход выполнения инструментов Service Fabric можно отслеживать в окне **Вывод**, выбрав **Средства Service Fabric** из раскрывающегося списка окна **Вывод**. Вы можете продолжить работу, пока развертывается образ Docker.

После создания проекта нажмите клавишу **F5**, чтобы выполнить локальную отладку службы. После завершения локального развертывания и запуска проекта в Visual Studio в окне браузера откроется пример веб-страницы.

Завершив просмотр развернутой службы, остановите отладку проекта, нажав клавиши **Shift+F5** в Visual Studio.

## <a name="publish-to-azure"></a>Публикация в Azure

Чтобы опубликовать проект службы "Сетка Service Fabric" в Azure, щелкните правой кнопкой мыши **проект службы "Сетка Service Fabric"** в Visual Studio и выберите **Опубликовать**.

![Щелчок правой кнопкой мыши проекта службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Отобразится диалоговое окно **Опубликовать приложение Service Fabric**.

![Диалоговое окно публикации службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Выберите учетную запись Azure и подписку. Выберите **расположение**. В этой статье используется **восточная часть США**.

В разделе **Группа ресурсов** выберите **\<Создать новую группу ресурсов...>**. Откроется диалоговое окно **Создать группу ресурсов**. Укажите **имя группы ресурсов** и **расположение**.  В этом кратком руководстве используется расположение **Восточная часть США** и группа **sfmeshTutorial1RG** (если в организации несколько человек используют одну и ту же подписку, выберите уникальное имя группы ресурсов).  Нажмите кнопку **Создать**, чтобы создать группу ресурсов и вернуться в диалоговое окно публикации.

![Диалоговое окно новой группы ресурсов службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Вернитесь в диалоговое окно **Опубликовать приложение Service Fabric** в разделе **Реестр контейнеров Azure**, выберите **\<Создать реестр контейнеров Azure...>**. В диалоговом окне **Создать реестр контейнеров** используйте уникальное имя для параметра **Имя реестра контейнеров**. Укажите **расположение** (в этом кратком руководстве используется **Восточная часть США**). Выберите в раскрывающемся списке **группу ресурсов**, созданную на предыдущем шаге, например **sfmeshTutorial1RG**. Задайте для параметра **SKU** значение **Базовый** и нажмите кнопку **Создать**, чтобы вернуться в диалоговое окно публикации.

![Диалоговое окно новой группы ресурсов службы "Сетка Service Fabric" в Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

Чтобы развернуть приложение службы "Сетка Service Fabric" в Azure, в диалоговом окне публикации нажмите клавишу **Опубликовать**.

При первой публикации в Azure образ Docker помещается в службу "Реестр контейнеров Azure" (ACR), на что требуется определенное время в зависимости от размера образа. Следующие публикации того же проекта будут проходить быстрее. Ход выполнения развертывания можно отследить, выбрав **Средства Service Fabric** в раскрывающемся списке окна **Вывод** в Visual Studio. После завершения развертывания выходные данные в области **Средства Service Fabric** будут отображать IP-адрес и порт приложения в виде URL-адреса.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Откройте веб-браузер и перейдите по URL-адресу, чтобы увидеть работу сайта в Azure.

![Запуск веб-приложения службы "Сетка Service Fabric"](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все созданные для этого краткого руководства ресурсы, если они больше не нужны. Так как была создана группа ресурсов для размещения ресурсов службы ACR и службы "Сетка Service Fabric", можно безопасно удалить эту группу ресурсов, что приведет к удалению всех связанных ресурсов.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Кроме того, группу ресурсов можно удалить [с помощью портала Azure](https://portal.azure.com).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о создании и развертывании приложений службы "Сетка Service Fabric" доступны в руководстве.
> [!div class="nextstepaction"]
> [Создание, отладка и развертывание веб-приложения на базе нескольких служб в Сетке Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)