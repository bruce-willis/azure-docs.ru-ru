---
title: Использование проекта базы данных U-SQL в разработке базы данных U-SQL для Azure Data Lake | Документация Майкрософт
description: Сведения о разработке базы данных U-SQL с помощью Средства Azure Data Lake для Visual Studio.
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
ms.openlocfilehash: 1fee28ad185629b0cb26abb54cc2e196fca791e5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364055"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Использование проекта базы данных U-SQL в разработке базы данных U-SQL для Azure Data Lake

База данных U-SQL предоставляет структурированные представления по неструктурированным данным и управляемым структурированным данным в таблицах. Она также предоставляет общую систему каталогов метаданных для организации структурированных данных и пользовательского кода. База данных объединяет эти связанные объекты.

См. дополнительные сведения о [базе данных U-SQL и языке описания данных (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Проект базы данных U-SQL — это тип проекта в Visual Studio, который помогает разработчикам быстро и легко разрабатывать, развертывать и администрировать свои базы данных U-SQL.

## <a name="create-a-u-sql-database-project"></a>Создание проекта базы данных U-SQL

Начиная с версии 2.3.3000.0, в Средства Azure Data Lake для Visual Studio добавлен новый шаблон проекта базы данных U-SQL. Чтобы создать проект U-SQL, выберите **Файл > Создать > Проект**. Проект Базы данных U-SQL можно найти в разделе **Azure Data Lake > узел U-SQL**.

![Создание проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Разработка объектов базы данных U-SQL с помощью проекта базы данных

Правой кнопкой мыши выберете проект базы данных U-SQL. Выберете **Добавить > Новый элемент**. Все новые поддерживаемые типы объектов можно найти в мастере **Добавить новый элемент**. 

Для объекта, не являющегося сборкой, например функции с табличным значением, сценарий U-SQL создается после добавления нового элемента. Можно начать разработку инструкции DDL для этого объекта в редакторе.

Для объекта сборки этот инструмент предоставляет удобный пользовательский интерфейс, который помогает регистрировать сборку и развертывать файлы DLL и другие дополнительные файлы. Чтобы добавить определение объекта сборки в проект базы данных U-SQL, выполните следующие действия.

1.  Добавьте ссылки на проект C#, который включает UDO/UDAG/UDF для проекта базы данных U-SQL.

    ![Добавьте ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Добавьте ссылки на проект базы данных U-SQL с помощью Средств Data Lake для Visual Studio.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  В представлении конструкции сборки выберите ссылочную сборку из раскрывающегося меню **Создать сборку из ссылки**.

    ![Создание сборки из ссылки с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  При необходимости добавьте **Управляемые зависимости** и **Дополнительные файлы**. При добавлении дополнительных файлов средство будет использовать относительный путь, чтобы позже обеспечить возможность определения сборки на локальном компьютере и компьютере построения. 

@_DeployTempDirectory — это предварительно определенная переменная, которая указывает средству на выходную папку сборки. В выходной папке сборки каждая сборка имеет вложенную папку с именем сборки. Все библиотеки DLL, а также дополнительные файлы, находятся в этой вложенной папке. 
 
## <a name="build-a-u-sql-database-project"></a>Создание проекта базы данных U-SQL

Выходные данные сборки для проекта базы данных U-SQL представляют собой пакет развертывания базы данных U-SQL с суффиксом `.usqldbpack`. Пакет `.usqldbpack` — это ZIP-файл, который объединяет все инструкции DDL в единый сценарий U-SQL в папке **DDL**, а также все библиотеки DLL и дополнительные файлы для сборок в папке **Temp**.

Дополнительные сведения см. в разделе [Создание проекта базы данных U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Развертывание базы данных U-SQL

Пакет .usqldbpack можно развернуть для локальной учетной записи или учетной записи Azure Data Lake Analytics с помощью Visual Studio или соответствующего пакета SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Развертывание базы данных U-SQL в Visual Studio

Вы можете развернуть базу данных U-SQL, используя проект базы данных U-SQL или пакет .usqldbpack в среде Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Развертывание с помощью проекта базы данных U-SQL

1.  Щелкните правой кнопкой мыши проект базы данных U-SQL и выберите **Развернуть**.
2.  В **мастере развертывания Баз данных U-SQL**выберите **учетную запись ADLA**, для которой требуется выполнить развертывание базы данных. Поддерживаются оба типа учетных записей — локальные и ADLA.
3.  **Источники базы данных** заполняются автоматически, указывая на пакет .usqldbpack в выходной папке сборки проекта.
4.  Введите имя в раздел **Имя базы данных**, чтобы создать базу данных. Если база данных с этим именем уже существует в целевой учетной записи Azure Data Lake Analytics, все объекты, указанные в проекте базы данных, создаются без ее повторного создания.
5.  Щелкните **Отправить** для развертывания базы данных U-SQL. Все ресурсы (сборки и дополнительные файлы) загружаются, и отправляется задание U-SQL, которое включает все заявления DDL.

    ![Развертывание проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Мастер развертывания проекта базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Развертывание с помощью пакета развертывания базы данных U-SQL

1.  Откройте **обозреватель сервера**. Затем разверните **учетную запись Azure Data Lake Analytics**, для которой требуется выполнить развертывание базы данных.
2.  Щелкните правой кнопкой мыши **Базу данных U-SQL**, а затем выберите **Развернуть базу данных**.
3.  Задайте в качестве пути к пакету развертывания базы данных U-SQL (файл с расширением .usqldbpack) **источник базы данных**.
4.  Введите имя в раздел **Имя базы данных**, чтобы создать базу данных. Если база данных с этим именем уже существует в целевой учетной записи Azure Data Lake Analytics, все объекты, указанные в проекте базы данных, создаются без ее повторного создания.

    ![Развертывание пакета базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Мастер развертывания пакета базы данных U-SQL с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Развертывание базы данных U-SQL с помощью пакета SDK

`PackageDeploymentTool.exe` предоставляет программный интерфейс и интерфейс командной строки, которые позволяют развернуть базы данных U-SQL. Пакет SDK включен в [пакет U-SQL SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), размещенный в файле `build/runtime/PackageDeploymentTool.exe`.

См. дополнительные сведения об [использовании пакета SDK и настройке конвейера CI/CD для развертывания базы данных U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Создание ссылки на проект базы данных U-SQL

Проект U-SQL может ссылаться на проект базы данных U-SQL. Ссылка влияет на две рабочие нагрузки:

- *Сборка проекта*. Перед созданием сценариев U-SQL настройте связанные среды баз данных. 
- *Локальная учетная запись (локальный проект)*. Связанные среды баз данных развертываются в (локальном проекте) до выполнения сценария U-SQL. Дополнительные сведения см. в разделе [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Добавление ссылки на базу данных U-SQL

1. В **обозревателе решений** щелкните проект U-SQL правой кнопкой мыши и выберите **Добавить ссылку на Базу данных U-SQL...**.

    ![Добавление ссылки на проект базы данных с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Настройте ссылку на базу данных из проекта базы данных U-SQL в текущем решении или файле пакета базы данных U-SQL.
3. Укажите имя базы данных.

    ![Мастер добавления ссылки на проект базы данных с помощью Средств Data Lake для Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Дополнительная информация

- [Настройка конвейера CI/CD для Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Тестирование кода Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
