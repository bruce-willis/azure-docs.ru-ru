---
title: Копирование данных в службу "Хранилище данных SQL Azure" и из нее с помощью службы "Фабрика данных Azure" | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ в хранилище данных SQL Azure или из него в поддерживаемые хранилища-приемники с помощью фабрики данных.
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
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 3c447a37b1dfbdac2c6e2a4eaa61d0e0e08a2176
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442245"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Версия 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Текущая версия](connector-azure-sql-data-warehouse.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в хранилище данных SQL Azure или из него. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из хранилища данных SQL Azure можно скопировать в любое поддерживаемое хранилище данных приемника. Вы можете скопировать данные из любого поддерживаемого исходного хранилища данных в хранилище данных SQL Azure. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель хранилища данных SQL Azure поддерживает такие функции:

- копирование данных с использованием проверки подлинности SQL и проверки подлинности по маркерам приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемым удостоверением службы (MSI);
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- загрузку данных с помощью технологии PolyBase или массовой вставки (в качестве приемника). Для лучшей производительности копирования рекомендуется использовать технологию PolyBase.

> [!IMPORTANT]
> Обратите внимание, что PolyBase поддерживает только проверку подлинности SQL, но не проверку подлинности Azure AD.

> [!IMPORTANT]
> При копировании данных с использованием среды выполнения интеграции фабрики данных Azure настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он разрешал службам Azure получать доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. В этот диапазон входит IP-адрес компьютера, который используется для подключения к базе данных SQL Azure.

## <a name="get-started"></a>Начало работы

> [!TIP]
> Для обеспечения лучшей производительности при загрузке данных в хранилище данных SQL Azure используйте PolyBase. Дополнительные сведения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах для определения сущностей фабрики данных, относящихся к соединителю хранилища данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы хранилища данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureSqlDW**. | Yes |
| connectionString | Укажите сведения, необходимые для подключения к экземпляру хранилища данных SQL Azure, для свойства **connectionString**. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет  |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- Аутентификация по маркеру приложения Azure AD на основе [субъекта-службы](#service-principal-authentication).
- Аутентификация по маркеру приложения Azure AD на основе [управляемого удостоверения службы](#managed-service-identity-authentication).

>[!TIP]
>Если вы получили ошибку с кодом ошибки UserErrorFailedToConnectToSqlServer и сообщение типа "Предел сеанса для базы данных — XXX, и он был достигнут", добавьте `Pooling=false` в строку подключения и повторите попытку.

### <a name="sql-authentication"></a>Аутентификация SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

1. **[Создайте приложение Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)** на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратором Azure AD может быть пользователь Azure AD или группа Azure AD. Если вы предоставили группе с MSI роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

1. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Настройте в ADF связанную службу хранилища данных SQL Azure** в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Фабрика данных может быть связана с [управляемым удостоверением службы](data-factory-service-identity.md), которое представляет это решение. Вы можете использовать это удостоверение службы для проверки подлинности хранилища данных SQL Azure. Назначенная фабрика может получить доступ и скопировать данные из вашего хранилища данных или в него, используя этот идентификатор.

> [!IMPORTANT]
> Обратите внимание, что PolyBase в настоящее время не поддерживает проверку подлинности по MSI.

Чтобы использовать проверку подлинности по маркеру приложения Azure AD на основе управляемого удостоверения службы, выполните следующие действия:

1. **Создайте группу в Azure AD.** Сделайте MSI фабрики членом группы.

    a. Найдите удостоверение службы фабрики данных на портале Azure. Перейдите к **свойствам** фабрики данных. Скопируйте идентификатор удостоверения службы.

    b. Установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Войдите с помощью команды `Connect-AzureAD`. Выполните следующие команды для создания группы и добавления MSI фабрики данных в качестве члена группы.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали).

1. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для группы Azure AD. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Предоставьте группе Azure AD необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Например, запустите следующий код.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. **Настройте в ADF связанную службу хранилища данных SQL Azure** в фабрике данных Azure.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Пример использования проверки подлинности на основе MSI в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services). Этот раздел содержит список свойств, поддерживаемых набором данных хранилища данных SQL Azure.

Чтобы скопировать данные в хранилище данных SQL Azure или из него, задайте для свойства **type** набора данных значение **AzureSqlDWTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** для набора данных должно иметь значение: **AzureSqlDWTable**. | Yes |
| tableName | Имя таблицы или представления в экземпляре хранилища данных SQL Azure, на которое ссылается связанная служба. | Yes |

#### <a name="dataset-properties-example"></a>Пример свойств набора данных

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником хранилища данных SQL Azure.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Хранилище данных SQL Azure в качестве источника

Чтобы скопировать данные из хранилища данных SQL Azure, задайте для свойства **type** источника действия копирования значение **SqlDWSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** источника действия копирования должно иметь значение **SqlDWSource**. | Yes |
| SqlReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. | Нет  |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет  |
| storedProcedureParameters | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет  |

### <a name="points-to-note"></a>Примечания

- Если для **SqlSource** указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных. Кроме того, можно создать хранимую процедуру. Укажите параметр **sqlReaderStoredProcedureName** и **storedProcedureParameters**, если хранимая процедура принимает параметры.
- Если не указать **sqlReaderQuery** или **sqlReaderStoredProcedureName**, то столбцы, определенные в разделе **structure** набора данных JSON, будут использоваться для построения запроса. `select column1, column2 from mytable` выполняется по отношению к хранилищу данных SQL Azure. Если в определении набора данных нет раздела **structure**, выбираются все столбцы из таблицы.
- При использовании **sqlReaderStoredProcedureName** по-прежнему необходимо указать фиктивное свойство **tableName** в наборе данных JSON.

#### <a name="sql-query-example"></a>Пример SQL-запроса

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="azure-sql-data-warehouse-as-sink"></a>Хранилище данных SQL Azure в качестве приемника

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** приемника действия копирования должно иметь значение **SqlDWSink**. | Yes |
| allowPolyBase | Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT. <br/><br/> Мы рекомендуем загружать данные в хранилище данных SQL с использованием PolyBase. Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию).  | Нет  |
| polyBaseSettings | Группа свойств, которые можно задать, если свойство **allowPolybase** имеет значение **true**. | Нет  |
| rejectValue | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе "Аргументы" раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2 и. т. д. |Нет  |
| rejectType | Указывает, является ли параметр **rejectValue** литеральным или процентным.<br/><br/>Допустимые значения: **Значение** (по умолчанию) и **Процент**. | Нет  |
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … | Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault | Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase получает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет  |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения **writeBatchSize**. Применимо, только если не используется PolyBase.<br/><br/>Допустимое значение: **целое число** (количество строк). | Нет. Значение по умолчанию — 10000. |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия. Применимо, только если не используется PolyBase.<br/><br/>Допустимое значение — **timespan**. Пример: 00:30:00 (30 минут). | Нет  |
| preCopyScript | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в хранилище данных SQL Azure при каждом выполнении. Это свойство используется для очистки предварительно загруженных данных. | Нет  | (#repeatability-during-copy). | Инструкция запроса. | Нет  |

#### <a name="sql-data-warehouse-sink-example"></a>Пример приемника хранилища данных SQL

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

В следующем разделе можно узнать, как использовать PolyBase для эффективной загрузки в хранилище данных SQL.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.

Применение [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) — это эффективный способ загрузки большого объема данных в хранилище данных SQL Azure с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Подробное сравнение см. в разделе [Базовые показатели производительности](copy-activity-performance.md#performance-reference). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных менее чем за 15 минут](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Если исходные данные находятся в хранилище BLOB-объектов Azure или в Azure Data Lake Store и их формат соответствует требованиям PolyBase, то копирование можно выполнять напрямую в хранилище данных SQL Azure, используя PolyBase. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-by-using-polybase)**.
* Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-by-using-polybase)**. Промежуточное копирование также обеспечивает лучшую пропускную способность. Оно автоматически преобразует данные в формат, совместимый с PolyBase. И он хранит данные в хранилище BLOB-объектов Azure. После этого данные загружаются в хранилище данных SQL.

> [!IMPORTANT]
> Обратите внимание, что PolyBase в настоящее время не поддерживается для аутентификации по маркеру приложения Azure AD на основе MSI.

### <a name="direct-copy-by-using-polybase"></a>Прямое копирование с помощью PolyBase

PolyBase хранилища данных SQL напрямую поддерживает хранилище BLOB-объектов Azure и Azure Data Lake Store. При этом используется субъект-служба в качестве источника и есть определенные требования к формату файла. Если исходные данные соответствуют критериям, описанным в этом разделе, используйте PolyBase, чтобы выполнять копирование напрямую из исходного хранилища данных в хранилище данных SQL Azure. В противном случае см. сведения в разделе [Промежуточное копирование с помощью PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Дополнительные сведения об эффективном копировании данных из Data Lake Store в хранилище данных SQL см. в [этой записи блога](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. Тип **Связанной службы источника** — хранилище BLOB-объектов Azure (**AzureBLobStorage**/**AzureStorage**) с ключом проверки подлинности учетной записи или хранилище Azure Data Lake 1-го поколения (**AzureDataLakeStore**) с проверкой подлинности субъекта-службы.
1. Тип **входного набора данных** — **AzureBlob** или **AzureDataLakeStoreFile**. Тип формата в свойствах типа `type` — **OrcFormat**, **ParquetFormat** или **TextFormat** со следующими конфигурациями:

   1. Параметр `rowDelimiter` должен иметь значение **\n**.
   1. Параметру `nullValue` задается **пустая строка** ("") или значение по умолчание, а параметру `treatEmptyAsNull` не задается значение false.
   1. Параметру `encodingName` присваивается значение **utf-8**, которое является значением по умолчанию.
   1. `escapeChar`, `quoteChar` и `skipLineCount` не указываются. Поддержка PolyBase пропускает строку заголовка, которую в файле определения приложения можно настроить как `firstRowAsHeader`.
   1. Параметр `compression` может иметь значение **no compression**, **GZip** или **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8",
           "firstRowAsHeader": <any>
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Промежуточное копирование с помощью PolyBase

Если исходные данные не соответствуют критериям предыдущего раздела, включите копирование данных через промежуточный экземпляр хранилища BLOB-объектов Azure. Это не может быть хранилище Azure класса Premium. В этом случае фабрика данных Azure автоматически запускает преобразования данных для соответствия требованиям формата данных PolyBase. Затем она загружает данные в хранилище данных SQL с помощью PolyBase. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Подробные сведения о копировании данных через промежуточный экземпляр хранилища BLOB-объектов Azure см. в разделе [Промежуточное копирование](copy-activity-performance.md#staged-copy).

Чтобы использовать эту функцию, создайте [связанную службу хранения Azure](connector-azure-blob-storage.md#linked-service-properties), которая относится к учетной записи хранения Azure с промежуточным хранилищем BLOB-объектов. Затем укажите свойства `enableStaging` и `stagingSettings` для действия копирования, как показано в следующем коде:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Советы и рекомендации по использованию PolyBase

В следующих разделах приведены практические рекомендации в дополнение к указанным в статье [Рекомендации по использованию хранилища данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, загружающий данные в хранилище, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли **db_owner**. Чтобы узнать, как это сделать, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Ограничение размера строки и типа данных

Загрузки PolyBase ограничены записями размером менее 1 МБ. PolyBase не позволяет загружать данные типа VARCHR(MAX), NVARCHAR(MAX) или VARBINARY(MAX). Дополнительные сведения см. в статье [Ограничения емкости хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если исходные данные имеют записи размером более 1 МБ, можно попробовать вертикально разбить исходные таблицы на несколько небольших. Убедитесь, что максимальный размер каждой записи не превышает предел. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и объединить в хранилище данных SQL Azure.

### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, присвойте более высокий класс ресурсов пользователю, который загружает данные в хранилище данных SQL через PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Использование **tableName** в хранилище данных SQL Azure

В следующей таблице приведены примеры того, как указать свойство **tableName** в наборе данных JSON. В ней показаны несколько сочетаний имен схем и таблиц.

| Схема базы данных | Имя таблицы | Свойство **tableName** в JSON |
| --- | --- | --- |
| dbo | MyTable | MyTable или dbo.MyTable либо [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable или [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] или [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Если вы видите следующую ошибку, это может указывать на неправильное значение свойства **tableName**. Правильные значения для свойства **tableName** в JSON см. в таблице выше.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Столбцы со значениями по умолчанию

Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Примером может служить таблица с четырьмя столбцами, и один из них определен со значением по умолчанию. Входные данные по-прежнему должны иметь четыре столбца. Если входной набор данных будет содержать три столбца, мы получим ошибку, похожую на следующую:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значение NULL, входные данные большого двоичного объекта для этого столбца могут быть пустыми. Но они не могут отсутствовать во входном наборе данных. Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов данных для хранилища данных SQL Azure

При копировании данных в хранилище данных SQL Azure или из него используются следующие сопоставления между типами данных хранилища данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных хранилища данных SQL Azure | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| bigint | Int64 |
| binary; | Byte[] |
| bit | Логическое |
| char; | String, Char[] |
| дата | Datetime |
| DateTime | Datetime |
| datetime2; | Datetime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Атрибут FILESTREAM (varbinary(max)) | Byte[] |
| Float | Double |
| изображение | Byte[] |
| int | Int32 |
| money; | Decimal |
| nchar; | String, Char[] |
| ntext | String, Char[] |
| numeric | Decimal |
| nvarchar; | String, Char[] |
| real; | Single |
| rowversion | Byte[] |
| smalldatetime; | Datetime |
| smallint; | Int16 |
| smallmoney; | Decimal |
| sql_variant | Object * |
| текст | String, Char[] |
| Twitter в режиме реального | Интервал времени |
| timestamp | Byte[] |
| tinyint; | Byte |
| uniqueidentifier | Guid |
| varbinary; | Byte[] |
| varchar. | String, Char[] |
| xml | xml |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
