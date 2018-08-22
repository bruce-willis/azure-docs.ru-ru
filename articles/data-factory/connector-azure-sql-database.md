---
title: Копирование данных в базу данных SQL Azure и из нее с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в базу данных SQL Azure или из базы данных SQL в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 59a48d06ca3c3bced73ed4043691b33784a4f81a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006033"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Версия 1](v1/data-factory-azure-sql-connector.md)
> * [Текущая версия](connector-azure-sql-database.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в базу данных SQL Azure или из нее. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из базы данных SQL Azure можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Вы можете скопировать данные из любого поддерживаемого хранилища исходных данных в базу данных SQL Azure. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель базы данных SQL Azure поддерживает такие функции:

- копирование данных с использованием проверки подлинности SQL и проверки подлинности по маркерам приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемым удостоверением службы (MSI);
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- добавление данных в целевую таблицу или вызов хранимой процедуры во время копирования с помощью пользовательской логики (в качестве приемника).

> [!IMPORTANT]
> При копировании данных с использованием среды выполнения интеграции фабрики данных Azure настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он разрешал службам Azure получать доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. В этот диапазон входит IP-адрес компьютера, который используется для подключения к базе данных SQL Azure.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для соединителя базы данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства **type** необходимо задать значение **AzureSqlDatabase**. | Yes |
| connectionString | В свойстве **connectionString** указываются сведения, необходимые для подключения к экземпляру базы данных SQL Azure. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет  |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL.](#sql-authentication)
- [Аутентификация по маркеру приложения Azure AD на основе субъекта-службы.](#service-principal-authentication)
- [Аутентификация по маркеру приложения Azure AD на основе управляемого удостоверения службы.](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Аутентификация SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Чтобы использовать проверку подлинности по маркеру приложения Azure AD на основе субъекта-службы, выполните следующие действия:

1. **[Создайте приложение Azure Active Directory на портале Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратор Azure AD должен быть пользователем Azure AD или группой Azure AD, но он не может быть субъектом-службой. Этот шаг нужен, чтобы затем можно было использовать удостоверение Azure AD для создания пользователя автономной базы данных для субъекта-службы.

1. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к базе данных, из которой или в которую требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Настройте связанную службу базы данных SQL Azure** в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>Аутентификация на основе управляемого удостоверения службы

Фабрика данных может быть связана с [управляемым удостоверением службы](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Вы можете использовать это удостоверение службы для проверки подлинности базы данных SQL Azure. Назначенная фабрика может получить доступ и скопировать данные из вашей базы данных или в нее, используя этот идентификатор.

Чтобы использовать проверку подлинности по маркеру приложения Azure AD на основе управляемого удостоверения службы, выполните следующие действия:

1. **Создайте группу в Azure AD.** Сделайте MSI фабрики членом группы.

    a. Найдите удостоверение службы фабрики данных на портале Azure. Перейдите к **свойствам** фабрики данных. Скопируйте идентификатор удостоверения службы.

    b. Установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Войдите с помощью команды `Connect-AzureAD`. Выполните следующие команды для создания группы и добавления MSI фабрики данных в качестве члена группы.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратором Azure AD может быть пользователь Azure AD или группа Azure AD. Если вы предоставили группе с MSI роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

1. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для группы Azure AD. Подключитесь к базе данных, из которой или в которую требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Предоставьте группе Azure AD необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Например, запустите следующий код:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Настройте связанную службу базы данных SQL Azure** в фабрике данных Azure.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Пример использования проверки подлинности на основе MSI в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services). Этот раздел содержит список свойств, поддерживаемых набором данных базы данных SQL Azure.

Чтобы скопировать данные в базу данных SQL Azure или из нее, задайте для свойства **type** набора данных значение **AzureSqlTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** для набора данных должно иметь значение: **AzureSqlTable**. | Yes |
| tableName | Имя таблицы или представления в экземпляре базы данных SQL Azure, на которое ссылается связанная служба. | Yes |

#### <a name="dataset-properties-example"></a>Пример свойств набора данных

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником базы данных SQL Azure.

### <a name="azure-sql-database-as-the-source"></a>База данных SQL Azure в качестве источника

Чтобы скопировать данные из базы данных SQL Azure, задайте для свойства **type** источника в действии копирования значение **SqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** источника действия копирования должно иметь значение **SqlSource**. | Yes |
| SqlReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. | Нет  |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет  |
| storedProcedureParameters | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет  |

### <a name="points-to-note"></a>Примечания

- Если для **SqlSource** указано **sqlReaderQuery**, то действие копирования выполняет этот запрос к источнику базы данных SQL Azure для получения данных. Кроме того, можно создать хранимую процедуру. Укажите параметр **sqlReaderStoredProcedureName** и **storedProcedureParameters**, если хранимая процедура принимает параметры.
- Если не указать **sqlReaderQuery** или **sqlReaderStoredProcedureName**, то столбцы, определенные в разделе **structure** набора данных JSON, будут использоваться для построения запроса. `select column1, column2 from mytable` выполняется по отношению к базе данных SQL Azure. Если в определении набора данных нет раздела **structure**, выбираются все столбцы из таблицы.
- При использовании **sqlReaderStoredProcedureName** по-прежнему необходимо указать фиктивное свойство **tableName** в наборе данных JSON.

#### <a name="sql-query-example"></a>Пример SQL-запроса

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Пример хранимой процедуры

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Определение хранимой процедуры

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>База данных SQL Azure в качестве приемника

Чтобы скопировать данные в базу данных SQL Azure, задайте для свойства **type** приемника в действии копирования значение **SqlSin**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** приемника действия копирования должно иметь значение **SqlSink**. | Yes |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения **writeBatchSize**.<br/> Допустимое значение: **целое число** (количество строк). | Нет. Значение по умолчанию — 10000. |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/> Допустимое значение — **timespan**. Пример: 00:30:00 (30 минут). | Нет  |
| preCopyScript | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в базу данных SQL Azure. Он вызывается однократно при каждом запуске копирования. Это свойство используется для очистки предварительно загруженных данных. | Нет  |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. Например, можно определить выполнение операций upsert или преобразований с помощью вашей собственной бизнес-логики. <br/><br/>Эта хранимая процедура будет **вызываться для каждого пакета**. Для операций, которые выполняются только один раз и не имеют ничего общего с исходными данными, используйте свойство `preCopyScript`. Примеры операций: delete и truncate. | Нет  |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет  |
| sqlWriterTableType | Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. | Нет  |

> [!TIP]
> Когда вы копируете данные в базу данных SQL Azure, действие копирования по умолчанию добавляет данные в таблицу-приемник. Для выполнения команды UPSERT или дополнительной бизнес-логики используйте хранимую процедуру в **SqlSink**. Дополнительные сведения см. в разделе [Вызов хранимой процедуры из приемника SQL](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Пример добавления данных

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Вызов хранимой процедуры во время копирования для выполнения операции Upsert

Дополнительные сведения см. в разделе [Вызов хранимой процедуры из приемника SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Столбцы идентификаторов в целевой базе данных

В этом разделе вы научитесь копировать данные из исходной таблицы без столбца идентификаторов в целевую таблицу со столбцом идентификаторов.

#### <a name="source-table"></a>Исходная таблица

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Целевая таблица

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> В целевой таблице есть столбец идентификаторов.

#### <a name="source-dataset-json-definition"></a>Определение JSON исходного набора данных

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Определение JSON целевого набора данных

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> Ваша исходная и целевая таблицы имеют разные схемы. 

У целевой таблицы есть дополнительный столбец с идентификатором. В этом случае нужно указать свойство **structure** в определении целевого набора данных, которое не включает в себя столбец идентификаторов.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

Для копирования данных в базу данных SQL Azure можно настроить и вызвать указанную пользователем хранимую процедуру с дополнительными параметрами.

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Она обычно используется, когда перед окончательной вставкой исходных данных в целевую таблицу необходимо выполнить вставку и обновление или дополнительную обработку. Некоторые дополнительные примеры обработки: объединение столбцов, просмотр дополнительных значений и вставка в несколько таблиц.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Azure. Предположим, что и входные данные, и таблица **Marketing** приемника состоят из трех столбцов: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

#### <a name="output-dataset"></a>Выходной набор данных

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Определите раздел **SqlSink** в действии копирования:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

В своей базе данных определите хранимую процедуру с тем же именем, что и **SqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа таблицы в хранимой процедуре должно совпадать с именем **tableName**, заданным в наборе данных.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

В своей базе данных определите тип таблицы с тем же именем, что и **sqlWriterTableType**. Схема типа таблицы должна быть той же, что и схема, возвращаемая входными данными.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Сопоставление типов данных для базы данных SQL Azure

При копировании данных в базу данных SQL Azure или из нее используются следующие сопоставления между типами данных базы данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных базы данных SQL Azure | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| bigint |Int64 |
| binary; |Byte[] |
| bit |Логическое |
| char; |String, Char[] |
| дата |Datetime |
| DateTime |Datetime |
| datetime2; |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Атрибут FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| изображение |Byte[] |
| int |Int32 |
| money; |Decimal |
| nchar; |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar; |String, Char[] |
| real; |Single |
| rowversion |Byte[] |
| smalldatetime; |Datetime |
| smallint; |Int16 |
| smallmoney; |Decimal |
| sql_variant |Object * |
| текст |String, Char[] |
| Twitter в режиме реального |Интервал времени |
| timestamp |Byte[] |
| tinyint; |Byte |
| uniqueidentifier |Guid |
| varbinary; |Byte[] |
| varchar. |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
