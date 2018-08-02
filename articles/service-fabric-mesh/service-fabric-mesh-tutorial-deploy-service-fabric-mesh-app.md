---
title: Руководство. Развертывание приложения Сетки Service Fabric | Документация Майкрософт
description: Узнайте, как опубликовать приложение Сетки Azure Service Fabric, состоящее из веб-сайта ASP.NET Core, который обменивается данными с северной веб-службой.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/26/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 350749161260768071afbb47b854cb2e9184bd9d
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284733"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Руководство. Развертывание приложения Сетки Service Fabric

Это руководство является третьей частью цикла, в котором показано, как публиковать веб-приложение Сетки Azure Service Fabric непосредственно из Visual Studio.

Из этого руководства вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * Публикация приложения в Azure.
> * Проверка состояния развертывания приложения
> * Обзор всех приложений, развернутых сейчас в подписке
> * Обзор журналов приложений
> * Очистка ресурсов, используемых в приложении.

Из этой серии руководств вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * [Создание веб-приложения Сетки Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Отладка локального приложения](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Публикация приложения в Azure

Здесь описывается, как создать приложение Сетки Azure Service Fabric с веб интерфейсом ASP.NET и серверной службой веб-API ASP.NET Core. Затем выполнение отладки приложения в локальном кластере разработки и публикация приложения в Azure. По окончанию у вас будет простое приложение, которое демонстрирует, как сделать вызов типа "служба — служба" в приложении Сетки Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет Azure подписки до начала работы, можно [создать бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Убедитесь, что имеется [настроенная среда разработки](service-fabric-mesh-howto-setup-developer-environment-sdk.md), которая включает установку среды выполнения Service Fabric, пакет SDK, Docker и Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Загрузка образца приложения

Если вы не создавали пример приложения [во второй части этой серии руководств](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), можете скачать его. В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Приложение находится в каталоге `src\todolistapp`.

## <a name="publish-to-azure"></a>Публикация в Azure

Чтобы опубликовать проект Сетки Service Fabric в Azure, щелкните правой кнопкой мыши **ServiceFabricMeshApp** в Visual Studio и выберите **Опубликовать**

Дальше появится диалоговое окно **Публикация приложения Service Fabric**.

![Диалоговое окно публикации Сетки Service Fabric в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Выберите учетную запись Azure и подписку. Выберите **расположение**. В этой статье используется **восточная часть США**.

В разделе **Группа ресурсов** выберите **\<Создать новую группу ресурсов...>**. Откроется диалоговое окно, в котором создайте новую группу ресурсов. В этой статье используется расположение **восточная часть США** и имена группы **sfmeshTutorial1RG** (если в организации несколько человек используют одну и ту же подписку, выберите уникальное имя группы).  Нажмите клавишу **Создать**, чтобы создать группу ресурсов и вернитесь в диалоговое окно публикации.

![Диалоговое окно новой группы ресурсов Сетки Service Fabric в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Вернитесь в диалоговое окно **Опубликовать приложение Service Fabric** в разделе **Реестр контейнеров Azure**, выберите **\<Создать реестр контейнеров Azure...>**. В диалоговом окне **Создать реестр контейнеров** используйте уникальное имя для **Имя реестра контейнеров**. Укажите **Расположение** (в этом руководстве используется **восточная часть США**). Выберите в раскрывающемся списке **Группу ресурсов**, созданную на предыдущем шаге, например **sfmeshTutorial1RG**. Задайте номер **SKU** для **Базовый** и нажмите клавишу **Создать**, чтобы вернуться в диалоговое окно публикации.

![Диалоговое окно новой группы ресурсов Сетки Service Fabric в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Если возникает ошибка, что поставщик ресурсов не зарегистрирован для вашей подписки, можно его зарегистрировать. Сначала определите, доступен ли поставщик ресурсов для вашей подписки.

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Если поставщик реестра контейнеров (`Microsoft.ContainerRegistry`) доступен, зарегистрируйте его с помощью PowerShell.

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Чтобы развернуть приложение Service Fabric в Azure, в диалоговом окне публикации нажмите клавишу **Опубликовать**.

При первой публикации в Azure образ docker помещается в Реестр контейнеров Azure (ACR), на что требуется определенное время в зависимости от размера образа. Следующие публикации того же проекта будут проходить быстрее. Ход выполнения процесса развертывания можно отследить, выбрав панель **Средства Service Fabric** в окне **Вывод** в Visual Studio. После завершения развертывания выходные данные **Средства Service Fabric** будут отображать IP-адрес и порт приложения в виде URL-адреса.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Откройте веб-браузер и перейдите к URL-адресу, чтобы увидеть работу сайта в Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric 
Чтобы выполнить остальные шаги, можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Установите модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив следующие [инструкции](service-fabric-mesh-howto-setup-cli.md).


## <a name="check-application-deployment-status"></a>Проверка состояния развертывания приложения

На этом этапе приложение развернуто. Можно проверить его состояние с помощью команды `app show`. 

Имя приложения для учебного приложения — `ServiceMeshApp`. Соберите сведения о приложении с помощью следующей команды.

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Обзор всех приложений, развернутых сейчас в подписке

Чтобы получить список приложений, которые развернуты в подписке, можно использовать команду "app list".

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Обзор журналов приложений

Изучите журналы для развернутого приложения.

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все созданные ресурсы, если они больше не нужны. Поскольку была создана группа ресурсов для размещения ресурсов службы ACR и Сетки Service Fabric, можно безопасно удалить эту группу ресурсов, что приведет к удалению всех связанных ресурсов.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Кроме того, можно удалить группу ресурсов [из портала](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства было показано следующее.
> [!div class="checklist"]
> * Публикация приложения в Azure.
> * Проверка состояния развертывания приложения
> * Обзор всех приложений, развернутых сейчас в вашей подписке
> * Обзор журналов приложений
> * Очистка ресурсов, используемых в приложении.

После завершения публикации приложения Сетки Service Fabric в Azure попробуйте следующее.

* Чтобы увидеть еще один пример связи между службами, изучите [пример приложения Voting](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
* Прочтите [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Введение в модель ресурса Service Fabric)
* Ознакомьтесь с [Обзором Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest