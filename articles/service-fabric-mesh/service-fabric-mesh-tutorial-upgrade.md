---
title: Руководство. Обновление приложения Сетки Azure Service Fabric | Документы Майкрософт
description: Узнайте, как обновить приложение Service Fabric с помощью Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: a734eb7b4efecf14a4d47a660c00d561a5fa6677
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971813"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Руководство. Узнайте, как обновить приложение Service Fabric с помощью Visual Studio

Это руководство является четвертой частью цикла, в котором показано, как обновить приложение Сетки Azure Service Fabric непосредственно из Visual Studio. Обновления затронут как код, так и конфигурацию. Вы увидите, что действия по обновлению и публикации в среде Visual Studio совпадают.

Из этого руководства вы узнаете, как выполнить следующие задачи:
> [!div class="checklist"]
> * Обновление службы сетки Service Fabric с помощью Visual Studio

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание приложения Сетки Service Fabric в Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Отладка приложения Сетки Service Fabric, выполняющегося в локальном кластере разработки](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Обновление приложения Сетки Service Fabric
> * [Очистка ресурсов Сетки Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если вы еще не развернули приложение списка дел, следуйте инструкциям в разделе [Публикация веб-приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

# <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Обновление службы сетки Service Fabric с помощью Visual Studio

В этой статье показано, как независимо обновить микрослужбу в приложении.  В этом примере мы будем изменять службу `WebFrontEnd`, чтобы отобразить категорию задач. Затем мы выполним обновление развернутой службы.

## <a name="modify-the-config"></a>Изменение конфигурации

Обновления могут выполняться из-за изменений в коде или конфигурации или и там, и тут.  Чтобы внести изменения конфигурации, откройте файл `service.yaml` проекта `WebFrontEnd` (который находится на узле **Ресурсы службы**).

В разделе `resources:` измените `cpu:` с 0,5 на 1,0, чтобы подготовиться к увеличению нагрузки на веб-интерфейс. Результат будет выглядеть так:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

## <a name="modify-the-model"></a>Изменение модели

Чтобы внести изменения кода, добавьте свойство `Category` в класс `ToDoItem` в файле `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Затем обновите метод `Load()` в том же файле, чтобы присвоить категорию строке по умолчанию:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Изменение службы

Проект `WebFrontEnd` — это приложение ASP.NET Core с веб-страницей, которая отображает список дел. В проекте `WebFrontEnd` откройте `Index.cshtml` и добавьте две строки, указанные ниже, чтобы отобразить категорию задачи:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Выполните сборку и запуск приложения, чтобы убедиться, что новый столбец категории отображается на веб-странице, в которой перечислены задачи.

## <a name="upgrade-the-app-from-visual-studio"></a>Обновление приложения из Visual Studio

Независимо от того, что вы обновляете — код или конфигурацию (в данном случае мы обновляем и то, и другое), чтобы обновить приложение Сетки Service Fabric, в Azure щелкните правой кнопкой мыши **todolistapp** в Visual Studio и выберите **Опубликовать...**

Дальше появится диалоговое окно **Публикация приложения Service Fabric**.

![Диалоговое окно публикации Сетки Service Fabric в Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Выберите учетную запись Azure и подписку. Убедитесь, что в поле **Расположение** задано расположение, которое использовалось при первоначальной публикации приложения списка дел в Azure. В этой статье используется **восточная часть США**.

Убедитесь, что в поле **Группа ресурсов** задана группа ресурсов, которая использовалась при первоначальной публикации приложения списка дел в Azure.

Убедитесь, что в поле **Реестр контейнеров Azure** указано имя реестра контейнеров Azure, который вы создали при первоначальной публикации приложения списка дел в Azure.

В диалоговом окне публикации нажмите кнопку **Опубликовать**, чтобы обновить приложение списка дел в Azure.

Ход выполнения процесса обновления можно отследить, выбрав панель **Средства Service Fabric** в окне **Вывод** в Visual Studio. После завершения обновления выходные данные в области **Средства Service Fabric** будут отображать IP-адрес и порт приложения в виде URL-адреса.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Откройте веб-браузер и перейдите к URL-адресу, чтобы увидеть работу сайта в Azure. Теперь вы видите веб-страницу, содержащую столбец категории.

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства было показано следующее.
> [!div class="checklist"]
> * Как обновить приложение Сетки Service Fabric с помощью Visual Studio

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Очистка ресурсов Сетки Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)