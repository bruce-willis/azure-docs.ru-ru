---
title: Перепроектирование приложения Contoso для использования контейнера Azure и Базы данных SQL Azure | Документация Майкрософт
description: Сведения о повторном проектировании приложения Contoso для использования контейнеров Azure для Windows и Базы данных SQL Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003203"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Миграция в компании Contoso: повторное проектирование локального приложения для использования контейнера Azure и Базы данных SQL Azure

В этой статье показано, как компания Contoso перемещает и повторно проектирует свое приложение SmartHotel в Azure. Специалисты компании перенесут виртуальную машину внешнего интерфейса приложения в контейнер Azure для Windows, а базу данных приложения — в Базу данных SQL Azure.

Это документ из цикла статей о том, как вымышленная компания Contoso переносит локальные ресурсы в облако Microsoft Azure. В статьях этого цикла содержатся общие сведения и сценарии развертывания, в которых проиллюстрирована настройка инфраструктуры миграции, оценка пригодности локальных ресурсов для миграции и выполнение различных типов миграции. Сценарии описаны в порядке возрастания сложности. Со временем мы добавим в этот цикл и другие статьи.

**Статья** | **Дополнительные сведения** | **Состояние**
--- | --- | ---
[Статья 1. Общие сведения](contoso-migration-overview.md) | Предоставляет общие сведения о стратегии миграции Contoso, цикле статей и примерах приложений, которые мы используем. | Доступна
[Статья 2. Развертывания инфраструктуры Azure](contoso-migration-infrastructure.md) | Здесь рассказывается о том, как Contoso готовит свою локальную инфраструктуру Azure для миграции. Для всех сценариев миграции Contoso используется одна и та же инфраструктура. | Доступна
[Статья 3. Доступ к локальным ресурсам](contoso-migration-assessment.md)  | В этой статье рассказывается, как компания Contoso выполняет оценку своего локального двухуровневого приложения SmartHotel в VMware. Для оценки виртуальных машин приложения компания Contoso использует службу [Миграция Azure](migrate-overview.md). Для оценки базы данных SQL Server приложения используется [помощник по миграции баз данных](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Доступна
[Статья 4. Повторное размещение приложения на виртуальных машинах Azure и в управляемом экземпляре SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Здесь демонстрируется, как Contoso выполняет процесс миграции приложения SmartHotel в Azure по методу lift-and-shift. Contoso переносит интерфейсную ВМ приложения с помощью [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Базу данных приложения она переносит в управляемый экземпляр SQL с помощью [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Доступна
[Статья 5. Перемещение приложения в виртуальные машины Azure](contoso-migration-rehost-vm.md) | В этой статье рассказывается, как компания Contoso переносит виртуальные машины приложения SmartHotel, используя только Site Recovery. | Доступна
[Статья 6. Повторное размещение приложения на виртуальных машинах Azure и в группах доступности SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Здесь показан выполняемый в Contoso процесс миграции приложения SmartHotel. Contoso использует Site Recovery для переноса ВМ приложений и службы миграции баз данных для миграции базы данных приложения в кластер SQL Server, защищенный группой доступности AlwaysOn. | Доступна
[Статья 7. Повторное размещение приложения Linux на виртуальных машинах Azure](contoso-migration-rehost-linux-vm.md) | Здесь показано, как компания Contoso осуществляет миграцию приложения Linux osTicket по методу lift-and-shift на виртуальные машины Azure с помощью Site Recovery | Доступна
[Статья 8. Повторное размещение приложения Linux на виртуальных машинах Azure и сервере MySQL в Azure](contoso-migration-rehost-linux-vm-mysql.md) | В этой статье рассказывается, как компания Contoso выполняет миграцию приложения Linux osTicket на виртуальные машины Azure с помощью Site Recovery и миграцию базы данных приложения в экземпляр Azure MySQL Server с помощью MySQL Workbench. | Доступна
[Статья 9. Рефакторинг приложения для веб-приложения Azure и Базы данных SQL Azure](contoso-migration-refactor-web-app-sql.md) | В статье описывается, как Contoso переносит приложение SmartHotel в веб-приложение Azure, а базу данных приложения — в экземпляр SQL Server Azure. | Доступна
[Статья 10. Рефакторинг приложения Linux для веб-приложений Azure и MySQL для Azure](contoso-migration-refactor-linux-app-service-mysql.md) | В статье описывается, как Contoso переносит приложение osTicket для Linux в веб-приложения Azure на нескольких сайтах, интегрированных с GitHub для непрерывной поставки. Специалисты компании переносят базу данных приложения в экземпляр MySQL в Azure. | Доступна
Статья 11. Повторное проектирование приложения в контейнерах Azure и Базе данных SQL Azure | В статье описывается, как Contoso переносит и повторно проектирует приложение SmartHotel в Azure. Специалисты компании повторно проектируют веб-уровень приложения как контейнер Windows и переносят базу данных приложения в Базу данных SQL Azure. | Эта статья.
[Статья 12. Повторное проектирование приложения для использования контейнеров Azure и Базы данных SQL Azure](contoso-migration-rearchitect-container-sql.md) | В статье описывается, как Contoso переносит и повторно проектирует приложение SmartHotel в Azure. Специалисты компании повторно проектируют веб-уровень приложения как контейнер Windows и переносят базу данных приложения в Базу данных SQL Azure. | Доступна
[Статья 13. Повторное создание приложения в Azure](contoso-migration-rebuild.md) | В статье описывается, как Contoso повторно создает свое приложение SmartHotel, используя ряд возможностей и служб Azure, включая Службу приложений Azure, Azure Kubernetes, "Функции Azure", Cognitive Services и Cosmos DB. | Доступна

В этой статье рассказывается, как Contoso выполняет миграцию двухуровневого приложения Windows. NET SmartHotel, работающего на виртуальных машинах VMware в Azure. Это приложение с открытым исходным кодом, и если вы хотите использовать его, загрузите его с [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Бизнес-факторы

Совместными усилиями ИТ-руководство и деловые партнеры определили указанные ниже цели этой миграции.

- **Адаптация к расширению бизнеса**. По мере развития компании Contoso растет нагрузка на локальные системы и инфраструктуры.
- **Повышение эффективности**: Contoso необходимо удалить ненужные процедуры и оптимизировать процессы для разработчиков и пользователей.  Бизнес нуждается в том, чтобы ИТ были быстрыми и чтобы не тратить время или деньги, обеспечивая более высокие требования клиентов.
- **Повышение гибкости**. ИТ-отдел компании Contoso нуждается в большей гибкости в отношении потребностей бизнеса. Должна реагировать быстрее, чем изменения на рынке, чтобы включить успех в глобальную экономику реагирования.  Он не должен мешать или становиться помехой для бизнеса.
- **Масштабирование**. По мере того как бизнес успешно растет, ИТ-отдел компании Contoso должен предоставлять системы, способные расти с тем же темпом.
- **Расходы**. Contoso хочет минимизировать стоимость лицензии.

## <a name="migration-goals"></a>Цели миграции

Группа, работающая над облачной инфраструктурой компании Contoso, определила цели этой миграции. Эти цели использовались для определения оптимального метода миграции.

**Цели** | **Дополнительные сведения**
--- | --- 
**Требования приложения** | Потребность использовать приложение в Azure всегда будет критически важной.<br/><br/> Должны быть доступны те же возможности производительности, что и в настоящее время в VMware.<br/><br/> Специалисты компании хотят прекратить поддержку ОС Windows Server 2008 R2, на которой в настоящее время работает приложение, и готовы вложить средства в соответствующее приложение.<br/><br/> Они хотят отойти от SQL Server 2008 R2, чтобы начать использовать современную платформу базы данных PaaS, что снизит необходимость в управлении.<br/><br/> Contoso хочет применить свои инвестиции в лицензирование SQL Server и Software Assurance, где это возможно.<br/><br/> Сотрудники компании хотят иметь возможность увеличить масштаб веб-уровня приложения.
**Ограничения** | Приложение состоит из приложения ASP.NET и службы WCF, работающей на той же виртуальной машине. С помощью Службы приложений Azure специалисты компании хотят разделить это приложение между двумя веб-приложениями. 
**Требования Azure** | Специалисты компании хотят перенести приложение в Azure и запустить его в контейнере для увеличения работоспособности приложения. Но реализовать его в Azure с нуля они не хотят. 

## <a name="solution-design"></a>Архитектура решения

Определив свои цели и требования, Contoso начинает разработку и анализ решения развертывания и идентифицирует процесс миграции, включая службы Azure, которые будут использоваться при этой миграции.

### <a name="current-app"></a>Текущее приложение

- Локальное приложение SmartHotel разбито на уровни на двух виртуальных машинах (WEBVM и SQLVM).
- Виртуальные машины расположены на узле VMware ESXi **contosohost1.contoso.com** (версии 6.5).
- Средой VMware управляет сервер vCenter Server 6.5 (**vcenter.contoso.com**), который запущен на виртуальной машине.
- Contoso использует локальный центр обработки данных (contoso-datacenter) с локальным контроллером домена (**contosodc1**).
- После завершения миграции локальные виртуальные машины в центре обработки данных Contoso будут выведены из эксплуатации.


### <a name="proposed-architecture"></a>Предлагаемая архитектура

- Для уровня базы данных приложения компания Contoso сравнивает Базу данных SQL Azure с SQL Server (в [этой статье](https://docs.microsoft.com/azure/sql-database/sql-database-features)). Специалисты компании решили продолжить работу с Базой данных SQL Azure по нескольким причинам:
    - База данных SQL Azure — это управляемая реляционная база данных. Она обеспечивает прогнозируемую производительность на нескольких уровнях обслуживания с почти нулевым администрированием. Среди преимуществ: динамическая масштабируемость без простоя, встроенная интеллектуальная оптимизация, глобальная масштабируемость и доступность.
    - Возможно использование упрощенного Помощника по миграции данных (DMA) для оценки и переноса локальной базы данных в SQL Azure.
    - В программе Software Assurance можно обменять имеющиеся лицензии на сниженные тарифы в Базе данных SQL с помощью программы "Преимущество гибридного использования Azure" для SQL Server. Это может обеспечить экономию до 30 %.
    - База данных SQL предоставляет ряд функций безопасности, включая постоянное шифрование, динамическую маскировку данных, обеспечение безопасности на уровне строк и обнаружение угроз.
- Специалисты компании решили преобразовать веб-приложение в контейнер Windows с помощью Visual Studio.
    - Они выполнят развертывание приложения с помощью Azure Service Fabric и извлекут образ контейнера Windows из Реестра контейнеров Azure (ACR).
    - Прототип расширения приложения для включения анализа тональности будет реализован как еще одна служба в Service Fabric, подключенная к Cosmos DB.  Он будет считывать сведения из твитов и отображать их в приложении.

    ![Архитектура сценария](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Проверка решения
Contoso оценивает предлагаемый дизайн, составляя список плюсов и минусов.

**Рассмотрение** | **Дополнительные сведения**
--- | ---
**Преимущества** | Код приложения SmartHotel необходимо изменить для миграции в Azure Service Fabric. Тем не менее реализация изменений требует минимальных усилий при использовании инструментов пакета SDK для Service Fabric.<br/><br/> С переходом на Service Fabric можно начать разработку микрослужб, чтобы через какое-то время быстро добавить их в приложение без риска для исходной базы кода.<br/><br/> Контейнеры Windows предоставляют те же преимущества, что контейнеры в целом. Они повышают гибкость, мобильность и управление.<br/><br/> Компания может задействовать свои инвестиции в программу Software Assurance, используя предложение "Преимущество гибридного использования Azure" для SQL Server и Windows Server.<br/><br/> После миграции можно будет отказаться от поддержки Windows Server 2008 R2. [Узнайте больше](https://support.microsoft.com/lifecycle).<br/><br/> Специалисты компании могут настроить веб-уровень приложения с несколькими экземплярами, и таким образом он больше не будет являться единой точкой отказа.<br/><br/> Исчезнет зависимость от устаревающей SQL Server 2008 R2.<br/><br/> База данных SQL поддерживает технические требования Contoso. Специалисты компании провели оценку локальной базы данных с помощью Помощника по миграции данных и обнаружили, что она является совместимой.<br/><br/> База данных SQL включает встроенную отказоустойчивость, которую Contoso не нужно настраивать. Это гарантия того, что уровень данных больше не является единой точкой отказа.
**Недостатки** | По сравнению с другими вариантами миграции контейнеры более сложные. Для Contoso длительное изучение контейнеров может стать проблемой.  Новый уровень сложности контейнеров представляет большую ценность, несмотря на длительное время, которое требуется на их изучение.<br/><br/> Рабочей группе в Contoso необходимо будет заполнить пробелы в знаниях, чтобы разобраться с Azure, контейнерами и микрослужбами приложения и обеспечить их обслуживание.<br/><br/> Если для переноса своей базы данных специалисты компании будут использовать Помощник по миграции данных вместо Службы миграции данных, инфраструктура Contoso не будет готова к переносу баз данных в масштабе.



### <a name="migration-process"></a>Процесс миграции

1. Contoso подготавливает кластер Azure Service Fabric для Windows.
2. Специалисты компании подготавливают экземпляр SQL Azure и переносят в него базу данных SmartHotel.
3. С помощью инструментов пакета SDK для Service Fabric они преобразуют виртуальную машину веб-уровня в контейнер Docker.
4. Они подключают кластер Service Fabric и ACR и развертывают приложение с использованием Azure Service Fabric.

    ![Процесс миграции](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Службы Azure

**Служба** | **Описание** | **Стоимость**
--- | --- | ---
[Помощник по миграции баз данных (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA используется для оценки и выявления проблем совместимости, которые могут повлиять на функциональность их базы данных в Azure. Помощник оценивает соотношение характеристик между источниками и целями SQL и предоставляет рекомендации по улучшению производительности и надежности. | Это средство можно загрузить бесплатно.
[база данных SQL Azure;](https://azure.microsoft.com/services/sql-database/) | Интеллектуальная полностью управляемая реляционная служба облачной базы данных. | Стоимость зависит от функций, пропускной способности и размера. [Узнайте больше](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/) | Для хранения образов для всех типов развертываний контейнера. | Стоимость зависит от функций, типа хранилища и длительности использования. [Узнайте больше](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Для создания и использования постоянно доступных, масштабируемых и распределенных приложений. | Стоимость зависит от размера, расположения и продолжительности работы вычислительных узлов. [Узнайте больше](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Предварительные требования

Ниже показано, что вам (и компании Contoso) необходимо сделать, чтобы выполнить этот сценарий:

**Требования** | **Дополнительные сведения**
--- | ---
**Подписка Azure.** | Вы должны были создать подписку, когда выполняли внутреннюю оценку в первой статье этой серии. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Если вы создаете бесплатную учетную запись, вы являетесь администратором своей подписки и можете выполнять любые действия.<br/><br/> Если вы используете существующую подписку, в которой не являетесь администратором, администратор должен назначить вам права владельца или участника.
**Инфраструктура Azure** | [Узнайте, как](contoso-migration-infrastructure.md) компания Contoso настраивает инфраструктуру Azure.
**Предварительные требования для разработчика** | Contoso необходимы следующие средства на рабочей станции разработчика:<br/><br/> - [выпуск Visual Studio Community 2017 15.5](https://www.visualstudio.com/);<br/><br/> включенная рабочая нагрузка .NET;<br/><br/> - [Git](https://git-scm.com/);<br/><br/> - [пакет SDK для Service Fabric версии 3.0 или более поздней](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started);<br/><br/> - [Docker CE (Windows 10) или Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/), настроенные на использование контейнеров Windows.



## <a name="scenario-steps"></a>Шаги выполнения сценария

Ниже рассказывается, как компания Contoso выполняет миграцию.

> [!div class="checklist"]
> * **Шаг 1. Подготовка экземпляра Базы данных SQL в Azure**. Contoso подготавливает экземпляр SQL в Azure. После миграции виртуальной машины веб-уровня внешнего интерфейса в контейнер Azure экземпляр контейнера с внешним веб-интерфейсом приложения укажет на эту базу данных.
> * **Шаг 2. Подготовка Azure Service Fabric**. Специалисты компании подготавливают кластер Service Fabric.
> * **Шаг 4. Миграция базы данных с помощью DMA**. Специалисты компании переносят базу данных приложения с помощью Помощника по миграции данных.
> * **Шаг 5. Преобразование приложения в контейнер**. Специалисты компании преобразовывают приложение в контейнер с помощью SDK Tools и Visual Studio.
> * **Шаг 6. Публикация приложения**. Специалисты компании выполняют публикацию приложения в ACR и кластере Service Fabric.
> * **Шаг 7. Расширение приложения**. После того, как приложение становится общедоступным, специалисты компании расширяют его, чтобы использовать возможности Azure, а затем повторно публикуют его в Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Шаг 1. Подготовка Базы данных SQL Azure

1. Специалисты компании выбирают Базу данных SQL в Azure. 

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Они указывают имя базы данных в соответствии с базой данных, работающей на локальной виртуальной машине (**SmartHotel.Registration**). Они размещают базу данных в группе ресурсов ContosoRG. Это группа ресурсов, которую специалисты компании используют для рабочих ресурсов в Azure.

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Специалисты компании настраивают новый экземпляр SQL Server (**sql-smarthotel-eus2**) в основном регионе.

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Они задают ценовую категорию в соответствии с потребностями своего сервера и базы данных, а также предпочитают сэкономить с помощью предложения "Преимущество гибридного использования Azure", так как у них уже есть лицензия SQL Server.
5. Для определения размера они используют вариант приобретения на основе виртуальных ядер и устанавливают ограничения в соответствии с ожидаемыми требованиями.

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Затем они создают экземпляр базы данных.

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. После создания экземпляра они открывают базу данных и отмечают сведения, которые им нужны при использовании Помощника по миграции базы данных.

    ![Подготовка SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Нужна дополнительная помощь?**

- [Получение справки](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) о подготовке Базы данных SQL.
- [Дополнительные сведения об](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) ограничениях ресурсов в модели приобретения на основе виртуальных ядер.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Шаг 2. Создание Реестра контейнеров Azure и подготовка контейнера Azure

Контейнер Azure создается с использованием экспортированных файлов из виртуальной машины веб-уровня. Контейнер размещается в Реестре контейнеров Azure (ACR).


1. Contoso создает Реестр контейнеров на портале Azure.

     ![Реестр контейнеров](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Специалисты компании предоставляют имя для реестра (**contosoacreus2**) и размещают его в основном регионе в группе ресурсов, которая используется для ресурсов инфраструктуры. Они обеспечивают доступ для администраторов и устанавливают для реестра номер SKU уровня "Премиум", чтобы была возможность использовать георепликацию.

    ![Реестр контейнеров](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Шаг 3. Подготовка Azure Service Fabric

Контейнер SmartHotel будет запускаться в кластере Azure Service Fabric. Contoso создает кластер Service Fabric следующим образом:

1. Создайте ресурс Service Fabric в Azure Marketplace.

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. В разделе **базовых** настроек специалисты компании предоставляют уникальное имя DS для кластера и учетные данные для доступа к локальной виртуальной машине. Они размещают ресурс в рабочей группе ресурсов (**ContosoRG**) в основном регионе "Восточная часть США 2".

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. В разделе **Конфигурация типа узла** нужно ввести имя типа узла, параметры устойчивости, размер виртуальной машины и конечные точки приложения.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. В разделе **создания хранилища ключей** нужно создать хранилище ключей в своей группе ресурсов инфраструктуры. В нем будет размещаться сертификат.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. В разделе **Политики доступа** нужно предоставить доступ к виртуальным машинам для развертывания хранилища ключей.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Затем нужно указать имя для сертификата.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. На странице сводки специалисты компании копируют ссылку, используемую для загрузки сертификата. Это необходимо для подключения к кластеру Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. После успешной проверки они подготавливают кластер.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. В мастере импорта сертификатов специалисты импортируют загруженный сертификат на компьютеры разработки. Сертификат используется для проверки подлинности в кластере.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. После подготовки кластера специалисты компании подключаются к проводнику кластеров Service Fabric Explorer.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Им нужно выбрать правильный сертификат.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. После загрузки Service Fabric Explorer администратор Contoso может управлять кластером.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>Шаг 3. Миграция базы данных с помощью DMA

Contoso перенесет базу данных SmartHotel с помощью DMA.

### <a name="install-dma"></a>Установка DMA

1. Они загружают инструмент с [центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=53595) к локальной виртуальной машине SQL Server (**SQLVM**).
2. Они запускают установку (DownloadMigrationAssistant.msi) на виртуальной машине.
3. Перед закрытием мастера на странице **Готово** необходимо выбрать **Launch Microsoft Data Migration Assistant** (Запустить помощник по миграции данных Майкрософт).

### <a name="configure-the-firewall"></a>Настройка брандмауэра

Чтобы подключиться к Базе данных SQL Azure, требуется правило брандмауэра.

1. В свойствах **брандмауэра и виртуальных сетей** для базы данных специалисты компании разрешают доступ к службам Azure и добавляют правило для клиентского IP-адреса локальной виртуальной машины SQL Server.
2. Создается правило брандмауэра на уровне сервера.

    ![Брандмауэр](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Требуется дополнительная помощь?

[Узнайте о](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) создании правил брандмауэра для Базы данных SQL Azure и управлении ими.

### <a name="migrate"></a>Миграция

1. В DMA создайте проект (**SmartHotelDB**) и выберите **миграцию**. 
2. Специалисты компании выбирают тип исходного сервера **SQL Server**, а целевого — **База данных SQL Azure**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. В подробностях о миграции они добавляют **SQLVM** в качестве исходного сервера и базу данных **SmartHotel.Registration**. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Они получают ошибку, которая, по всей видимости, связана с проверкой аутентификации. Однако после анализа оказывается, что проблема заключается в наличии точки (.) в имени базы данных. В качестве обходного решения специалисты компании решают подготовить новую базу данных SQL, используя имя **SmartHotel-Registration**. При повторном запуске DMA они смогут выбрать имя **SmartHotel-Registration** и продолжить работу с мастером.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. На вкладке **выбора объектов** они выбирают таблицы базы данных и генерируют скрипт SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. После того как DMS создаст скрипт, нужно нажать кнопку **Deploy schema** (Развернуть схему).

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA подтверждает, что развертывание выполнено успешно.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Теперь специалисты компании начинают процесс миграции.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. После завершения миграции Contoso может проверить, что база данных запущена в экземпляре SQL Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Специалисты компании удаляют на портале Azure дополнительную базу данных SQL **SmartHotel.Registration**.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>Шаг 4. Преобразование приложения в контейнер

Локальное приложение является традиционным трехуровневым приложением:

- Оно содержит веб-формы и службу WCF, которая подключается к SQL Server.
- Оно использует Entity Framework для интеграции с данными в базе данных SQL, предоставляя их через службу WCF.
- Приложение WebForms взаимодействует со службой WCF.

Contoso преобразует приложение в контейнер с помощью Visual Studio и SDK Tools следующим образом:

1. Специалисты компании локально клонируют репозиторий на компьютер разработчика:

    **git clone https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Используя Visual Studio, они открывают файл решения (SmartHotel.Registration.sln) в каталоге **SmartHotel360-internal-booking-apps\src\Registration** локального репозитория.  Показаны два приложения. SmartHotel.Registration.Web внешнего веб-интерфейса и приложение службы WCF SmartHotel.Registration.WCF.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Далее нужно щелкнуть веб-приложение правой кнопкой мыши > **Добавить** > **Container Orchestrator Support** (Поддержка оркестратора контейнеров).
4. В окне **поддержки оркестратора контейнеров** нужно выбрать **Service Fabric**.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso повторяет процесс для приложения SmartHotel.Registration.WCF.
6. Теперь Contoso проверяет, как изменилось решение.

    - Новое приложение — **SmartHotel.RegistrationApplication/**.
    - Оно содержит две службы: **SmartHotel.Registration.WCF** и **SmartHotel.Registration.Web**.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. С помощью Visual Studio был создан файл Docker и локально извлечены на компьютер разработчика требуемые образы.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Файл манифеста (**ServiceManifest.xml**) создается и открывается в Visual Studio. Этот файл сообщает Service Fabric, как настроить контейнер, когда он развернут в Azure.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Другой файл манифеста (**ApplicationManifest.xml) содержит приложения конфигурации для контейнеров.

    ![Контейнер](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Шаг 5. Публикация приложения


Наконец, специалисты компании Contoso могут опубликовать приложение в ACR и кластере Service Fabric.

> [!NOTE]
> В приложение SmartHotel были внесены некоторые изменения, связанные с кластером Service Fabric. Вы можете загрузить оригинальный и обновленный код приложения из [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). Измененный файл — **AppliationModern/ApplicationParameters/Cloud.xml**.


1. В Visual Studio обновите строку подключения для подключения приложения к Базе данных SQL Azure. Строку подключения можно найти в базе данных на портале Azure.

    ![Опубликовать](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso публикует приложение в Service Fabric с помощью Visual Studio. Нужно щелкнуть правой кнопкой мыши приложение Service Fabric > **Опубликовать**.

    ![Опубликовать](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Необходимо выбрать подписку, конечную точку подключения и ACR. Затем нажать кнопку **Опубликовать**.

    ![Опубликовать](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. По завершении развертывания SmartHotel теперь будет работать в Service Fabric.

    ![Опубликовать](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Чтобы подключиться к приложению, Contoso направляет трафик к общедоступному IP-адресу подсистемы балансировки нагрузки Azure на узлах Service Fabric.

    ![Опубликовать](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Шаг 6. Расширение приложения и повторная публикация

Теперь, когда приложение SmartHotel и база данных запущены в Azure, Contoso хочет расширить приложение.

- Разработчики Contoso создают прототипы нового приложения .NET Core, которое будет работать в кластере Service Fabric.
- Приложение будет использоваться для вывода данных тональности из CosmosDB.
- Эти данные будут представлены в виде твитов, которые обрабатываются с помощью бессерверной функции Azure и API анализа текста Cognitive Services.

### <a name="provision-azure-cosmos-db"></a>Подготовка Azure Cosmos DB

Сначала Contoso готовит базу данных Cosmos Azure.

1. Специалисты компании создают ресурс Azure Cosmos DB в Azure Marketplace.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Они предоставляют имя базы данных (**contososmarthotel**), выбирают API SQL и размещают ресурс в группе рабочих ресурсов в основном регионе "Восточная часть США 2".

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. В разделе о **начале работы** они выбирают **Обозреватель данных** и добавляют новую коллекцию.
4. В разделе **Добавить коллекцию** указываются идентификаторы и устанавливается емкость и пропускная способность.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. На портале открывается новая база данных > **Коллекция** > **Документы**, а затем — **Новый документ**.
6. Далее в окно документа вставляется код JSON, приведенный ниже. Это пример данных в виде одного твита.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Определяется конечная точка Cosmos DB и ключ проверки подлинности. Они используются в приложении для подключения к коллекции. В базе данных нужно щелкнуть **Ключи** и скопировать универсальный код ресурса (URI) и первичный ключ в Блокнот.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Обновление приложения тональности

Подготовив базу данных Cosmos DB, Contoso может настроить приложение для подключения к ней.

1. В Visual Studio в обозревателе решений нужно открыть файл ApplicationModern\ApplicationParameters\cloud.xml.

    ![Приложение тональности](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Заполняются следующие два параметра:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Приложение тональности](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Повторная публикация приложения

После расширения приложения Contoso повторно публикует его в Azure.

1. На портале нужно щелкнуть правой кнопкой мыши приложение Service Fabric > **Опубликовать**.

    ![Повторная публикация](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Необходимо выбрать подписку, конечную точку подключения и ACR. Затем нажать кнопку **Опубликовать**.

    ![Повторная публикация](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. По завершении развертывания SmartHotel теперь будет работать в Service Fabric. На консоли управления Service Fabric теперь отображаются три службы.

    ![Повторная публикация](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Специалисты компании Contoso могут щелкнуть службы, чтобы увидеть, что приложение SentimentIntegration запущено и работает.

    ![Повторная публикация](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Очистка после миграции

После миграции специалистам компании Contoso необходимо выполнить указанные ниже действия по очистке.  

- Удалите локальные виртуальные машины из перечня в vCenter.
- Удалить виртуальные машины из локальных заданий резервного копирования.
- Обновите внутреннюю документацию, чтобы показать новые расположения приложения SmartHotel. Покажите базу данных как запущенную в Базе данных SQL Azure, а внешний интерфейс как запущенный в Service Fabric.
- Проверить все ресурсы, взаимодействующие с резервными виртуальными машинами, и обновить все соответствующие параметры или документы согласно новой конфигурации.


## <a name="review-the-deployment"></a>Проверка развертывания

Теперь, когда выполняется миграция ресурсов в Azure, компании Contoso необходимо полностью ввести его в эксплуатацию и защитить свою новую инфраструктуру.

### <a name="security"></a>Безопасность

- Contoso необходимо обеспечить безопасность своей новой базы данных **SmartHotel-Registration**. [Узнайте больше](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- В частности, нужно обновить контейнер для использования SSL с сертификатами.
- Необходимо рассмотреть возможность использования KeyVault для защиты секретов для приложений Service Fabric. [Узнайте больше](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Резервные копии

- Contoso следует рассмотреть требования резервного копирования для Базы данных SQL Azure. [Узнайте больше](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Нужно рассмотреть реализацию групп отработки отказа для обеспечения отказоустойчивости в регионе для базы данных. [Узнайте больше](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Специалисты компании могут использовать георепликацию для номера SKU уровня "Премиум" ACR. [Узнайте больше](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso необходимо рассмотреть развертывание веб-приложения в основном регионе "Восточная часть США 2" и "Центральная часть США", когда Веб-приложение для контейнеров станет доступно. Специалисты компании могут настроить диспетчер трафика для обеспечения отработки отказа в случае региональных сбоев.

### <a name="licensing-and-cost-optimization"></a>Лицензирование и оптимизация затрат

- После развертывания всех ресурсов специалисты компании Contoso должны назначить теги Azure в соответствии со своим [планированием инфраструктуры](contoso-migration-infrastructure.md#set-up-tagging).
- Полное лицензирование входит в стоимость служб PaaS, которые использует Contoso. Это будет вычтено из EA.
1. Компания будет использовать Управление затратами Azure по лицензии Cloudyn (дочернее подразделение корпорации Майкрософт). Это решение по управлению затратами для нескольких облаков позволяет использовать Azure и другие облачные ресурсы, а также управлять ими.  Дополнительные сведения об управлении расходами см. [на этой странице](https://docs.microsoft.com/azure/cost-management/overview). 

## <a name="conclusion"></a>Заключение

В этой статье специалисты компании Contoso выполнили рефакторинг приложения SmartHotel в Azure путем миграции виртуальной машины внешнего интерфейса приложения в Service Fabric. Они перенесли базу данных приложения в Базу данных SQL Azure.





