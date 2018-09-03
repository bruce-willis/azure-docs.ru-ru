---
title: Перенос в Управляемый экземпляр Базы данных SQL Azure с помощью DMS | Документация Майкрософт
description: Узнайте, как выполнять миграцию из локального экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: jtoland
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: dbf71b1fcc15743f4670c4072921f1a167a90e97
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887308"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS в автономном режиме
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance.md). Сведения о дополнительных методах, которые могут потребовать некоторых действий вручную, см. в статье [Перенос экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Проекты миграции из SQL Server в Управляемый экземпляр базы данных SQL Azure находятся в стадии предварительной версии, и на них распространяются [Дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве выполняется миграция базы данных **Adventureworks2012** из локального экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.
> * скачивание отчета о миграции.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется следующее:

- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Изучите [сетевые топологии для миграции Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure (VNET) не блокируют порты 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру исходной СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и файлам SMB через порт 445.
- Создайте Управляемый экземпляр Базы данных SQL Azure на портале Azure, следуя инструкциям из [этой статьи](https://aka.ms/sqldbmi).
- Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
- Создайте общую сетевую папку, которую Azure Database Migration Service сможет использовать для резервного копирования базы данных-источника.
- Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись в эту созданную сетевую папку, а учетной записи компьютера для исходного сервера — права на чтение и запись для этой папки.
- Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
- Создайте контейнер больших двоичных объектов и получите его URI SAS, следуя инструкциям, приведенным в разделе [Получение SAS для контейнера больших двоичных объектов](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), и не забудьте установить все разрешения (чтение, запись, удаление, перечисление) в окне политик при создании URI SAS. Это позволяет Azure Database Migration Service с помощью контейнера учетной записи хранения выполнять отправку резервных копий, которые используются для переноса баз данных в Управляемый экземпляр Базы данных SQL Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите + **Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4.  Выберите расположение, в котором хотите создать экземпляр DMS.

5. Выберите существующую виртуальную сеть или создайте новую.
 
    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и конечному Управляемому экземпляру базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).
   
    ![Создание службы DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service2.png)

7.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания экземпляра службы найдите его на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра и выберите его.
 
3. Выберите **+ Новый проект миграции**.

4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, в текстовом поле **Target server type** (Тип целевого сервера) выберите **Управляемый экземпляр Базы данных SQL Azure**, а затем в разделе **Выберите тип действия** нажмите кнопку **Автономная миграция данных**.

   ![Создание проекта DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project2.png)

5. Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Migration source detail** (Сведения об источнике миграции) задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Щелкните **Сохранить**.

4. На экране **Выбор баз данных-источников** выберите базу данных **Adventureworks2012** для миграции.

   ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1.  На экране **Migration target details** (Сведения о целевом объекте миграции) задайте сведения о подключении для целевого объекта — предварительно подготовленного Управляемого экземпляра Базы данных SQL Azure, в который нужно перенести базу данных **AdventureWorks2012**.

    Если Управляемый экземпляр базы данных SQL Azure еще не подготовлен, выберите **Нет** и перейдите по ссылке, чтобы подготовить его. Вы можете продолжить создание проекта, а затем, когда Управляемый экземпляр базы данных SQL Azure будет готов, вернуться к этому проекту, чтобы выполнить миграцию.   
 
       ![Выбор цели](media\tutorial-sql-server-to-managed-instance\dms-target-details2.png)

2.  Щелкните **Сохранить**.

## <a name="select-source-databases"></a>Выбор баз данных-источников

1. На экране **Выбор баз данных-источников** выберите базу данных-источник для миграции.

    ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\select-source-databases.png)

2. Щелкните **Сохранить**.

## <a name="select-logins"></a>Выберите имена для входа
 
1. Выберите имена для входа, которые хотите перенести, на экране **Select logins** (Выбор имени входа).

    >[!NOTE]
    >В этом выпуске поддерживается только перенос имен для входа SQL.

    ![Выберите имена для входа](media\tutorial-sql-server-to-managed-instance\select-logins.png)

2. Щелкните **Сохранить**.
 
## <a name="configure-migration-settings"></a>Настройка параметров миграции
 
1. На экране **Configure migration settings** (Настройка параметров миграции) укажите следующие сведения:

    | | |
    |--------|---------|
    |**Выбор исходного архива** | Выберите вариант **I will provide latest backup files** (Я предоставлю последние файлы резервных копий) при наличии файлов полных резервных копий, доступных для DMS для переноса базы данных. Выберите вариант **I will let Azure Database Migration Service create backup files** (Я разрешу Azure Database Migration Service создавать файлы резервных копий), если нужно, чтобы служба DMS сначала создала полную резервную копию базы данных-источника и использовала ее для миграции. |
    |**Общая сетевая папка** | Локальная сетевая папка SMB, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника. Учетная запись службы, в которой выполняется исходный экземпляр SQL Server, должна иметь права записи для этой сетевой папки. Укажите полное доменное имя или IP-адрес сервера и сетевую папку, например "\\\servername.domainname.com\backupfolder" или "\\\IP address\backupfolder".|
    |**Имя пользователя** | Убедитесь, что пользователь Windows имеет полное право доступа к указанной ранее сетевой папке. Служба Azure Database Migration Service будет олицетворять пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления. В случае выбора для миграции баз данных с поддержкой TDE указанный ранее пользователь Windows должен иметь встроенную учетную запись администратора, а режим [Контроль учетных записей](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) должен быть отключен для Azure Database Migration Service, чтобы отправлять и удалять файлы сертификатов. |
    |**Пароль** | Пароль для пользователя |
    |**Параметры учетной записи хранения** | Код URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий, используемых для переноса базы данных в Управляемый экземпляр базы данных SQL Azure. [Получение SAS для контейнера больших двоичных объектов](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Параметры TDE** | Необходимо иметь права записи для целевого Управляемого экземпляра Базы данных Azure SQL, чтобы выполнить миграцию баз данных-источников с включенным прозрачным шифрованием данных (TDE).  В раскрывающемся меню выберите подписку, в которой подготовлен Управляемый экземпляр Базы данных SQL Azure.  В раскрывающемся меню выберите целевой Управляемый экземпляр Базы данных SQL Azure. |
    
    ![Настройка параметров миграции](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings3.png)

2. Щелкните **Сохранить**.
 
## <a name="review-the-migration-summary"></a>Просмотр сводки по миграции

1. На экране **Сводка по миграции** в текстовом поле **Имя активности** задайте имя действия миграции.

2. Разверните раздел **Вариант проверки**, чтобы открыть экран **Выбор варианта проверки**, укажите, нужно ли проверять переносимую базу данных на правильность запроса, а затем щелкните **Сохранить**.

3. Проверьте и подтвердите сведения, связанные с проектом миграции.
 
    ![Сводка по проекту миграции](media\tutorial-sql-server-to-managed-instance\dms-project-summary2.png)

4.  Щелкните **Сохранить**.   

## <a name="run-the-migration"></a>Выполнение миграции

- Выберите **Запустить миграцию**.

  Появится окно действия миграции, в котором будет указано состояние действия **Ожидание**.

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. Для обновления отображающихся данных нажмите кнопку **Обновить** на экране действия миграции.
 
   ![Выполняется действие миграции](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration1.png)

    Далее можно развернуть категории баз данных и имен для входа, чтобы отслеживать состояние переноса соответствующих объектов сервера.

   ![Выполняется действие миграции](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration-extend.png)

2. После завершения миграции нажмите **Скачать отчет**, чтобы получить отчет с подробными сведениями о процессе миграции.
 
3. Проверьте конечную базу данных в конечной среде Управляемого экземпляра базы данных SQL Azure.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о переносе базы данных в Управляемый экземпляр с помощью команды T-SQL RESTORE см. в [этой статье](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Сведения об Управляемом экземпляре см. в [обзоре Управляемого экземпляра](../sql-database/sql-database-managed-instance.md).
- Сведения о подключении приложений к Управляемому экземпляру см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).