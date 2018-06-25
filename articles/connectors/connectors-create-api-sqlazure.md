---
title: Подключение к SQL Server или Базе данных SQL Azure из Azure Logic Apps | Документация Майкрософт
description: Как получать доступ к базам данных SQL и управлять ими в локальной среде или в облаке путем автоматизации рабочих процессов с помощью Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296294"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Подключение к SQL Server или Базе данных SQL Azure из Azure Logic Apps

В этой статье описано, как получить доступ к данным в базе данных SQL из приложения логики с помощью соединителя SQL Server. Так вы можете автоматизировать задачи, процессы и рабочие процессы, которые управляют данными и ресурсами SQL, путем создания приложений логики. Соединитель можно использовать как для [SQL Server на локальном компьютере](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation), так и для [Базы данных SQL Azure в облаке](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Вы можете создавать приложения логики, которые активируются событиями в базе данных SQL или в других системах, таких как Dynamics CRM Online. Приложения логики могут получать, вставлять или удалять данные, а также выполнять хранимые процедуры и запросы SQL. Например, вы можете создать приложение логики, которое автоматически проверяет наличие новых записей в Dynamics CRM Online, добавляет элементы в Базу данных SQL для новых записей, а затем отправляет оповещения по электронной почте.

Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. Если вы не работали с приложениями логики, см. руководства по [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и [созданию первого приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). См. технические сведения о <a href="https://docs.microsoft.com/connectors/sql/" target="blank">соединителях SQL Server</a>.

## <a name="prerequisites"></a>предварительным требованиям

* Приложения логики с возможностью доступа к базе данных SQL. Чтобы запустить приложение логики с помощью триггера SQL, требуется [пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* [База данных SQL Server](../sql-database/sql-database-get-started-portal.md) или [База данных SQL Azure](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database). 

  Чтобы при вызове операций приложение логики могло вернуть результаты, таблицы должны содержать данные. Если вы создаете Базу данных SQL Azure, вы также можете использовать предоставляемые примеры баз данных. 

* Имя сервера SQL, имя базы данных, а также имя пользователя и пароль. Эти учетные данные используются для авторизации приложения логики и получения доступа к серверу SQL. 

  * Для Базы данных Azure SQL эти сведения можно найти в строке подключения или на портале Azure в свойствах Базы данных SQL:

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * Для Базы данных SQL Server эти сведения можно найти в строке подключения: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Прежде чем подключать приложение логики к таким локальным системам, как SQL Server, нужно [настроить локальный шлюз данных](../logic-apps/logic-apps-gateway-install.md). Шлюз можно выбрать при создании SQL-подключения для приложения логики.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Добавление триггера SQL

В Azure Logic Apps каждое приложение логики должно запускаться по [триггеру](../logic-apps/logic-apps-overview.md#logic-app-concepts), который активируется, когда происходит определенное событие или если выполняются заданные условия. При каждом срабатывании триггера обработчик Logic Apps создает экземпляр приложения логики, запускающий рабочий процесс.

1. На портале Azure или в Visual Studio создайте пустое приложение логики. Откроется конструктор Logic Apps. В этом примере используется портал Azure.

2. В поле поиска введите "sql server" в качестве условия фильтра. В списке триггеров выберите нужный триггер SQL. 

   Например, выберите этот триггер: **SQL Server — при создании элемента**.

   ![Выбор триггера "SQL Server — при создании элемента"](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Если потребуется ввести сведения о подключении, [создайте подключение к SQL](#create-connection). 
   Или, если подключение уже существует, выберите в списке **имя таблицы**.

   ![Выбор таблицы](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Задайте **интервал** и **частоту**, с которыми приложение логики будет проверять таблицу.

   В этом примере проверяется только выбранная таблица. 
   Чтобы сделать что-то более интересное, добавьте действия, которые выполняют нужные задачи. 
   
   Например, для просмотра нового элемента в таблице, можно добавить другие действия, включая создание файла, который содержит поля из таблицы, и отправку оповещений по электронной почте. 
   См. дополнительные сведения о [других действиях для разных соединителей Logic Apps](../connectors/apis-list.md).

5. Завершив работу, нажмите кнопку **Сохранить** на панели инструментов конструктора. 

   Этот шаг автоматически включает и публикует приложение логики в Azure в реальном времени. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Добавление действия SQL

В Azure Logic Apps [действие](../logic-apps/logic-apps-overview.md#logic-app-concepts) — это шаг в рамках рабочего процесса, выполняемый после срабатывания триггера или другого действия. В этом примере приложение логики запускается при срабатывании [триггера повторения](../connectors/connectors-native-recurrence.md). Оно вызывает действие, которое получает строку из базы данных SQL.

1. На портале Azure или в Visual Studio откройте приложение логики в конструкторе Logic Apps. В этом примере используется портал Azure.

2. В конструкторе Logic Apps выберите для триггера или действия пункты **Новый шаг** > **Добавить действие**.

   ![Выбор пунктов "Новый шаг" > "Добавить действие"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Чтобы добавить действие между существующими шагами, наведите указатель мыши на соединяющую стрелку. 
   Щелкните появившийся знак "плюс" (**+**), а затем выберите **Добавить действие**.

2. В поле поиска введите "sql server" в качестве условия фильтра. В списке действий выберите любое необходимое действие SQL. 

   Например, выберите действие, которое возвращает одну запись: **SQL Server — получение строки**.

   ![Ввод "sql server" и выбор "SQL Server — получение строки"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Если потребуется ввести сведения о подключении, [создайте подключение к SQL](#create-connection). 
   Или, если подключение существует, выберите **имя таблицы** и введите **идентификатор строки** для нужной записи.

   ![Ввод имени таблицы и идентификатора строки](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   В этом примере возвращается только одна строка из выбранной таблицы. 
   Чтобы просмотреть данные в этой строке, может добавить другие действия, которые создают файл с полями из строки для последующего просмотра и которые сохранят этот файл в учетной записи облачного хранилища. См. дополнительные сведения о [других действиях для разных соединителей Logic Apps](../connectors/apis-list.md).

4. Завершив работу, нажмите кнопку **Сохранить** на панели инструментов конструктора. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Подключение к базе данных

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Обработка данных в пакетном режиме

Иногда требуется управлять размером и структурой результирующих наборов или приходится работать с результирующими наборами, которые настолько велики, что соединитель не может вернуть все результаты в рамках одного действия. В таких случаях можно использовать *разбиение на страницы*, которое позволяет обрабатывать результаты в виде небольших наборов. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Создание хранимой процедуры

При получении или вставке нескольких строк приложение логики может выполнять итерацию этих элементов с помощью [*цикла until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) в пределах этих [ограничений](../logic-apps/logic-apps-limits-and-config.md). Но иногда приложение логики должно работать с настолько большими наборами данных, например с тысячами или миллионами строк, что целесообразно сократить расходы на вызовы к базе данных. 

Вместо этого можно создать <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank">*хранимую процедуру*</a>, которая выполняется в экземпляре SQL и использует инструкцию **SELECT - ORDER BY** для упорядочивания результатов должным образом. Это решение позволяет управлять размером и структурой полученных результатов. Приложение логики вызывает хранимую процедуру с помощью действия **выполнения хранимой процедуры** соединителя SQL Server. 

См. дополнительные сведения:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">Разбиение на страницы в SQL для массовой передачи данных с помощью Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Предложение SELECT - ORDER BY</a>

## <a name="connector-specific-details"></a>Сведения о соединителях

См. дополнительные технические сведения о [триггерах, действиях и ограничениях этого соединителя](/connectors/sql/). 

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).

