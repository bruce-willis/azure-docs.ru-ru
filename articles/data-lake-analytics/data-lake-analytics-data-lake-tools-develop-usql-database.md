---
title: Использование проекта базы данных U-SQL для разработки базы данных U-SQL для Azure Data Lake | Документация Майкрософт
description: Узнайте, как разработать базу данных U-SQL с помощью Средств Azure Data Lake для Visual Studio.
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
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890904"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Использование проекта базы данных U-SQL для разработки базы данных U-SQL для Azure Data Lake

Базы данных U-SQL обеспечивает структурированные представления неструктурированных данных, управляет структурированными данными в таблицах и предоставляет общую систему каталога метаданных для упорядочивания структурированных данных и пользовательского кода. База данных объединяет эти связанные объекты.

См. дополнительные сведения о [базе данных U-SQL и языке описания данных (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Проект базы данных U-SQL — это тип проекта в Visual Studio, который помогает разработчикам быстро и легко разрабатывать, развертывать и администрировать свои базы данных U-SQL.

## <a name="create-a-u-sql-database-project"></a>Создание проекта базы данных U-SQL

Начиная с версии 2.3.3000.0, в Средства Azure Data Lake для Visual Studio добавлен новый шаблон проекта базы данных U-SQL. Чтобы создать проект U-SQL, выберите **Файл > Создать > Проект**. Проект базы данных U-SQL можно найти в разделе **Azure Data Lake > Узел U-SQL**.

![Создание проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Разработка объектов базы данных U-SQL с помощью проекта базы данных

Щелкните правой кнопкой мыши проект базы данных U-SQL и выберите **Добавить > Новый элемент**. Все поддерживаемые типы объектов можно найти в мастере добавления нового элемента. 

1.  Для объекта, не являющегося сборкой, например функции с табличным значением, скрипт U-SQL создается после добавления нового элемента. Можно начать разработку инструкции DDL для этого объекта в редакторе.
2.  Для объекта сборки средство предоставляет удобный редактор пользовательского интерфейса, который поможет зарегистрировать сборку, а также развернуть файлы DLL и другие дополнительные файлы. Чтобы добавить определение объекта сборки в проект базы данных U-SQL, сделайте следующее:

1.  Добавьте ссылки на проект C# (UDO/UDAG/UDF) в проект базы данных U-SQL.

    ![Добавление ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Добавление ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  В окне конструктора сборок выберите в раскрывающемся списке **Создать сборку из ссылки** сборки, на которые указывают ссылки.

    ![Создание сборки из ссылки с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  При необходимости добавьте **управляемые зависимости** и **дополнительные файлы**. При добавлении дополнительных файлов средство будет использовать относительный путь, чтобы позже обеспечить возможность определения сборки на локальном компьютере и компьютере построения. @_DeployTempDirectory — это предварительно определенная переменная, которая указывает средству на выходную папку сборки. Выходные данные сборки включают вложенную папку с таким же именем. Там расположены все библиотеки DLL и дополнительные файлы для этой сборки. 
 
## <a name="build-u-sql-database-project"></a>Создание проекта базы данных U-SQL

Выходные данные сборки для проекта базы данных U-SQL — это пакет развертывания базы данных U-SQL с именем, которое содержит суффикс `.usqldbpack`. Пакет `.usqldbpack` — это ZIP-файл, который объединяет все инструкции DDL в единый скрипт U-SQL в папке **DDL**, а также все библиотеки DLL и дополнительные файлы для сборок в папке **Temp**.

См. дополнительные сведения о [создании проекта базы данных U-SQL с помощью командной строки MSBuild и задачи сборки Visual Studio Team Service](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Развертывание базы данных U-SQL

Вы можете развернуть пакет .usqldbpack для локальной учетной записи или учетной записи Azure Data Lake Analytics с помощью Visual Studio или соответствующего пакета SDK. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Развертывание базы данных U-SQL в Visual Studio

Вы можете развернуть базу данных U-SQL, используя проект базы данных U-SQL или пакет .usqldbpack в среде Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Развертывание с помощью проекта базы данных U-SQL

1.  Щелкните правой кнопкой мыши проект базы данных U-SQL и выберите **Развернуть**.
2.  В мастере развертывания базы данных U-SQL выберите **учетную записи ADLA**, в которую будет развернута эта база данных. Поддерживаются оба типа учетных записей — (Local) и ADLA.
3.  **Источники базы данных** заполняются автоматически, указывая на пакет .usqldbpack в выходной папке сборки проекта.
4.  Введите **имя базы данных**, чтобы создать базу данных. Если база данных с этим именем уже существует в целевой учетной записи Azure Data Lake Analytics, все объекты, определенные в проекте базы данных, будут созданы без повторного создания базы данных.
5.  Щелкните **Отправить** для развертывания базы данных SQL. Будут отправлены все ресурсы (сборки и дополнительные файлы) и передано задание U-SQL, которое включает все инструкции DDL.

    ![Развертывание проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Мастер развертывания проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Развертывание с помощью пакета развертывания базы данных U-SQL

1.  В **обозревателе серверов** разверните **учетную запись Azure Data Lake Analytics**, в которую будет развернута база данных.
2.  Щелкните правой кнопкой мыши базу данных U-SQL и выберите **Развернуть базу данных**.
3.  Задайте в качестве пути к пакету развертывания базы данных U-SQL (файл с расширением .usqldbpack) **источник базы данных**.
4.  Введите **имя базы данных**, чтобы создать базу данных. Если база данных с этим именем уже существует в целевой учетной записи Azure Data Lake Analytics, все объекты, определенные в проекте базы данных, будут созданы без повторного создания базы данных.

    ![Развертывание пакета базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Мастер развертывания пакета базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Развертывание базы данных U-SQL с помощью пакета SDK

`PackageDeploymentTool.exe` предоставляет программный интерфейс и интерфейс командной строки, которые позволяют развернуть базы данных U-SQL. Пакет SDK включен в [пакет U-SQL SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), размещенный в файле `build/runtime/PackageDeploymentTool.exe`.

См. дополнительные сведения об [использовании пакета SDK и настройке конвейера CI/CD для развертывания базы данных U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Создание ссылки на проект базы данных U-SQL

Проект U-SQL может ссылаться на проект базы данных U-SQL. Ссылка влияет на две рабочие нагрузки:

- Сборка проекта — среды баз данных, на которые указывает ссылка, настраиваются перед созданием скриптов U-SQL. 
- Запуск в локальной среде в учетной записи (Local-project) — среды баз данных, на которые указывает ссылка, развертываются в учетную запись (Local-project) перед выполнением скрипта U-SQL. См. дополнительные сведения о [разнице между учетными записями (Local-machine) и (Local-project)](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Добавление ссылки на базу данных U-SQL

1. В **обозревателе решений** щелкните проект U-SQL правой кнопкой мыши и выберите **Добавить ссылку на базы данных U-SQL**.

    ![Добавление ссылки на проект базы данных с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Настройте ссылку на базу данных из проекта базы данных U-SQL в текущем решении или файле пакета базы данных U-SQL.
3. Укажите имя базы данных.

    ![Мастер добавления ссылки на проект базы данных с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка конвейера CI/CD для Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Тестирование кода Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
