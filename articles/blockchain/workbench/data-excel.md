---
title: Использование данных Azure Blockchain Workbench в Microsoft Excel
description: Узнайте, как загружать и просматривать данные базы данных SQL Azure Blockchain Workbench в Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 3c257a47c796636d0b86aa6b246b17c0fd39bae3
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242104"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Просмотр данных Azure Blockchain Workbench с помощью Microsoft Excel

Microsoft Excel можно использовать для просмотра данных в базе данных SQL Azure Blockchain Workbench. В этой статье описаны действия, необходимые для:

* Подключения к базе данных Blockchain Workbench из Microsoft Excel.
* Просмотра таблиц данных и представлений базы данных Blockchain Workbench.
* Загрузки данных представления Blockchain Workbench в Excel.

## <a name="connect-to-the-blockchain-workbench-database"></a>Подключение к базе данных Blockchain Workbench

Чтобы выполнить подключение к базе данных Blockchain Workbench, сделайте следующее:

1. Откройте Microsoft Excel.
2. На вкладке **Данные** выберите **Получить данные**.
3. Щелкните **Из Azure**, а затем выберите **Из базы данных SQL Azure**.

   ![Подключение к базе данных SQL Azure](./media/data-excel/connect-sql-db.png)

4. В диалоговом окне **База данных SQL Server** сделайте следующее:

    * В поле **Сервер** введите имя сервера Blockchain Workbench.
    * В поле **База данных (необязательно)** введите имя базы данных.

   ![Указание сервера базы данных и базы данных](./media/data-excel/provide-server-db.png)

5. На панели навигации диалогового окна **База данных SQL Server** выберите **База данных**. Заполните поля **Имя пользователя** и **Пароль**, а затем нажмите кнопку **Подключиться**.

    > [!NOTE]
    > Если вы используете учетные данные, созданные в процессе развертывания Azure Blockchain Workbench, в поле **Имя пользователя** будет задано значение `dbadmin`. **Пароль** — пароль, созданный вами при развертывании Blockchain Workbench.
    
   ![Предоставление учетных данных для доступа к базе данных](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Просмотр таблиц базы данных и представлений

После подключения к базе данных откроется диалоговое окно навигатора Excel. Вы можете использовать окно навигации для просмотра таблиц и представлений в базе данных. Представления предназначены для создания отчетов. Их имена начинаются с префикса **vw**.

   ![Предварительная версия представления навигатора Excel](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Загрузка данных представления в книгу Excel

В следующем примере показано, как загрузить данные из представления в книгу Excel.

1. В полосе прокрутки **Навигатор** выберите представление **vwContractAction**. В предварительной версии **vwContractAction** отображаются все действия, связанные с контрактом в базе данных Blockchain Workbench.
2. Выберите **Загрузить**, чтобы получить все данные в представлении и поместить их в книгу Excel.

   ![Данные, загруженные из представления](./media/data-excel/view-data.png)

После загрузки данных вы можете использовать функции Excel, чтобы создавать собственные отчеты с помощью метаданных и данных транзакций из базы данных Azure Blockchain Workbench.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)