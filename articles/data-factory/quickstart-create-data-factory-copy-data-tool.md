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
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: d314c04a40155fccc99660bacdb9f646ce77b22f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107594"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Копирование данных с помощью средства копирования данных 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Версия 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Текущая версия](quickstart-create-data-factory-copy-data-tool.md)

В этом руководстве вы создадите фабрику данных с помощью портала Azure. После этого вы примените средство копирования данных, чтобы создать конвейер для копирования данных из одной папки в другую в хранилище BLOB-объектов Azure. 

> [!NOTE]
> Если вы еще не работали со службой "Фабрика данных Azure", ознакомьтесь с [общими сведениями](data-factory-introduction.md) о ней перед изучением этого руководства. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева выберите **Создать**, **Данные+аналитика**, **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
1. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
   ![Страница "Новая фабрика данных"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть *глобально уникальным*. При возникновении указанной ниже ошибки измените имя фабрики данных (например, на **&lt;ваше_имя_&gt;ADFTutorialDataFactory**) и попробуйте создать фабрику данных снова. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
  
   ![Ошибка "Имя недоступно"](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
1. В поле **Подписка** выберите подписку Azure, в рамках которой вы хотите создать фабрику данных. 
1. Для **группы ресурсов** выполните одно из следующих действий:
     
   - Выберите **Use existing** (Использовать имеющуюся) и выберите имеющуюся группу ресурсов в списке. 
   - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
   Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
1. Укажите **V2** при выборе **версии**.
1. В поле **Расположение** выберите расположение фабрики данных. 

   В списке отображены только поддерживаемые расположения. Хранилища данных (такие как служба хранилища Azure и база данных SQL Azure) и вычислительные среды (например, Azure HDInsight), используемые фабрикой данных, могут находиться в других расположениях и/или регионах.

1. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
1. Нажмите кнопку **Создать**.
1. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
1. Когда создание завершится, откроется страница **Фабрика данных**. Выберите элемент **Author & Monitor** (Создание и мониторинг), чтобы открыть на отдельной вкладке приложение пользовательского интерфейса службы "Фабрика данных Azure".
   
   ![Домашняя страница фабрики данных с элементом Author & Monitor (Создание и мониторинг)](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Запуск средства копирования данных

1. Чтобы запустить средство копирования данных, на странице **Let's get started** (Начало работы) выберите элемент **Копирование данных**. 

   ![Элемент "Копирование данных"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

1. На странице **Свойства** средства копирования данных можно указать имя и описание конвейера, а затем нажать кнопку **Далее**. 

   ![Страница "Свойства"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. На странице **Исходное хранилище данных** сделайте следующее:

    a. Нажмите кнопку **+Создать подключение**, чтобы добавить подключение.

    ![Страница "Исходное хранилище данных"](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. В коллекции выберите **Хранилище BLOB-объектов Azure**, а затем нажмите кнопку **Далее**.

    ![Выбор хранилища BLOB-объектов из коллекции](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. На странице **Specify the Azure Blob storage account** (Указание учетной записи хранилища BLOB-объектов Azure) выберите учетную запись из списка **Имя учетной записи хранения**, а затем щелкните **Готово**. 

   ![Настройка учетной записи хранилища BLOB-объектов Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Выберите созданную связанную службу в качестве источника, а затем нажмите кнопку **Далее**.

   ![Выбор исходной связанной службы](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


1. На странице **Choose the input file or folder** (Выбор входного файла или папки) выполните следующие действия:

   a. Нажмите кнопку **Обзор**, чтобы перейти к папке **adftutorial/input**, выделите файл **emp.txt** и щелкните **Выбрать**. 

   ![Страница Choose the input file or folder (Выбор входного файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Установите флажок **Двоичное копирование**, чтобы скопировать файл как есть, и нажмите кнопку **Далее**. 

   ![Страница Choose the input file or folder (Выбор входного файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. На странице **целевого хранилища данных** выберите созданную связанную службу **Хранилище BLOB-объектов Azure** и нажмите кнопку **Далее**. 

   ![Страница "Целевое хранилище данных"](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

1. На странице **Выбор целевого файла или папки** укажите путь к папке **adftutorial/output** и нажмите кнопку **Далее**. 

   ![Страница Choose the output file or folder (Выбор целевого файла или папки)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. На странице **Параметры** нажмите кнопку **Далее**, чтобы использовать настройки по умолчанию. 

1. Проверьте все параметры на странице **Сводка** и нажмите кнопку **Далее**. 

    ![Страница Summary (Сводка)](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

1. На странице **Deployment complete** (Развертывание завершено) щелкните **Monitor** (Мониторинг), чтобы отслеживать созданный конвейер. 

    ![Страница Deployment complete (Завершение развертывания)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Приложение переключится на вкладку **Мониторинг**. На этой вкладке можно увидеть состояние конвейера. Щелкните **Обновить**, чтобы обновить список. 
    
    ![Мониторинг выполнения конвейера](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

1. Выберите ссылку **View Activity Runs** (Просмотр запусков действий) в столбце **Действия**. Наш конвейер содержит только одно действие типа **Копирование**. 

    ![Мониторинг выполнения действий](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
1. Чтобы просмотреть сведения об операции копирования, щелкните ссылку **Сведения** (изображение очков) в столбце **Действия**. Дополнительные сведения о свойствах см. в [обзоре действия копирования](copy-activity-overview.md).

    ![Сведения об операции копирования](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

1. Убедитесь, что файл **emp.txt** создан в папке **output** в контейнере **adftutorial**. Если указанной папки выходных данных нет, она будет автоматически создана в службе "Фабрика данных". 

1. Перейдите на вкладку **Автор**, расположенную над вкладкой **мониторинга** на панели слева. Там вы сможете изменять связанные службы, наборы данных и конвейеры. Дополнительные сведения об их изменении в пользовательском интерфейсе фабрики данных см. в статье [Создание фабрики данных с помощью пользовательского интерфейса службы "Фабрика данных Azure"](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-portal.md), чтобы узнать об использовании фабрики данных в различных сценариях. 