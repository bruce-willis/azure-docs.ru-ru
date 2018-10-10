---
title: Руководство. Отладка веб-приложения Сетки Azure Service Fabric, выполняющегося в локальном кластере разработки | Документация Майкрософт
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
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 27e4c8f6ac24d40a6afacf10175413745f5151d9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997018"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Руководство по отладке приложения Сетки Service Fabric, выполняющегося в локальном кластере разработки.

Это руководство является вторым из серии руководств. Здесь показано, как создать и отладить приложение Сетки Azure Service Fabric в локальном кластере разработки.

В этом руководстве описано следующее.

> [!div class="checklist"]
> * Процессы, происходящие при создании приложения Сетки Azure Service Fabric
> * Задание точки останова для наблюдения за вызовом "служба — служба"

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание приложения Сетки Service Fabric в Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Отладка приложения Сетки Service Fabric, выполняющегося в локальном кластере разработки
> * [Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Обновление приложения Сетки Service Fabric](service-fabric-mesh-tutorial-upgrade.md)
> * [Удаление ресурсов Сетки Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

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

Сейчас есть проблема, которая приводит сбою подключения к службе при вызове `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())`. Это может произойти при каждом изменении IP-адреса узла. Чтобы устранить эту проблему:

1. Удалите приложения из локального кластера (в Visual Studio выберите **Сборка** > **Очистить решение**).
2. Выберите в диспетчере локального кластера Service Fabric **Остановить локальный кластер** и **Запустить локальный кластер**.
3. Повторно разверните приложение (в Visual Studio нажмите **F5**).

Если обнаружилось, что  **локальный кластер Service Fabric не работает**, убедитесь, что диспетчер локального кластера Service Fabric запущен. Для этого на панели задач щелкните правой кнопкой мыши соответствующий значок, затем нажмите **Запустить Локальный кластер**. После запуска службы возвращайтесь к Visual Studio и нажмите клавишу **F5**.

Если при запуске приложения появится ошибка **404**, это может значить, что переменные среды в **service.yaml** неправильные. Убедитесь, что `ApiHostPort`и `ToDoServiceName` установлены соответственно инструкциям из руководства по [созданию переменных среды](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables).

Если возникают ошибки сборки в **service.yaml**, убедитесь, что для задания отступов строки используются пробелы, а не символы табуляции. Кроме того, теперь необходимо создать приложение, используя английский языковой стандарт.

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
> [Развертывание приложения Сетки Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)