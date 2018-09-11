---
title: Настройка конвейера CI/CD с использованием задачи сборки эмулятора Azure Cosmos DB
description: Руководство по настройке рабочего процесса сборки и выпуска в Visual Studio Team Services (VSTS) с использованием задачи сборки эмулятора Cosmos DB
services: cosmos-db
keywords: Эмулятор Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783880"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Настройка конвейера CI/CD с использованием задачи сборки эмулятора Azure Cosmos DB в Visual Studio Team Services

Эмулятор Azure Cosmos DB предоставляет локальную среду для разработки, которая эмулирует службу Azure Cosmos DB. Этот эмулятор позволяет разрабатывать и тестировать приложения локально без создания подписки Azure и каких-либо затрат. 

Задача сборки эмулятора Azure Cosmos DB для Visual Studio Team Services (VSTS) выполняет аналогичную роль для среды непрерывной интеграции. Задачу сборки можно использовать для выполнения в эмуляторе тестов в рамках рабочих процессов сборки и выпуска. Эта задача запускает контейнер Docker с уже работающим эмулятором и предоставляет конечную точку, которую можно использовать для других частей определения сборки. Вы можете создать и запустить любое число экземпляров эмулятора, каждый в отдельном контейнере. 

В этой статье показано, как настроить в VSTS конвейер непрерывной интеграции для приложения ASP.NET, которое использует задачу сборки эмулятора Cosmos DB для выполнения тестов. 

## <a name="install-the-emulator-build-task"></a>Установка задачи сборки эмулятора

Чтобы использовать задачу сборки, нам нужно установить ее в организации VSTS. Найдите расширение **Эмулятор Azure Cosmos DB** в [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) и щелкните **Получить бесплатно**.

![Поиск и установка задачи сборки эмулятора Azure Cosmos DB в VSTS из Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Теперь выберите организацию, в которой нужно установить это расширение. 

> [!NOTE]
> Чтобы установить расширение в организации VSTS, нужно являться владельцем учетной записи или администратором коллекции проектов. Если у вас нет нужных разрешений, но вы являетесь участником учетной записи, попробуйте запросить расширения. [Подробнее.](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Выбор организации VSTS, в которой нужно установить расширение](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Создание определения сборки

Теперь, когда расширение установлено, его нужно добавить в [определение сборки.](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav) Вы можете изменить существующее определение сборки или создать новое. Если у вас уже есть определение сборки, переходите к разделу [Добавление задачи сборки эмулятора в определение сборки](#addEmulatorBuildTaskToBuildDefinition).

Чтобы создать определение сборки, откройте в VSTS вкладку **Сборка и выпуск**. Щелкните **+Создать.**

![Создание нового определения сборки](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Выберите для сборки нужный командный проект, репозиторий и ветвь. 

![Выбор командного проекта, репозитория и ветви для определения сборки ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

И наконец, выберите нужный шаблон для определения сборки. В нашем примере для этого руководства выбран шаблон **ASP.NET**. 

![Выбор нужного шаблона определения сборки ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Теперь у нас есть определение сборки, в котором можно настроить использование задачи сборки эмулятора Azure Cosmos DB. Это будет выглядеть примерно так. 

![Шаблон определения сборки ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Добавление задачи в определение сборки

Чтобы добавить задачу сборки эмулятора, найдите в поле поиска **cosmos** и выберите **Добавить**. Задача сборки запускает контейнер с уже выполняющимся экземпляром эмулятора Cosmos DB, поэтому задачу нужно добавить до выполнения других задач, для которых требуется действующий эмулятор.

![Добавление задачи сборки эмулятора в определение сборки](./media/tutorial-setup-ci-cd/addExtension_3.png). В этом руководстве описано, как добавить задачу в начало этапа 1, чтобы эмулятор был доступен до выполнения тестов.
Полное определение сборки теперь выглядит так. 

![Шаблон определения сборки ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Настройка использования эмулятора в тестах
Теперь давайте настроим тесты, чтобы они использовали эмулятор. Задача сборки эмулятора экспортирует переменную среды с именем CosmosDbEmulator.Endpoint, значение которой может использовать любая задача, расположенная дальше в конвейере сборки. 

В этом руководстве описано, как использовать [задачу Visual Studio Test](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md) для запуска модульных тестов, которые настраиваются в файле **.runsettings**. См. дополнительные сведения о [настройке модульных тестов](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

Ниже приведен пример файла **.runsettings**, в котором определены параметры для передачи в модульные тесты приложения. Обратите внимание, что используемая переменная `authKey` является [известным ключом](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) эмулятора. Задача сборки эмулятора ожидает именно этот ключ `authKey`, который нужно определить в файле **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
Эти параметры `TestRunParameters` указываются с помощью свойства `TestContext` в тестовом проекте приложения. Ниже приведен пример теста, который выполняется для Cosmos DB. 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Перейдите к разделу с параметрами выполнения в задаче Visual Studio Test. В параметре **Файл настроек** укажите, что тесты настраиваются с помощью файла **.runsettings**. В параметре **Переопределить параметры тестового запуска** добавьте ` -endpoint $(CosmosDbEmulator.Endpoint)`. Это действие настраивает для тестовой задачи ссылку на другую конечную точку задачи сборки эмулятора вместо той, которая определена в файле **.runsettings**.  

![Переопределение переменной конечной точки для задачи сборки эмулятора](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Запустите сборку.
Теперь сохраните сборку и поместите ее в очередь. 

![Сохраните и запустите сборку.](./media/tutorial-setup-ci-cd/runBuild_1.png)

После запуска сборки вы увидите, что задача эмулятора Cosmos DB запрашивает и получает образ Docker, в котором установлен эмулятор. 

![Сохраните и запустите сборку.](./media/tutorial-setup-ci-cd/runBuild_4.png)

После завершения сборки вы увидите, что все тесты из задачи сборки успешно выполнены для эмулятора Cosmos DB.

![Сохраните и запустите сборку.](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения об [использовании эмулятора Azure Cosmos DB для разработки и тестирования в локальной среде](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

См. дополнительные сведения об [экспорте сертификатов эмулятора Azure Cosmos DB для использования с Java, Python и Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates).
