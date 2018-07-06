---
title: Установка кластера для Hadoop, Spark, Kafka, HBase или R Server — Azure HDInsight | Документация Майкрософт
description: Сведения об установке кластеров Hadoop, Kafka, Spark, HBase, R Server или Storm для HDInsight из браузера, с помощью Azure CLI, Azure PowerShell, REST или пакета SDK.
keywords: установка кластера hadoop, установка кластера kafka, установка кластера spark, что такое кластер hadoop
services: hdinsight
documentationcenter: ''
author: jamesbak
manager: jahogg
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 6707610fc3410eab6eb2985c7d8a0ef821354689
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062085"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Краткое руководство по установке кластеров в HDInsight

Из этого краткого руководства вы узнаете, как установить и настроить кластеры в HDInsight с использованием Hadoop, Spark, Kafka, кластера интерактивных запросов, HBase, R Server или Storm. Также вы научитесь настраивать кластеры, объединять их в домен и подключать к [Azure Data Lake Storage Gen2 (предварительная версия)](introduction.md).

Кластер Hadoop включает в себя несколько виртуальных машин (узлов), которые используются для распределенной обработки задач. Azure HDInsight управляет сведениями об установке и настройке отдельных узлов, поэтому вам нужно указать только общие сведения о конфигурации.

> [!IMPORTANT]
>Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. Узнайте, как [удалить кластер](../../hdinsight/hdinsight-delete-cluster.md).

В рамках этого краткого руководства в качестве уровня хранения данных используется Data Lake Storage. Благодаря наличию службы иерархических пространств имен и [драйвера Hadoop](abfs-driver.md) Data Lake Storage оптимизирован для процессов распределенной обработки и анализа. Данные, размещенные в Data Lake Storage, сохраняются даже после удаления кластера HDInsight.

## <a name="cluster-setup-methods"></a>Способы установки кластера

В приведенной ниже таблице представлены различные способы установки кластера HDInsight.

| Метод создания кластеров | браузер | Команда | ИНТЕРФЕЙС REST API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [портал Azure](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Фабрика данных Azure](../../hdinsight/hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI (версия 1.0)](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Шаблоны диспетчера ресурсов Azure](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Быстрое создание: установка базового кластера

В этой статье приводятся пошаговые инструкции по выполнению установки на [портале Azure](https://portal.azure.com), где можно создать кластер HDInsight, выбрав *Быстрое создание* или *Пользовательский*. 

![hdinsight create options custom quick create](media/quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Чтобы установить базовый кластер, следуйте инструкциям на экране. Ниже приведены сведения для следующих элементов:

* [Имя группы ресурсов](#resource-group-name)
* [Типы и конфигурация кластеров](#cluster-types) 
* [Имя входа в кластер и имя пользователя SSH](#cluster-login-and-ssh-username)
* [Местоположение.](#location)

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе о [прекращении поддержки HDInsight 3.3](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Имя группы ресурсов

[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) позволяет работать с ресурсами в приложении в виде группы, которая называется группой ресурсов Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции.

## <a name="cluster-types"></a> Типы и конфигурация кластеров
В настоящее время Azure HDInsight предоставляет указанные ниже типы кластеров, каждый из которых имеет набор компонентов для предоставления определенных функциональных возможностей.

> [!IMPORTANT]
> Доступны различные типы кластеров HDInsight, каждый из которых предназначен для отдельной рабочей нагрузки или технологии. Создать кластер, в котором бы объединились несколько типов, например Storm и HBase, нельзя. Если для решения нужны технологии, распределенные по нескольким типам кластеров HDInsight, [виртуальная сеть Azure](https://docs.microsoft.com/azure/virtual-network) может объединять необходимые типы кластеров. 
>
>

| Тип кластера | Функции |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Пакетный запрос и анализ хранимых данных |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |Обработка больших объемов данных NoSQL без схемы |
| [Интерактивный запрос](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Кэширование в памяти для обеспечения интерактивных и ускоренных запросов Hive |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | Распределенная платформа потоковой передачи с открытым кодом, которую можно использовать для создания конвейеров и приложений потоковой передачи данных в режиме реального времени. |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Разнообразная статистика больших данных, прогнозное моделирование и возможности машинного обучения |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Обработка в памяти, интерактивные запросы, обработка потоков микро-пакетов |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |Обработка событий в режиме реального времени |

### <a name="hdinsight-version"></a>Версия HDInsight

Выберите версию HDInsight для этого кластера. Дополнительные сведения см. в разделе [Поддерживаемые версии HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="enterprise-security-package"></a>Пакет безопасности корпоративного уровня

Для кластеров типа Hadoop, Spark и Interactive Query вы можете включить **пакет безопасности предприятия**. Этот пакет содержит параметр для более безопасной настройки кластера с помощью Apache Ranger и интеграции с Azure Active Directory. Дополнительные сведения см. в статье о [пакете безопасности предприятия в Azure HDInsight](../../hdinsight/domain-joined/apache-domain-joined-introduction.md).

![hdinsight create options choose enterprise security package](./media/quickstart-create-connect-hdi-cluster/hdinsight-creation-enterprise-security-package.png)

Дополнительные сведения о создании присоединенного к домену кластера HDInsight см. в статье о [создании присоединенной к домену среды песочницы HDInsight](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Имя входа в кластер и имя пользователя SSH

Во время создания кластера HDInsight можно настроить две учетные записи пользователя.

* Пользователь HTTP: имя пользователя HTTP по умолчанию — *admin*. Эта учетная запись использует базовую конфигурацию на портале Azure. Иногда его называют "пользователем кластера".
* Пользователь SSH (кластеры Linux): используется для подключения к кластеру через SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Пакет безопасности предприятия позволяет интегрировать HDInsight с Active Directory и Apache Ranger. При помощи пакета безопасности корпоративного уровня можно создать нескольких пользователей.

## <a name="location"></a>Расположение (регионы) для кластеров и хранилища

Расположение кластера не требуется указывать явно: кластер находится в том же расположении, что и хранилище по умолчанию. Просмотреть поддерживаемые регионы можно в раскрывающемся списке **Регион** на странице [Цены на HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Конечные точки хранилища для кластеров

Хотя при локальной установке кластера Hadoop для хранилища используется распределенная файловая система Hadoop (HDFS), в облаке применяются конечные точки хранилища, подключенные к кластеру. Для кластеров HDInsight используется [Data Lake Storage Gen2](abfs-driver.md) или [BLOB-объекты в службе хранилища Azure](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Использование службы хранилища Azure или Data Lake Storage означает, что вы можете спокойно удалять кластеры HDInsight, используемые для вычислений, не теряя данные.

> [!WARNING]
> Использование дополнительной учетной записи хранения, местоположение которой отличается от местоположения кластера HDInsight, не поддерживается.

В процессе настройки вам следует указать Data Lake Storage в качестве конечной точки хранения, используемой по умолчанию. Хранилище по умолчанию содержит журналы приложений и системный журнал. При необходимости вы можете указать дополнительные учетные записи Azure Data Lake Storage, к которым может обращаться кластер. Кластер HDInsight должен находиться в том же расположении Azure, что и зависимые учетные записи хранения.

![Параметры хранилища кластера: конечные точки хранилища, совместимые с HDFS](media/quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage-TEMP.png)

> [!IMPORTANT]
> Не забудьте **отключить доступ к Data Lake Store**. Эти настройки относятся к старой функции *Data Lake Store*, и их необходимо отключить для правильной работы *Data Lake Storage*.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Дополнительные хранилища метаданных
Вы можете создать дополнительные хранилища метаданных Hive или Oozie. Однако не все типы кластеров поддерживают хранилища метаданных, а хранилище данных SQL Azure не совместимо с хранилищами метаданных. 

Дополнительную информацию см. в статье [Использование внешних хранилищ метаданных в Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Создавая пользовательское хранилище метаданных, не используйте в имени базы данных тире, дефисы и пробелы. Это может привести к сбою при создании кластера.

### <a name="use-hiveoozie-metastore"></a>Хранилище метаданных Hive

Если вы хотите сохранить таблицы Hive после удаления кластера HDInsight, используйте пользовательское хранилище метаданных. Затем можно будет подключить это хранилище к другому кластеру HDInsight.

Хранилище метаданных HDInsight, созданное для одной версии кластера HDInsight, не может использоваться в других версиях такого кластера. Список версий HDInsight см. в разделе [Поддерживаемые версии HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Хранилище метаданных Oozie

Для повышения производительности Oozie используйте пользовательское хранилище метаданных. Хранилище метаданных также позволяет осуществлять доступ к данным задания Oozie после удаления кластера. 

> [!IMPORTANT]
> Повторно использовать хранилище метаданных Oozie невозможно. Чтобы использовать пользовательское хранилище метаданных Oozie, при создании кластера HDInsight необходимо предоставить пустую базу данных SQL Azure.

## <a name="configure-cluster-size"></a>Настройка размера кластера

Плата за использование узлов взимается, пока существует кластер. Начисление оплаты начинается после создания кластера и прекращается только после его удаления. Перевести кластер в режим ожидания или отменить его выделение невозможно.


### <a name="number-of-nodes-for-each-cluster-type"></a>Количество узлов для каждого типа кластера
Для каждого типа кластера используется своя терминология. Кроме того, типы отличаются количеством узлов и стандартными размерами виртуальных машин. В следующей таблице число узлов каждого типа указано в скобках.

| type | Nodes | Схема |
| --- | --- | --- |
| Hadoop |Головной узел (2), узел данных (от 1) |![Узлы кластера HDInsight Hadoop](media/quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |Головной сервер (2), региональный сервер (от 1), основной узел или узел Zookeeper (3) |![Узлы кластера HDInsight HBase](media/quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Узел Nimbus (2), сервер супервизора (от 1), узел Zookeeper (3) |![Узлы кластера HDInsight Storm](media/quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |Головной узел (2), рабочий узел (от 1), узел Zookeeper (3) (кат. "Бесплатный" для размера виртуальной машины Zookeeper A1) |![Узлы кластера HDInsight Spark](media/quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Дополнительные сведения см. в разделе [Конфигурация узлов и размеры виртуальных машин по умолчанию для кластеров](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) статьи "Что представляют собой компоненты и версии Hadoop, доступные в HDInsight?"

Затраты на кластеры HDInsight определяются числом узлов и размерами виртуальных машин на узлах. 

Кластеры разных типов отличаются типами, количеством и размерами узлов.
* Тип кластера Hadoop по умолчанию: 
    * два *головных узла*;  
    * четыре *узла данных*.
* Тип кластера Storm по умолчанию: 
    * два *узла Nimbus*;
    * три *узла ZooKeeper*;
    * четыре *узла супервизора*. 

При пробном использовании HDInsight рекомендуем применять один узел данных. Подробные сведения о ценах на HDInsight см. на [этой странице](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Максимальный размер кластера зависит от подписки Azure. Чтобы увеличить лимит, обратитесь в [службу поддержки по вопросам выставления счетов Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
>

При настройке кластера на портале Azure размер узла можно выбрать в колонке **Ценовые категории узлов**. На портале также отображаются расценки на узлы разных размеров. 

![Размеры узла виртуальной машины HDInsight](media/quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Размер виртуальных машин 
При развертывании кластеров выберите вычислительные ресурсы в зависимости от решения, которое планируется развернуть. Для кластеров HDInsight используются следующие виртуальные машины:
* виртуальные машины серий A и D1–4: [Размеры виртуальных машин Linux общего назначения](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general);
* виртуальные машины серии D11–14: [Размеры виртуальных машин Linux, оптимизированных для памяти](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Сведения о том, какие значения следует указывать для размера виртуальной машины при создании кластера с помощью различных пакетов SDK или Azure PowerShell, см. в разделе [Таблицы размеров](../../cloud-services/cloud-services-sizes-specs.md#size-tables). Используйте значение из столбца **Размер** таблиц, приведенных в этой статье.

> [!IMPORTANT]
> Если в кластере требуется более 32 рабочих узлов, для головного узла следует выбрать размер с по крайней мере 8 ядрами и 14 ГБ ОЗУ.
>
>

Дополнительные сведения см. в разделе [Размеры виртуальных машин](../../virtual-machines/windows/sizes.md). Сведения о расценках на разные размеры см. [здесь](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Пользовательская установка кластера
Пользовательская установка кластера основана на параметрах быстрого создания, к которым добавляются следующие параметры:
- [Приложения HDInsight](#hdinsight-applications)
- [Размер кластера](#cluster-size)
- Дополнительные параметры
  - [Действия скриптов](#customize-clusters-using-script-action)
  - [Виртуальная сеть](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Установка приложений HDInsight в кластерах

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight под управлением Linux. Вы можете использовать приложения, предоставляемые корпорацией Майкрософт, сторонними производителями или разработанные самостоятельно. Дополнительные сведения см. в статье [Установка сторонних приложений Hadoop в Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

Большинство приложений HDInsight устанавливаются в пустой граничный узел.  Пустой граничный узел — это виртуальная машина Linux, на которой установлены и настроены те же клиентские инструменты, что и на головном узле. Граничный узел можно использовать для доступа к кластеру, а также тестирования и размещения клиентских приложений. Подробные сведения см. в статье [Использование пустых граничных узлов в HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Дополнительные параметры: действия скриптов

Можно установить дополнительные компоненты или настроить конфигурацию кластера с помощью сценариев во время создания. Такие скрипты вызываются с помощью **действия скрипта** — параметра конфигурации, который может использоваться с помощью портала Azure, командлетов HDInsight PowerShell или пакета SDK для HDInsight .NET. Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Некоторые собственные компоненты Java, такие как Mahout и Cascading, могут выполняться в кластере как JAR-файлы. Эти JAR-файлы можно распространить в службе хранилища Azure или Azure Data Lake Storage и отправить в кластеры HDInsight с помощью механизмов отправки заданий Hadoop. Подробные сведения см. в статье [Отправка заданий Hadoop в HDInsight](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Если при развертывании или вызове JAR-файлов в кластерах HDInsight возникают проблемы, обратитесь в [службу поддержки Майкрософт](https://azure.microsoft.com/support/options/).
>
> Компонент Cascading не поддерживается службой HDInsight и на него не распространяется техническая поддержка Майкрософт. Списки поддерживаемых компонентов см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../../hdinsight/hdinsight-component-versioning.md)
>
>

В некоторых случаях требуется изменить следующие файлы конфигурации в процессе создания:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Подробные сведения см. в статье [Настройка кластеров HDInsight с помощью начальной загрузки](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Дополнительные параметры: расширение кластеров с помощью виртуальной сети
Если для решения нужны технологии, распределенные по нескольким типам кластеров HDInsight, [виртуальная сеть Azure](../../hdinsight/https://docs.microsoft.com/azure/virtual-network) может объединять необходимые типы кластеров. Благодаря такой конфигурации кластеры и любой развернутый в них код могут взаимодействовать друг с другом напрямую.

Подробные сведения об использовании виртуальных сетей Azure в HDInsight см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Пример использования двух типов кластера в виртуальной сети Azure см. в статье об [использовании структурированного потока Spark при помощи Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Дополнительные сведения об использовании HDInsight в виртуальной сети, включая требования к конфигурации виртуальной сети, см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Устранение неполадок управления доступом

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../../hdinsight/hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация

- [The Azure Blob Filesystem driver (ABFS): A dedicated Azure Storage driver for Hadoop](abfs-driver.md) (Драйвер файловой системы BLOB-объектов Azure, специально предназначенный для работы службы хранилища Azure c Hadoop)
- [Руководство. Извлечение, преобразование и загрузка данных с помощью Apache Hive в Azure HDInsight](tutorial-extract-transform-load-hive.md)
- [Общие сведения об Azure HDInsight, технологической платформе Hadoop и кластерах Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Работа в экосистеме Hadoop в HDInsight на компьютере с Windows](../../hdinsight/hdinsight-hadoop-windows-tools.md)
