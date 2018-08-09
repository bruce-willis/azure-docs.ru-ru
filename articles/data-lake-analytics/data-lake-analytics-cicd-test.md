---
title: Тестирование кода Azure Data Lake Analytics | Документация Майкрософт
description: Сведения о добавлении тестовых случаев в расширенный код C# и U-SQL для Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: fa5c113541452a93c25adc7c14bdaa6994434c71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365866"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Тестирование кода Azure Data Lake Analytics

Azure Data Lake предоставляет язык U-SQL, который объединяет декларативный язык SQL и императивный язык C# для обработки данных в любом масштабе. В этом документе описано, как создавать тестовые случаи для U-SQL и расширенного кода C# UDO (определяемых пользователем операторов).

## <a name="test-u-sql-scripts"></a>Тестирование скриптов U-SQL

Сценарий U-SQL компилируется и оптимизируется для исполняемого кода для выполнения на нескольких компьютерах в облачной или локальной средах. В процессе компиляции и оптимизации обрабатывается весь сценарий U-SQL. Невозможно выполнить традиционные "модульные тесты" для каждой инструкции. Но используя тестовый пакет SDK для U-SQL и пакет SDK для запуска в локальной среде, можно выполнить тесты на уровне сценариев.

### <a name="create-test-cases-for-u-sql-script"></a>Создание тестовых случаев для скриптов U-SQL

Средства Azure Data Lake для Visual Studio позволяют создавать тестовые случаи для скриптов U-SQL.

1.  В обозревателе решений щелкните правой кнопкой мыши сценарий U-SQL, а затем выберите **Создать модульный тест**.
2.  Создайте новый тестовый проект или вставьте тестовый случай в существующий тестовый проект.

    ![Data Lake Tools для Visual Studio — создание тестового проекта U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Создание конфигурации тестового проекта U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Управление источником тестовых данных

При тестировании сценариев U-SQL требуются файлы с тестовыми входными данными. Вы можете управлять тестовыми данными, настроив **источник тестовых данных** в свойствах проекта U-SQL. 

При вызове интерфейса `Initialize()` в тестовом пакете SDK для U-SQL в рабочей папке тестового проекта создается временная локальная корневая папка данных, в которую перед запуском тестовых случаев сценария U-SQL копируются все файлы и вложенные папки (и файлы в этих вложенных папках) в исходной папке с тестовыми данными. Можно добавить дополнительные папки источника тестовых данных, разделив пути к папкам с тестовыми данными точкой с запятой.

![Data Lake Tools для Visual Studio — настройка источника тестовых данных проекта](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Управление средой базы данных для тестирования

Если сценарии U-SQL используют объекты базы данных U-SQL или обращаются к ним (например, вызывая хранимые процедуры), вам нужно инициализировать среду базы данных перед запуском тестов U-SQL. Интерфейс `Initialize()` в тестовом пакете SDK для U-SQL позволяет развернуть все базы данных, на которые ссылается проект U-SQL, во временную локальную корневую папку данных в рабочей папке тестового проекта. 

Дополнительные сведения см. в разделе [Создание ссылки на проект базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Проверка результатов теста

Интерфейс `Run()` возвращает результат выполнения задания. 0 означает успешно выполнено, а 1 — сбой. Чтобы проверить выходные данные, можно также использовать функции assert в C#. 

### <a name="run-test-cases-in-visual-studio"></a>Запуск тестовых случаев в Visual Studio

Тестовый проект сценария U-SQL создается на базе платформы модульного тестирования C#. После создания проекта можно запустить все тестовые примеры с помощью **Обозреватель тестов> Список воспроизведения**. Кроме того, щелкните правой кнопкой мыши файл .cs, а затем выберите **Выполнить тесты**.

## <a name="test-c-udos"></a>Тестирование определяемых пользователем операторов C#

### <a name="create-test-cases-for-c-udos"></a>Создание тестовых случаев для определяемых пользователем операторов C#

Платформу модульного тестирования C# можно использовать для тестирования определяемых пользователем операторов (UDO) C#. При тестировании UDO необходимо подготовить соответствующий объект **IRowset** в качестве входных данных.

Объект IRowset можно создать двумя способами.

- Загрузить данные из файла для создания IRowset.

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Использовать данные из коллекции данных для создания IRowset.

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Проверка результатов теста

После вызова функций UDO результат можно проверить, используя схемы и проверку значения Rowset с помощью функций assert в C#. Можно использовать пример кода в примере проекта модульного теста U-SQL UDO C#, выбрав **Файл > Создать > Проект** в Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Запуск тестовых случаев в Visual Studio

Создав тестовый проект, можно выполнить все тестовые случаи. Для этого выберите **Обозреватель тестов > Список воспроизведения** или щелкните правой кнопкой мыши файл CS и выберите **Выполнить тесты**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Запуск тестовых случаев в Visual Studio Team Service

Оба проекта, **тестовые проекты сценария U-SQL** и **тестовые проекты UDO C#**, наследуют проекты модульного теста C#. Эти тестовые случаи можно выполнять с помощью [задачи тестирования Visual Studio](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) в Visual Studio Team Services. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Запуск тестовых случаев U-SQL в Visual Studio Team Service

Перед тестированием U-SQL убедитесь, что `CPPSDK` загружен на компьютер сборки, а затем передайте путь `CPPSDK` в USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Что такое CPPSDK?**

CPPSDK — это пакет, включающий Microsoft Visual C++ 14 и Windows SDK 10.0.10240.0. Это среда, необходимая для среды выполнения U-SQL. Этот пакет можно получить в папке установки Средств Azure Data Lake для Visual Studio.

- Для Visual Studio 2015: `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`.
- Для Visual Studio 2017: `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`.

**Подготовка CPPSDK в агенте сборки Visual Studio Team Services**

Наиболее распространенный способ подготовки зависимости CPPSDK в Visual Studio Team Service заключается в следующем.

1.  Упакуйте в ZIP-файл папку с библиотеками CPPSDK.
2.  Добавьте ZIP-файл в систему управления версиями (ZIP-файл гарантирует, что проверяются все библиотеки в папке CPPSDK, чтобы некоторые файлы не игнорировались .gitignore).   
3.  Распакуйте ZIP-файл в конвейер сборки.
4.  Создайте для `USqlScriptTestRunner` ссылку на эту распакованную папку на компьютере сборки.

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>Запуск тестовых случаев для определяемых пользователем операторов C# в Visual Studio Team Services

Для тестирования определяемых пользователем операторов C# создайте ссылки на следующие требуемые сборки. Если вы ссылаетесь на них с помощью [Microsoft.Azure.DataLake.USQL.Interfaces пакета Nuget ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), добавьте задачу восстановления NuGet в конвейер сборки.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Дополнительная информация

- [Настройка конвейера CI/CD для Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
- [Использование проекта базы данных U-SQL для разработки базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

