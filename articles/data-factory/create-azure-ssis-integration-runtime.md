---
title: Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как создать среду выполнения интеграции Azure–SSIS в фабрике данных Azure, чтобы запускать и развертывать пакеты Integration Services в Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: a7ba62a28b65d1cd7152c793bc303e747057cdf8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991476"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"
В этой статье представлены шаги по подготовке среды выполнения интеграции Azure SSIS в фабрике данных Azure. Затем можно использовать SQL Server Data Tools (SSDT) ​​или SQL Server Management Studio (SSMS) для запуска и развертывания пакетов служб SQL Server Integration Services (SSIS) в этой среде выполнения в Azure. 

В руководстве [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md) показано, как создать среду выполнения интеграции Azure–SSIS, используя базу данных SQL Azure, чтобы разместить каталог служб SSIS. Эта статья дополняет руководство и содержит следующие инструкции: 

- Необязательное использование базы данных SQL Azure с конечными точками службы виртуальной сети или с управляемым экземпляром для размещения каталога (базы данных) SSISDB. Инструкции по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Сравнение логического сервера базы данных SQL и управляемого экземпляра базы данных SQL](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Предварительно необходимо подключить среду выполнения интеграции Azure-SSIS к виртуальной сети и, при необходимости, настроить ее разрешения и параметры. См. статью [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Необязательное использование проверки подлинности Azure Active Directory (AAD) с помощью Управляемого удостоверения службы "Фабрики данных Azure" (MSI) для среды выполнения интеграции Azure-SSIS для подключения к серверу базы данных. Для последнего варианта необходимо добавить в группу AAD Управляемое удостоверение службы "Фабрика данных Azure" с разрешениями на доступ к серверу базы данных. Эта процедура описана в статье [Включение аутентификации Azure Active Directory в среде выполнения интеграции Azure-SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Обзор
В этой статье показаны различные способы подготовки среды выполнения интеграции Azure SSIS IR: 

- [портала Azure](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Шаблон Azure Resource Manager](#azure-resource-manager-template) 

При создании среды выполнения интеграции Azure SSIS фабрика данных подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS (SSISDB). Сценарий также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure–SSIS. 

При подготовке экземпляра среды выполнения интеграции Azure Integration Services также устанавливается пакет функций Azure для служб SSIS и распространяемый компонент Access. Эти компоненты обеспечивают подключение к файлам Excel и Access и другим источникам данных Azure, кроме тех, которые поддерживаются встроенными компонентами. Кроме того, вы можете установить дополнительные компоненты. Дополнительные сведения см. в статье о [пользовательской установке для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Предварительные требования 
- **Подписка Azure**. Если у вас нет подписки, вы можете [создать бесплатную пробную учетную запись](http://azure.microsoft.com/pricing/free-trial/). 

- **Логический сервер базы данных SQL Azure или управляемый экземпляр**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. На этом сервере размещается база данных каталога служб SSIS (SSISDB). Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure. В зависимости от выбранного сервера базы данных SSISDB может быть создана от вашего имени как отдельная база данных, а также как часть эластичного пула или управляемого экземпляра. Доступ к ней можно получить через общедоступную сеть или после присоединения к виртуальной сети. Список поддерживаемых ценовых категорий базы данных SQL Azure см. в статье [Ограничения ресурсов базы данных SQL Azure](../sql-database/sql-database-resource-limits.md). 

    Удостоверьтесь в том, что логический сервер базы данных SQL Azure или управляемый экземпляр не имеют каталога служб SSIS (базы данных SSIDB). При подготовке среды Azure SSIS IR не поддерживается использование существующего каталога SSIS. 

- **Классическая виртуальная сеть или виртуальная сеть Azure Resource Manager (необязательно)**. Виртуальная сеть Azure нужна, если выполняется хотя бы одно из следующих условий: 
    - Разместите базу данных каталога SSIS в базе данных Azure SQL с конечными точками виртуальной сети или управляемым экземпляром, который находится внутри виртуальной сети. 
    - Вы хотите подключиться к локальным хранилищам данных из пакетов SSIS, работающих в среде выполнения интеграции Azure SSIS. 

- **Azure PowerShell**. Если вы используете PowerShell, чтобы запустить скрипт для подготовки среды выполнения Azure-SSIS, которая запускает пакеты SSIS в облаке, см. статью [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-azurerm-ps) (Установка Azure PowerShell с помощью PowerShellGet). 

### <a name="region-support"></a>Поддержка регионов
Чтобы получить список регионов Azure, в которых в настоящее время доступна Фабрика данных, выберите интересующие вас регионы на следующей странице, а затем разверните раздел **Аналитика**, чтобы найти пункт **Фабрика данных**: [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/).

Чтобы получить список регионов Azure, в которых сейчас доступна среда выполнения интеграции Azure-SSIS, выберите интересующие вас регионы на следующей странице, а затем разверните раздел **Аналитика**, чтобы найти пункт **Среда выполнения интеграции SSIS**: [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/).### Сравнение базы данных SQL и управляемого экземпляра

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Сравнение логического сервера базы данных SQL и управляемого экземпляра базы данных SQL

В следующей таблице сравниваются некоторые функции логического сервера и управляемого экземпляра базы данных SQL, связанные со средой выполнения интеграции Azure-SSIR.

| Функция | Логический сервер базы данных SQL| Управляемый экземпляр базы данных SQL |
|---------|--------------|------------------|
| **Планирование** | Агент SQL Server недоступен.<br/><br/>См. раздел [Schedule a package as part of an Azure Data Factory pipeline](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity) (Планирование пакета в рамках конвейера фабрики данных Azure).| Агент SQL Server доступен. |
| **Аутентификация** | Вы можете создать базу данных, где будет находиться учетная запись пользователя базы данных, которая представляет любого пользователя Azure Active Directory в роли **dbmanager**.<br/><br/>См. раздел [Enable Azure AD on Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database) (Включение Azure AD в базе данных SQL Azure). | Создать базу данных с установленной учетной записью пользователя базы данных, которая представляет любого пользователя Azure Active Directory, отличного от администратора Azure AD невозможно. <br/><br/>См. раздел [Enable Azure AD on Azure SQL Database Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance) (Включение Azure AD в управляемом экземпляре базы данных SQL Azure). |
| **Уровень служб** | При создании среды выполнения интеграции Azure SSIS или базы данных SQL для SSISDB можно выбрать уровень служб. Существует несколько уровней служб. | Создавая среду выполнения интеграции Azure SSIS в управляемом экземпляре, уровень служб для SSISDB выбрать невозможно. Все базы данных одного управляемого экземпляра базы данных SQL используют один и тот же ресурс, который ему выделен. |
| **Виртуальная сеть** | Поддерживаются как Azure Resource Manager, так и классические виртуальные сети. | Поддерживается только виртуальная сеть Azure Resource Manager. Требуется виртуальная сеть.<br/><br/>Если среда выполнения интеграции Azure SSIS присоединяется к виртуальной сети управляемого экземпляра, используйте для них разные подсети. Чтобы присоединить среду выполнения интеграции Azure SSIS к другой виртуальной сети, отличной от сети управляемого экземпляра, используйте пиринг между виртуальными сетями (в том же регионе) или подключение типа "виртуальная сеть — виртуальная сеть". См. статью [Подключение приложения к управляемому экземпляру базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Распределенные транзакции** | Транзакции координатора распределенных транзакций (MSDTC) не поддерживаются. Если для того, чтобы координировать распределенные транзакции, пакеты используют координатор распределенных транзакций, то временно можно использовать эластичные транзакции для базы данных SQL. Сейчас в SSIS нет встроенной поддержки эластичных транзакций. Чтобы использовать эластичные транзакции в пакете SSIS, необходимо в задаче "скрипт" написать пользовательский код ADO.NET. Эта задача "скрипт" должна включать начало и конец транзакции и все действия, которые должны выполняться в ходе транзакции.<br/><br/>Дополнительные сведения о кодировке эластичных транзакций см. в разделе [Эластичные транзакции с помощью базы данных SQL Azure](https://azure.microsoft.com/en-us/blog/elastic-database-transactions-with-azure-sql-database/). Дополнительные сведения об эластичных транзакциях в целом см. в разделе [Распределенные транзакции по облачным базам данных](../sql-database/sql-database-elastic-transactions-overview.md). | Не поддерживается. |
| | | |

## <a name="azure-portal"></a>Портал Azure
В этом разделе для создания среды выполнения интеграции Azure SSIS используется портал Azure, в частности пользовательский интерфейс фабрики данных. 

### <a name="create-a-data-factory"></a>Создание фабрики данных 
1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных. 
1. Войдите на [портал Azure](https://portal.azure.com/). 
1. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 

   ![Создать -> Фабрика данных](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. На странице **Новая фабрика данных** введите **MyAzureSsisDataFactory** в поле **Имя**. 

   ![Страница "Новая фабрика данных"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы получите указанную ниже ошибку, введите другое имя фабрики данных (например, ваше_имя_MyAzureSsisDataFactory) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных". 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
1. Для **группы ресурсов** выполните одно из следующих действий. 

   - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
   - Выберите **Создать новую**и укажите имя группы ресурсов. 

   Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md). 

1. Укажите **V2** при выборе **версии**. 
1. Укажите **расположение** фабрики данных. В списке отображаются только те расположения, в которых можно создать фабрики данных. 
1. Кроме того, установите флажок **Закрепить на панели мониторинга**. 
1. Нажмите кнопку **Создать**. 
1. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже. 

    ![Домашняя страница фабрики данных](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Щелкните **Создание и мониторинг**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS 
1. На странице начала работы щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS). 

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. На странице **General Settings** (Общие параметры) для **настройки среды выполнения интеграции** выполните следующие действия: 

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. В поле **Имя** введите имя среды выполнения интеграции. 

    b. В поле **Описание** введите описание среды выполнения интеграции. 

    c. В поле **Расположение** введите расположение среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения. Для узла SSISDB сервера базы данных рекомендуется выбирать одинаковые расположения. 

    d. В раскрывающемся списке **Размер узла** выберите размер узла кластера среды выполнения интеграции. Отображаются только поддерживаемые размеры узла. Если планируется использовать большое количество компьютеров или памяти ресурсоемких пакетов, необходимо выбрать большой размер узла (увеличение масштаба). 

    д. В поле **Номер узла** введите количество узлов в кластере среды выполнения интеграции. Отображается только поддерживаемое число узлов. Если в параллельном режиме требуется запустить несколько пакетов, необходимо выбрать больной кластер с большим числом узлов (с поддержкой горизонтального масштабирования). 

    Е. В раскрывающемся списке **Edition/License** (Выпуск/лицензия) выберите выпуск SQL Server или лицензию SQL Server среды выполнения интеграции для SQL Server Standard или Enterprise. Если в среде выполнения интеграции необходимо использовать дополнительные функции или функции, которые доступны в версии "Премиум", выберите SQL Server Enterprise. 

    ж. В поле **Save Money** (Экономия средств) для параметра "Преимущество гибридного использования Azure (AHB)" выберите "Да" или "Нет". Если для получения преимуществ и экономии затрат необходимо использовать собственную лицензию SQL Server вместе с программой Software Assurance, выберите "Да". 

    h. Щелкните **Далее**. 

1. На странице **SQL Settings** (Параметры SQL) сделайте следующее: 

   ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. В раскрывающемся списке **Подписка** выберите подписку Azure, которая соответствует серверу базы данных узла SSISDB. 

    b. В раскрывающемся списке **Расположение** выберите расположение сервера базы данных узла SSISDB. Для среды выполнения интеграции рекомендуется выбирать одинаковые расположения. 

    c. В раскрывающемся списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога) выберите конечную точку сервера базы данных узла SSISDB. В зависимости от выбранного сервера базы данных SSISDB может быть создана от вашего имени как отдельная база данных, а также как часть эластичного пула или управляемого экземпляра. Доступ к ней можно получить через общедоступную сеть или после присоединения к виртуальной сети. 

    d. Установите флажок в поле **Use AAD authentication...** (Использование проверки подлинности AAD…) и выберите метод проверки подлинности базы данных узла SSISDB: SQL или Azure Active Directory (AAD) с помощью Управляемого удостоверения службы "Фабрика данных Azure". Если вы установите его, вам потребуется добавить в группу AAD Управляемое удостоверение службы "Фабрика данных Azure" с разрешениями на доступ к серверу базы данных. Эта процедура описана в статье [Включение аутентификации Azure Active Directory в среде выполнения интеграции Azure-SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    д. В поле **Имя администратора** введите имя входа SQL для проверки подлинности базы данных сервера узла SSISDB. 

    Е. В поле **Пароль администратора** введите пароль входа SQL для проверки подлинности базы данных сервера узла SSISDB. 

    ж. В раскрывающемся списке **Уровень службы базы данных каталога** выберите уровень службы базы данных сервера узла SSISDB. Уровни делятся на "Базовый", "Стандартный", "Премиум" или "Эластичный пул". 

    h. Нажмите копку **Проверить подключение**, и если проверка прошла успешно, щелкните **Далее**. 

1.  На странице **Дополнительные параметры** сделайте следующее: 

    ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. В раскрывающемся списке **Maximum Parallel Executions Per Node** (Максимальное количество параллельных выполнений на каждом узле) выберите максимальное количество пакетов, которые будут выполняться одновременно на один узел кластера интегрированной среды выполнения. Отображается только поддерживаемое число пакетов. Если возникла необходимость использовать больше чем одно ядро для запуска одного большого пакета или пакета, обработка которого отнимает много вычислительных ресурсов или памяти, выберите меньшее число. Если возникла необходимость запуска одного или нескольких небольших пакетов, которые не потребляют много ресурсов при запуске одним ядром, выберите число побольше. 

    b. Дополнительные сведения о необязательном для заполнения поле **Custom Setup Container SAS URI** (Пользовательская настройка контейнера URI SAS), в которое можно ввести подписанный URL-адрес универсального кода ресурса (URI) контейнера Azure Storage Blob, в котором хранятся связанные с ним файлы, можно узнать из статьи [Пользовательская установка для среды выполнения интеграции Azure MSSQL Integration Services](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. После установки флажка в поле **Select a virtual network...** (Выбрать виртуальную сеть…) необходимо определиться, будет ли среда выполнения интеграции присоединена к виртуальной сети. Установите его, если база данных SQL Azure используется с конечными точками службы виртуальной сети или управляемым экземпляром для размещения SSISDB или требования доступа к локальным данным. Если у вас есть локальные источники или целевые расположения данных в пакетах SSIS, см. статью [Присоединение среды выполнения интеграции Azure-SSIS к виртуальной сети](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Если вы установили его, выполните следующие действия: 

   ![Дополнительные параметры с использованием виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. В поле **Подписка** выберите подписку Azure, в которой расположена виртуальная сеть. 

    b. В поле **Расположение** введите то же расположение среды выполнения интеграции. 

    c. В поле **Тип** выберите тип виртуальной сети (классическая или Azure Resource Manager). Рекомендуется выбрать виртуальную сеть Azure Resource Manager, так как поддержка классических виртуальных сетей скоро будет прекращена. 

    d. В поле **Имя виртуальной сети** выберите имя вашей виртуальной сети. Это должна быть виртуальная сеть, используемая для базы данных Azure SQL с конечными точками виртуальной сети или управляемым экземпляром для размещения SSISDB, или виртуальная сеть, подключенная к вашей локальной сети. 

    д. В поле **Имя подсети** выберите имя подсети в виртуальной сети. Это должна быть подсеть, отличная от той, которая используется управляемым экземпляром базы данных SQL Azure для размещения SSISDB. 

1. Щелкните **VNet Validation** (Проверка виртуальной сети) и в случае успеха нажмите кнопку**Готово**, чтобы начать создание вашей среды выполнения интеграции Azure-SSIS. 

    > [!IMPORTANT]
    > - Эта процедура занимает около 20–30 минут
    > - Служба фабрики данных подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS. Сценарий также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure–SSIS. 

1. В окне **Connections** (Подключения) перейдите на **среду выполнения интеграции**. Чтобы обновить состояние, щелкните **Refresh** (Обновить). 

   ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. С помощью ссылок в столбце **Actions** (Действия) вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена. 

   ![Среда выполнения интеграции Azure SSIS — действия](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале
1. В пользовательском интерфейсе фабрики данных Azure перейдите на вкладку **Изменить**, щелкните **Подключения**, а затем откройте вкладку **Integration Runtimes** (Среды выполнения интеграции), чтобы просмотреть все существующие в фабрике данных среды выполнения интеграции. 

   ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Щелкните **New** (Создать), чтобы создать новую среду выполнения интеграции Azure SSIS. 

   ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Чтобы создать среду выполнения интеграции Azure SSIS, щелкните действие **New** (Создать), как показано на рисунке. 
1. В окне настройки среды выполнения интеграции выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос существующих пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Next** (Далее). 

   ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Остальные шаги описаны в разделе [Подготовка среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime). 

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе для создания среды выполнения интеграции Azure SSIS используется Azure PowerShell.

### <a name="create-variables"></a>Создание переменных
Определите переменные для использования в скрипте этого руководства:

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Вход и выбор подписки
Добавьте следующий код в скрипт для выполнения входа и выбора подписки Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Проверьте подключение к базе данных
Добавьте приведенный ниже скрипт для проверки конечной точки сервера службы "База данных SQL Azure". 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### <a name="configure-virtual-network"></a>Настройка виртуальной сети
Добавьте следующий скрипт, чтобы автоматически настроить разрешения или параметры виртуальной сети вашей среды интеграции Azure SSIS.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Создание фабрики данных
Чтобы создать фабрику данных, выполните следующую команду.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции
Выполните следующую команду для создания среды выполнения интеграции Azure SSIS, запускающей пакеты служб SSIS в Azure: 

Если для размещения SSISDB или требований доступа к локальным данным не используется база данных SQL Azure с конечными точками службы виртуальной сети или управляемым экземпляром, вы можете опустить параметры VNetId и подсети или передать для них пустые значения. В противном случае их нельзя опускать и нужно передать действительные значения из конфигурации вашей виртуальной сети. См. статью [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

При использовании управляемого экземпляра для размещения SSISDB можно опустить параметр CatalogPricingTier или передать для него пустое значение. В противном случае его нельзя опускать и нужно передать действительное значение из списка поддерживаемых ценовых категорий для службы "База данных SQL Azure". Дополнительные сведения см. в статье [Overview Azure SQL Database resource limits](../sql-database/sql-database-resource-limits.md) (Общие сведения об ограничении ресурсов службы "База данных SQL Azure"). 

Если вы используете проверку подлинности Azure Active Directory (AAD) со своим Управляемым удостоверением службы "Фабрика данных Azure" (MSI) для подключения к серверу базы данных, вы можете опустить параметр CatalogAdminCredential, но следует добавить Управляемое удостоверение службы фабрики в группу AAD с разрешениями доступа к серверу базы данных. Дополнительные сведения см. в статье [Включение аутентификации Azure Active Directory в среде выполнения интеграции Azure-SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). В противном случае этот параметр опустить невозможно и для проверки подлинности SQL возникает необходимость передать действительный объект, который сформирован из имени пользователя и пароля администратора сервера.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Запуск среды выполнения интеграции
Выполните следующую команду для запуска среды выполнения интеграции SSIS Azure: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

Выполнение этой команды занимает от **20 до 30 минут**. 

### <a name="full-script"></a>Полный сценарий
Ниже приведен полный скрипт, который создает среду выполнения интеграции Azure-SSIS. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager
В этом разделе для создания среды выполнения интеграции Azure-SSIS используется шаблон Azure Resource Manager. Ниже приведен пример пошагового руководства. 

1. Создайте файл JSON со следующим шаблоном Resource Manager Azure. Замените значения в угловых скобках (заполнители) на собственные. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
    
1. Чтобы развернуть шаблон Azure Resource Manager, запустите команду New-AzureRmResourceGroupDeployment, как показано в следующем примере, где ADFTutorialResourceGroup — это имя вашей группы ресурсов, а ADFTutorialARM.json — файл, который содержит определение JSON для фабрики данных и среды выполнения интеграции Azure-SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    С помощью этой команды создается фабрика данных, а в ней — среда выполнения интеграции Azure SSIS, но текущая команда не запускает среду выполнения интеграции. 

1. Чтобы запустить среду выполнения интеграции Azure SSIS, выполните команду Start-AzureRmDataFactoryV2IntegrationRuntime. 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.
Теперь используйте SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS в Azure. Подключитесь к серверу базы данных, на котором размещен каталог служб SSIS (SSISDB). Имя сервера базы данных имеет формат: &lt;имя сервера базы данных SQL Azure&gt;.database.windows.net или &lt;имя управляемого экземпляра.префикс DNS&gt;.database.windows.net. Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения об Azure SSIS IR в этой документации: 

- [Среда выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). В этой статье содержатся общие сведения о средах выполнения интеграции в целом, включая Azure SSIS IR. 
- [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-create-azure-ssis-runtime-portal.md). Эта статья содержит пошаговые инструкции для создания Azure SSIS IR и использует базу данных SQL Azure для размещения каталога SSIS. 
- [Мониторинг среды выполнения интеграции в фабрике данных Azure](monitor-integration-runtime.md#azure-ssis-integration-runtime). В этом статье показано, как извлечь сведения о среде выполнения интеграции Azure SSIS и описания состояний из возвращаемых данных. 
- [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md). В этой статье показано, как остановить, запустить или удалить Azure SSIS IR. В ней также показано, как развернуть Azure SSIS IR путем добавления дополнительных узлов в среду выполнения интеграции. 
- [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md) В этой статье содержатся общие сведения о присоединении среды выполнения интеграции Azure-SSIS к виртуальной сети Azure. В ней также показано, как настроить виртуальную сеть, чтобы присоединить среду выполнения интеграции Azure–SSIS к виртуальной сети с помощью портала Azure. 
