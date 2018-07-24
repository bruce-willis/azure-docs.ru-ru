---
title: Руководство. Отладка веб-приложения Сетки Azure Service Fabric" | Документация Майкрософт
description: В этом руководстве описывается отладка приложения Сетки Azure Service Fabric, работающая в локальном кластере.
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
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e39e5861c0606c43a869fe02a7de2dc9b6f489ea
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125659"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Руководство. Отладка веб-приложения Сетки Azure Service Fabric

Это руководство является вторым из серии руководств и показывает отладку веб-приложения Сетки Azure Service Fabric в локальном кластере разработки.

В этом руководстве описано следующее.

> [!div class="checklist"]
> * Процессы, происходящие при создании приложения Сетки Azure Service Fabric
> * Задание точки останова для наблюдения за вызовом "служба — служба"

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создавать приложение Сетки Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Отладка локального приложения
> * [Публиковать приложение в Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Если у вас еще нет Azure подписки до начала работы, можно [создать бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Убедитесь, что имеется [настроенная среда разработки](service-fabric-mesh-howto-setup-developer-environment-sdk.md), которая включает установку среды выполнения Service Fabric, пакет SDK, Docker и Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Загрузка образца приложения

Можно просто скачать образец приложения, если вы не создавали его в [первой части этой серии руководств](service-fabric-mesh-tutorial-create-dotnetcore.md). В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Приложение находится в каталоге `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Сборка и отладка в локальном кластере

Изображение Docker автоматически создается и развертывается в локальном кластере во время загрузки проекта. Это может занять некоторое время. Чтобы отслеживать ход выполнения работы в панели **Вывод** Visual Studio, установите раскрывающееся меню **Show output from:** (Показать источник вывода:) области вывода для **Средства Service Fabric**.

Нажмите клавишу **F5** для локальной компиляции и запуска службы. При локальной отладке и запуске проекта Visual Studio выполняет следующие действия.

* Проверяет, чтобы инструментарий Docker для Windows был запущен и настроен для управления контейнерами операционной системы Windows.
* Загружает все отсутствующие базовые образы Docker. Это может занять некоторое время
* Собирает (или перестраивает) образ Docker, используемый для размещения кода проекта.
* Выполняет развертывание и запуск контейнера на локальном кластере разработки Service Fabric.
* Запускает службы и выполняет заданные точки останова.

После завершения локального развертывания и запуска приложения Visual Studio в окне браузера откроется веб-страница с образцом по умолчанию.

**Советы по отладке**

* Если обнаружилось, что  **локальный кластер Service Fabric не работает**, убедитесь, что служба Service Local Custer Manager (SLCM) запущена. Для этого на панели задач щелкните правой кнопкой мыши значок SLCM, затем нажмите **Start Local Cluster** (Запустить Локальный кластер). После запуска службы возвращайтесь к Visual Studio и нажмите клавишу **F5**.
* Если при запуске приложения появится ошибка **404**, это скорее всего означает, что переменные среды в **service.yaml** неверны. Убедитесь, что `AppName`, `ApiHostPort` и `ServiceName` установлены соответственно инструкциям из раздела [Установка переменных среды](#set-environment-variables).
* Если возникают ошибки сборки в **service.yaml**, убедитесь, что для задания отступов строки используются пробелы, а не символы табуляции.

### <a name="debug-in-visual-studio"></a>Отладка в Visual Studio

При отладке приложения Сетки Service Fabric в Visual Studio используется локальный кластер разработки Service Fabric. Чтобы увидеть как задачи извлекаются из серверной службы, делайте отладку методом OnGet().
1. В проекте **WebFrontEnd** откройте **Страницы** > **Index.cshtml** > **Index.cshtml.cs** и задайте точку останова методом **Get** (строка 17).
2. В проекте **ToDoService** откройте **TodoController.cs** и задайте точку останова методом **OnGet** (строка 15).
3. Затем вернитесь в браузер и обновите страницу. Вы попадете в точку останова во внешнем интерфейсе методом `OnGet()`. Чтобы увидеть как переменные среды, определенные в файле **service.yaml**, объединены в URL-адрес, используемый для связи с внутренней службой, можно проверить переменную `backendUrl`.
4. Шаг назад (F10) к запросу `client.GetAsync(backendUrl).GetAwaiter().GetResult())` и вы окажетесь в точке останова оператора `Get()`. При помощи этого метода можно увидеть как список работающих элементов извлекается из списка в памяти.
5. После окончания остановите отладку проекта в Visual Studio, нажав комбинацию клавиш **Shift+F5**.
 
## <a name="next-steps"></a>Дополнительная информация

В этой части руководства было показано следующее.

> [!div class="checklist"]
> * Процессы, происходящие при создании приложения Сетки Azure Service Fabric
> * Задание точки останова для наблюдения за вызовом "служба — служба"

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Deploy a Service Fabric Mesh web application](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) (Публикация веб-приложения Сетки Service Fabric)