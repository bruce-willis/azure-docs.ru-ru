---
title: Перенос баз данных SQL Server в Базу данных SQL Azure с помощью Database Migration Service по сети | Документация Майкрософт
description: Узнайте, как выполнять перенос баз данных из локального экземпляра SQL Server в базу данных SQL Azure по сети с помощью Azure Database Migration Service по сети.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: cbc30d1a70d7a333f56df6f0a796d3a8e6532fe8
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068734"
---
# <a name="migrate-sql-server-to-azure-sql-database-online-using-dms"></a>Перенос баз данных SQL Server в базу данных SQL Azure с помощью DMS по сети
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [Базу данных SQL Azure](https://docs.microsoft.com/azure/sql-database/) с минимальным временем простоя. В этом руководстве выполняется миграция базы данных **Adventureworks2012**, восстановленной в локальном экземпляре SQL Server 2016 (или более поздней версии), в базу данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * получение доступа к локальной базе данных с помощью помощника по миграции данных;
> * перенос примера схемы с помощью помощника по миграции данных;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.
> * скачивание отчета о миграции.

> [!IMPORTANT]
> Чтобы оптимизировать процесс миграции, Майкрософт рекомендует создать экземпляр Azure Database Migration Service в том же регионе Azure, в котором размещена целевая база данных. Перемещение данных между регионами и географическими областями может замедлить процесс миграции и привести к ошибкам.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется следующее:

- Скачайте и установите [SQL Server версии 2012 или более поздней](https://www.microsoft.com/sql-server/sql-server-downloads) (любой выпуск).
- При установке SQL Server Express протокол TCP/IP отключен по умолчанию. Включите его, выполнив инструкции в статье [Включение или отключение сетевого протокола сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Создайте экземпляр базы данных SQL Azure, следуя инструкциям в статье [Создание базы данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) версии 3.3 или более поздней.
- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure (VNET) не блокируют порты 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
- Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера базы данных SQL Azure, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
- Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server, имеют разрешения [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Убедитесь, что учетные данные, используемые для подключения к целевому экземпляру базы данных SQL Azure, имеют разрешения CONTROL DATABASE в целевых базах данных SQL Azure.
- Требуемая версия исходного SQL Server — SQL Server 2005 и выше. См. дополнительные сведения о том, как [определить версию, выпуск и уровень обновления системы SQL Server и ее компонентов](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Базы данных должны находиться в режиме неполного протоколирования или полного восстановления. См. дополнительные сведения о [просмотре или изменению модели восстановления базы данных (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Убедитесь в наличии полных резервных копий для всех баз данных. См. дополнительные сведения о [создании полной резервной копии базы данных (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Если у вас нет первичного ключа к какой-либо таблице, включите отслеживание измененных данных (CDC) в базе данных и этой таблице.
    > [!NOTE]
    > Чтобы найти таблицы без первичных ключей, используйте скрипт ниже.
    ```
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```
    >Если в результатах отображается одна или несколько таблиц со значением 0 в поле is_tracked_by_cdc, [включите отслеживание измененных данных в базе данных и этих таблицах (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Настройте роль распространителя для исходного сервера SQL Server.

    >[!NOTE]
    > Вы можете определить, установлены ли компоненты репликации, используя указанный ниже запрос.
    ```
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```
    Если результат вернет сообщение об ошибке с предложением установить компоненты репликации SQL Server, [установите их](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Если компоненты репликации уже установлены, проверьте настроена ли роль распространителя на исходном сервере SQL Server, с помощью указанной ниже команды -SQL.
    ```
    EXEC sp_get_distributor;
    ```

    Если распространение не настроено и сервер распространения возвращает NULL для указанной выше команды, настройте распространение с помощью инструкций в руководстве по [настройке публикации и распространения](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Отключите триггеры в целевой Базе данных SQL Azure.
    >[!NOTE]
    > Триггеры в целевой Базе данных SQL Azure можно найти с помощью следующего запроса:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Дополнительные сведения см. в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Оценка локальной базы данных
Перед переносом данных из локального экземпляра SQL Server в базу данных SQL Azure необходимо оценить базу данных SQL Server на наличие любых проблем, связанных с блокировкой, которые могут помешать миграции. В Помощнике по миграции данных версии 3.3 или более поздней выполните инструкции по [оценке миграции SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), чтобы завершить оценку локальной базы данных.

Чтобы оценить локальную базу данных, сделайте следующее:
1.  В Помощнике по миграции данных щелкните "Создать" (значок плюса), а затем выберите тип проекта **Оценка**.
2.  Укажите имя проекта в текстовом поле **Тип исходного сервера**, выберите **SQL Server**, в текстовом поле **Тип конечного сервера** выберите **База данных SQL Azure**, а затем нажмите **Создать**, чтобы создать проект.

    При оценке исходной Базы данных SQL Server, переносимой в Базу данных SQL Azure, можно выбрать один или несколько следующих типов отчетов об оценке:
    - проверка совместимости базы данных;
    - проверка четности компонентов.

    По умолчанию выбраны оба типа отчетов.

3.  На экране **Параметры** Помощника по миграции данных выберите **Далее**.
4.  На экране **Выберите источники** в диалоговом окне **Соединение с сервером** предоставьте сведения о подключении к SQL Server, а затем выберите **Подключить**.
5.  В диалоговом окне **Add sources** (Добавление источников) выберите **AdventureWorks2012**, а затем щелкните **Add** (Добавить) и выберите **Start Assessment** (Начать оценку).

    После завершения оценки результаты будут показаны, как на следующем рисунке:

    ![Оценка миграции данных](media\tutorial-sql-server-to-azure-sql-online\dma-assessments.png)

    В базе данных SQL Azure внутренняя оценка идентифицирует проблемы с равенством функций и проблемы, блокирующие миграцию.

    - Для категории **четности компонентов SQL Server** доступны разные рекомендации, а также описание альтернативных механизмы, используемых в Azure, и мер по устранению, которые помогут вам рассчитать трудозатраты для выполнения миграции.
    - Категория **проблем совместимости** определяет частично поддерживаемые или неподдерживаемые возможности, связанные с проблемами совместимости, которые могут блокировать миграцию локальных баз данных SQL Server в базу данных SQL Azure. Также предлагаются рекомендации, которые помогут вам решить эти проблемы.

6.  Просмотрите результаты оценки проблем, блокирующих миграцию, и проблем с четностью компонентов, выбрав конкретные параметры.

## <a name="migrate-the-sample-schema"></a>Перенос примера схемы
Если результаты оценки вас устраивают и выбранная база данных подходит для миграции в Базу данных SQL Azure, воспользуйтесь Помощником по миграции данных для переноса схемы в Базу данных SQL Azure.

> [!NOTE]
> Прежде чем создавать проект миграции в Помощнике по миграции данных, убедитесь, что База данных SQL Azure уже подготовлена, как описано выше. В рамках этого руководства для базы данных SQL Azure используется имя **AdventureWorksAzure**, но вы можете назвать ее иначе.

Чтобы перенести схему **AdventureWorks2012** в базу данных SQL Azure, выполните следующие действия:

1.  В Помощнике по миграции данных щелкните значок New (+) (Создать (+)), а затем в разделе **Project type** (Тип проекта) выберите **Migration** (Миграция).
2.  Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.
3.  В разделе **Migration Scope** (Область переноса) выберите **Schema only** (Только схема).

    После выполнения предыдущих действий интерфейс Помощника по миграции данных должен выглядеть, как показано на следующем рисунке:
    
    ![Создание проекта в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql-online\dma-create-project.png)

4.  Выберите **Создать**, чтобы создать проект.
5.  В Помощнике по миграции данных задайте сведения о подключении к источнику SQL Server, нажмите **Подключение**, а затем выберите базу данных **AdventureWorks2012**.

    ![Сведения о подключении к источнику в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql-online\dma-source-connect.png)

6.  В разделе **Подключение к целевому серверу** выберите **Далее**, укажите сведения о подключении к целевому объекту для Базы данных SQL Azure, нажмите **Подключить**, а затем выберите базу данных **AdventureWorksAzure**, предварительно подготовленную в Базе данных SQL Azure.

    ![Сведения о подключении к целевому объекту в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql-online\dma-target-connect.png)

7.  Выберите **Далее**, чтобы перейти на экран **Выбор объектов**, на котором можно указать объекты схемы в базе данных **AdventureWorks2012**, которую нужно развернуть в базе данных SQL Azure.

    По умолчанию выбраны все объекты.

    ![Создание сценариев SQL](media\tutorial-sql-server-to-azure-sql-online\dma-assessment-source.png)

8.  Выберите **Создать скрипт SQL**, чтобы создать скрипт SQL, а затем просмотрите его на наличие ошибок.

    ![Скрипт схемы](media\tutorial-sql-server-to-azure-sql-online\dma-schema-script.png)

9.  Выберите **Deploy schema** (Развернуть схему), чтобы развернуть схему в базу данных SQL Azure, а затем проверьте целевой сервер на наличие аномалий.

    ![Развертывание схемы](media\tutorial-sql-server-to-azure-sql-online\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration
1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.
 
   ![Отображение подписок на портале](media\tutorial-sql-server-to-azure-sql-online\portal-select-subscription1.png)
       
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
 
    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-azure-sql-online\portal-select-resource-provider.png)
    
3.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
 
    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-azure-sql-online\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра
1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска "Azure Database Migration Service", а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql-online\portal-marketplace.png)

2.  На экране **Azure Database Migration Service** выберите **Создать**.
 
    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-create1.png)
  
3.  На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите расположение, в котором нужно создать экземпляр Azure Database Migration Service. 

5. Выберите существующую виртуальную сеть или создайте новую.

    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и конечному экземпляру базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

6. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

    Если вам нужна помощь в выборе подходящей категории Azure Database Migration Service, см. рекомендации в [этой записи блога](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Настройка параметров экземпляра Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-settings2.png)

7.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции
После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).
 
      ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра Azure Database Migration Service и выберите его.
 
     ![Поиск экземпляра службы миграции базы данных Azure](media\tutorial-sql-server-to-azure-sql-online\dms-instance-search.png)
 
3. Выберите **+ Новый проект миграции**.
4. На экране **Новый проект миграции** задайте имя проекта, в текстовом поле **Тип исходного сервера** выберите **SQL Server**, а в текстовом поле **Тип целевого сервера** — **База данных SQL Azure**.
5. В разделе **Choose type of activity** (Выберите тип действия) выберите **Online data migration** (Оперативное перемещение данных).

    ![Создание проекта Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-create-project3.png)

    > [!NOTE]
    > Кроме того, вы можете выбрать **только создание проекта**, чтобы сейчас создать проект по миграции и позже выполнить миграцию.

6. Щелкните **Сохранить**.

7. Выберите **Создать и выполнить действие**, чтобы создать проект и выполнить действие миграции.

    ![Создание и выполнение действия Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-create-and-run-activity.png)
 
## <a name="specify-source-details"></a>Указание сведений об источнике
1. На экране **Сведения об источнике миграции** задайте сведения о подключении для исходного экземпляра SQL Server.
 
    Используйте для имени исходного экземпляра SQL Server полное доменное имя (FQDN). Если разрешение DNS-имен невозможно, можно использовать IP-адрес.

2. Если на исходном сервере доверенный сертификат не установлен, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создаст самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-подключения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежную защиту. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media\tutorial-sql-server-to-azure-sql-online\dms-source-details3.png)

## <a name="specify-target-details"></a>Указание сведений о цели
1. Выберите **Сохранить**, а затем на экране **Данные целевого объекта** укажите сведения о подключении для целевого сервера Базы данных SQL Azure — предварительно подготовленной Базы данных SQL Azure, где была развернута схема **AdventureWorks2012** с использованием Помощника по миграции данных.

    ![Выбор цели](media\tutorial-sql-server-to-azure-sql-online\dms-select-target3.png)

2. Нажмите кнопку **Сохранить**, а затем на экране **Сопоставление с целевыми базами данных** сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure Database Migration Service выберет целевую базу данных по умолчанию.

    ![Сопоставление с целевыми базами данных](media\tutorial-sql-server-to-azure-sql-online\dms-map-targets-activity3.png)

3. На экране **Выбрать таблицы** выберите **Сохранить**, разверните список таблиц и просмотрите список затронутых полей.

    Azure Database Migration Service автоматически выбирает все пустые исходные таблицы в целевом экземпляре Базы данных SQL. Чтобы повторно перенести таблицы, уже содержащие данные, необходимо явным образом выбрать их в этой колонке.

    ![Выбор таблиц](media\tutorial-sql-server-to-azure-sql-online\dms-configure-setting-activity3.png)

4.  Нажмите кнопку **Сохранить** в окне **Migration summary** (Сводка по миграции) и в поле **Имя действия** введите имя действия миграции, а затем проверьте соответствие сведениям о целевом и исходном сервере, которые вы указали ранее.

    ![Сводка по миграции](media\tutorial-sql-server-to-azure-sql-online\dms-migration-summary.png)

## <a name="run-the-migration"></a>Выполнение миграции
- Выберите **Запустить миграцию**.

    Появится окно действия миграции и в поле **Состояние** будет указано **Инициализация**.

    ![Состояние действия — инициализация](media\tutorial-sql-server-to-azure-sql-online\dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции
1. На экране действия миграции нажимайте **Обновить**, чтобы периодически обновлять содержимое экрана, пока в поле **Состояние** не будет указано **Выполнение**.

2. Выберите определенную базу данных, чтобы узнать состояние миграции для операций **полной загрузки данных** и **добавочной синхронизации данных**.

    ![Состояние действия — выполняется](media\tutorial-sql-server-to-azure-sql-online\dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Выполнение прямой миграции
После завершения начальной полной загрузки базы данных помечаются как **готовые к прямой миграции**.

1. Когда вы будете готовы выполнить миграцию базы данных, щелкните **Запустить прямую миграцию**.

    ![Запуск прямой миграции](media\tutorial-sql-server-to-azure-sql-online\dms-start-cutover.png)
 
2.  Обязательно остановите все входящие транзакции в исходную базу данных. Подождите, пока в счетчике **Ожидающие изменения** появится значение **0**.
3.  Выберите **Подтвердить**, а затем — **Применить**.
4. Когда состояние миграции базы данных изменится на **Завершено**, подключите свои приложения к новой целевой Базе данных SQL Azure.
 
    ![Состояние действия — завершено](media\tutorial-sql-server-to-azure-sql-online\dms-activity-completed.png)

## <a name="next-steps"></a>Дополнительная информация
- См. дополнительные сведения об [известных проблемах и ограничениях при выполнении миграции в базу данных SQL Azure по сети](known-issues-azure-sql-online.md).
- См. дополнительные сведения о [службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
- См. дополнительные сведения о [функциях службы "База данных SQL Azure"](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
