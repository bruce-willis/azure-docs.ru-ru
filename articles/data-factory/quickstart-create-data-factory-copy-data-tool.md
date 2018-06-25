---
title: Копирование данных с помощью средства копирования данных Azure | Документация Майкрософт
description: Создание фабрики данных Azure и применение средства копирования данных для копирования данных из одного расположения в хранилище BLOB-объектов Azure в другое.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 23d4afffbe108d439dd53d95ed81f7d9fc4246ef
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284580"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Копирование данных с помощью средства копирования данных 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-copy-data-tool.md)

В этом руководстве вы создадите фабрику данных с помощью портала Azure. После этого вы примените средство копирования данных, чтобы создать конвейер для копирования данных из одной папки в другую в хранилище BLOB-объектов Azure. 

> [!NOTE]
> Если вы еще не работали со службой "Фабрика данных Azure", ознакомьтесь с [общими сведениями](data-factory-introduction.md) о ней перед изучением этого руководства. 
>
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы, см. статью [Руководство. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева выберите **Создать**, **Данные+аналитика**, **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
   ![Страница "Новая фабрика данных"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть *глобально уникальным*. При возникновении указанной ниже ошибки измените имя фабрики данных (например, на **&lt;ваше_имя_&gt;ADFTutorialDataFactory**) и попробуйте создать фабрику данных снова. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
  
   ![Ошибка "Имя недоступно"](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. В поле **Подписка** выберите подписку Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий:
     
   - Выберите **Use existing** (Использовать имеющуюся) и выберите имеющуюся группу ресурсов в списке. 
   - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
   Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Для **версии** выберите **V2 (предварительная версия)**.
5. В поле **Расположение** выберите расположение фабрики данных. 

   В списке отображены только поддерживаемые расположения. Хранилища данных (такие как служба хранилища Azure и база данных SQL Azure) и вычислительные среды (например, Azure HDInsight), используемые фабрикой данных, могут находиться в других расположениях и/или регионах.

6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Когда создание завершится, откроется страница **Фабрика данных**. Выберите элемент **Author & Monitor** (Создание и мониторинг), чтобы открыть на отдельной вкладке приложение пользовательского интерфейса службы "Фабрика данных Azure".
   
   ![Домашняя страница фабрики данных с элементом Author & Monitor (Создание и мониторинг)](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Запуск средства копирования данных

1. Чтобы запустить средство копирования данных, на странице **Let's get started** (Начало работы) выберите элемент **Копирование данных**. 

   ![Элемент "Копирование данных"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

2. На странице **Свойства** средства копирования данных можно указать имя и описание конвейера, а затем нажать кнопку **Далее**. 

   ![Страница "Свойства"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. На странице **Исходное хранилище данных** сделайте следующее:

    a. Нажмите кнопку **+Создать подключение**, чтобы добавить подключение.

    ![Страница "Исходное хранилище данных"](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    Б. В коллекции выберите **Хранилище BLOB-объектов Azure**, а затем нажмите кнопку **Далее**.

    ![Выбор хранилища BLOB-объектов из коллекции](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. На странице **Specify the Azure Blob storage account** (Указание учетной записи хранилища BLOB-объектов Azure) выберите учетную запись из списка **Имя учетной записи хранения**, а затем щелкните **Далее**. 

   ![Настройка учетной записи хранилища BLOB-объектов Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Выберите созданную связанную службу в качестве источника, а затем нажмите кнопку **Далее**.

   ![Выбор исходной связанной службы](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


4. На странице **Choose the input file or folder** (Выбор входного файла или папки) выполните следующие действия:

   a. Нажмите кнопку **Обзор**, чтобы перейти к папке **adftutorial/input**, выделите файл **emp.txt** и щелкните **Выбрать**. 

   ![Страница Choose the input file or folder (Выбор входного файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Установите флажок **Двоичное копирование**, чтобы скопировать файл как есть, и нажмите кнопку **Далее**. 

   ![Страница Choose the input file or folder (Выбор входного файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


5. На странице **целевого хранилища данных** выберите созданную связанную службу **Хранилище BLOB-объектов Azure** и нажмите кнопку **Далее**. 

   ![Страница "Целевое хранилище данных"](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

6. На странице **Выбор целевого файла или папки** укажите путь к папке **adftutorial/output** и нажмите кнопку **Далее**. 

   ![Страница Choose the output file or folder (Выбор целевого файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

7. На странице **Параметры** нажмите кнопку **Далее**, чтобы использовать настройки по умолчанию. 

8. Проверьте все параметры на странице **Сводка** и нажмите кнопку **Далее**. 

    ![Страница Summary (Сводка)](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

9. На странице **Deployment complete** (Развертывание завершено) щелкните **Monitor** (Мониторинг), чтобы отслеживать созданный конвейер. 

    ![Страница Deployment complete (Завершение развертывания)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

10. Приложение переключится на вкладку **Мониторинг**. На этой вкладке можно увидеть состояние конвейера. Щелкните **Обновить**, чтобы обновить список. 
    
    ![Мониторинг выполнения конвейера](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

11. Выберите ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Наш конвейер содержит только одно действие типа **Копирование**. 

    ![Мониторинг выполнения действий](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
12. Чтобы просмотреть сведения об операции копирования, щелкните ссылку **Сведения** (изображение очков) в столбце **Действия**. Дополнительные сведения о свойствах см. в [обзоре действия копирования](copy-activity-overview.md).

    ![Сведения об операции копирования](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

13. Убедитесь, что файл **emp.txt** создан в папке **output** в контейнере **adftutorial**. Если указанной папки выходных данных нет, она будет автоматически создана в службе "Фабрика данных". 

14. Перейдите на вкладку **Автор**, расположенную над вкладкой **мониторинга** на панели слева. Там вы сможете изменять связанные службы, наборы данных и конвейеры. Дополнительные сведения об их изменении в пользовательском интерфейсе фабрики данных см. в статье [Создание фабрики данных с помощью пользовательского интерфейса службы "Фабрика данных Azure"](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-portal.md), чтобы узнать об использовании фабрики данных в различных сценариях. 