---
title: Обеспечение высокого уровня доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server | Документация Майкрософт
description: Сведения об обеспечении высокого уровня доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: e2e76e3cd058e5798b0159923118b050f38d077e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034643"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Обеспечение высокого уровня доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Для разработки в локальной среде вы можете обеспечить высокий уровень доступности для SAP HANA с помощью системной репликации HANA или общего хранилища.
В настоящее время на виртуальных машинах Azure поддерживается только одна функция высокого уровня доступности — системная репликация HANA в Azure. Для репликации SAP HANA используются один основной узел и по крайней мере один вторичный узел. Изменения данных на основном узле синхронно или асинхронно реплицируются на вторичные узлы.

В этой статье описывается развертывание и настройка виртуальных машин, установка платформы кластера, а также установка и настройка системной репликации SAP HANA.
В примерах конфигурации и командах установки используется номер экземпляра **03** и идентификатор системы HANA **HN1**.

Прежде всего прочитайте следующие примечания и документы SAP:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.
* примечание к SAP № [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP № [2205917], которое содержит рекомендуемые параметры ОС для SUSE Linux Enterprise Server for SAP Applications;
* примечание к SAP № [1944799], которое содержит рекомендации по SAP HANA для SUSE Linux Enterprise Server for SAP Applications;
* примечание к SAP № [2178632], которое содержит подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1984787], содержащее общие сведения о SUSE Linux Enterprise Server 12;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) со всеми необходимыми примечаниями к SAP для Linux;
* [Платформы IaaS, сертифицированные для SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide];
* статья [Развертывание программного обеспечения SAP на виртуальных машинах Linux в Azure][deployment-guide];
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide];
* [рекомендации по SUSE Linux Enterprise Server for SAP Applications версии 12 с пакетом обновления 3 (SP3)][sles-for-sap-bp]:
  * настройка инфраструктуры SAP HANA SR, оптимизированной для высокой производительности (SLES for SAP Applications версии 12 с пакетом обновления 1 (SP1)). Это руководство содержит все сведения, необходимые для настройки системной репликации SAP HANA в локальной среде для разработки. Используйте это руководство как основу.
  * настройка инфраструктуры SAP HANA SR, с оптимизированной стоимостью (SLES for SAP Applications версии 12 с пакетом обновления 1 (SP1)).

## <a name="overview"></a>Обзор

Чтобы достичь высокого уровня доступности, SAP HANA устанавливается на двух виртуальных машинах. Данные реплицируются с использованием системной репликации HANA.

![Обзор высокого уровня доступности SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Для системной репликации SAP HANA используется выделенное виртуальное имя узла и виртуальный IP-адрес. Балансировщику нагрузки в Azure нужен виртуальный IP-адрес. Ниже показана конфигурация подсистемы балансировки нагрузки.

* Конфигурация интерфейсной части: IP-адрес 10.0.0.13 для hn1-db.
* Конфигурация серверной части: подключение к основным сетевым интерфейсам всех виртуальных машин, которые должны входить в системную репликацию HANA.
* Порт пробы: 62503.
* Правила балансировки нагрузки: 30313 TCP, 30315 TCP, 30317 TCP.

## <a name="deploy-for-linux"></a>Развертывание для Linux

Агент ресурсов для SAP HANA включен в состав SUSE Linux Enterprise Server for SAP Applications.
В Azure Marketplace доступен образ для SUSE Linux Enterprise Server для приложения SAP 12, который можно использовать для развертывания новых виртуальных машин.

### <a name="deploy-with-a-template"></a>Развертывание с помощью шаблона

Все необходимые ресурсы можно развернуть с помощью шаблонов быстрого запуска с сайта GitHub. Шаблон развертывает виртуальные машины, подсистему балансировки нагрузки, группу доступности и т. д.
Чтобы развернуть шаблон, сделайте следующее.

1. Откройте [шаблон базы данных][template-multisid-db] или [конвергированный шаблон][template-converged] на портале Azure. 
    Шаблон базы данных создает правила балансировки нагрузки только для базы данных. Конвергированный шаблон также создает правила балансировки нагрузки для ASCS/SCS и экземпляра ERS (только Linux). Если вы планируете установить систему на основе SAP NetWeaver и экземпляр ASCS/SCS на одних и тех же компьютерах, используйте [конвергированный шаблон][template-converged].

1. Задайте следующие параметры:
    - **Sap System Id** (Идентификатор системы SAP). Введите идентификатор системы SAP, которую требуется установить. Идентификатор будет использоваться в качестве префикса для развертываемых ресурсов.
    - **Stack Type** (Тип стека). Указывается только при использовании конвергированного шаблона. Выберите тип стека SAP NetWeaver.
    - **Os Type** (Тип ОС). Выберите один из дистрибутивов Linux. В этом примере используется **SLES 12**.
    - **Db Type** (Тип базы данных). Выберите **HANA**.
    - **Sap System Size** (Размер системы SAP). Введите число систем SAP, которое должна предоставлять новая система. Если вы не знаете, сколько систем SAP потребуется, обратитесь к партнеру по технологиям или системному интегратору SAP.
    - **System Availability** (Доступность системы). Выберите значение **HA**.
    - **Admin Username and Admin Password** (Имя пользователя и пароль администратора). Создается новый пользователь, учетные данные которого можно использовать для входа на этот компьютер.
    - **New Or Existing Subnet** (Новая или существующая подсеть). Определяет, следует ли создать виртуальную сеть и подсеть или использовать имеющуюся подсеть. Если у вас уже есть виртуальная сеть, подключенная к локальной сети, выберите вариант **Existing** (Существующая).
    - **Subnet ID** (Идентификатор подсети).Чтобы развернуть виртуальную машину в имеющуюся виртуальную сеть с определенной подсетью, необходимо указать идентификатор этой определенной подсети. Идентификатор обычно имеет формат **/subscriptions/\<идентификатор_подписки>/resourceGroups/\<имя_группы_ресурсов>/providers/Microsoft.Network/virtualNetworks/\<имя_виртуальной_сети>/subnets/\<имя_подсети>**.

### <a name="manual-deployment"></a>Развертывание вручную

> [!IMPORTANT]
> Убедитесь, что выбранная операционная система сертифицирована для использования SAP HANA на определенных типах виртуальных машин. Список сертифицированных для SAP HANA типов виртуальных машин и выпусков ОС см. [здесь](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Чтобы просмотреть подробные сведения о поддерживаемых SAP HANA на определенных типах виртуальных машин выпусках ОС, щелкните необходимый тип виртуальной машины.
>  

1. Создайте группу ресурсов.
1. Создайте виртуальную сеть.
1. Создайте группу доступности.
   - Настройте максимальное число доменов обновления.
1. Создайте подсистему балансировки нагрузки (внутреннюю).
   - Выберите виртуальную сеть, созданную на шаге 2.
1. Создайте виртуальную машину 1.
   - Используйте образ SLES4SAP из коллекции Azure, который поддерживается для SAP HANA на выбранном типе виртуальной машины.
   - Выберите группу доступности, созданную на шаге 3.
1. Создайте виртуальную машину 2.
   - Используйте образ SLES4SAP из коллекции Azure, который поддерживается для SAP HANA на выбранном типе виртуальной машины.
   - Выберите группу доступности, созданную на шаге 3. 
1. Добавьте диски данных.
1. Настройте подсистему балансировки нагрузки. Сначала создайте пула IP-адресов для интерфейсной части.

   1. Откройте подсистему балансировки нагрузки, выберите **пул интерфейсных IP-адресов** и щелкните **Добавить**.
   1. Введите имя нового пула интерфейсных IP-адресов (например, **hana-frontend**).
   1. Для параметра **Назначение** выберите значение **Статическое** и введите IP-адрес (например, **10.0.0.13**).
   1. Нажмите кнопку **ОК**.
   1. Когда пул интерфейсных IP-адресов будет создан, запишите его IP-адрес.

1. Теперь создайте серверный пул.

   1. Откройте подсистему балансировки нагрузки, выберите **серверные пулы** и щелкните **Добавить**.
   1. Введите имя нового серверного пула (например, **hana-backend**).
   1. Щелкните **Добавить виртуальную машину**.
   1. Выберите группу доступности, созданную на шаге 3.
   1. Выберите виртуальные машины кластера SAP HANA.
   1. Нажмите кнопку **ОК**.

1. Создайте зонд работоспособности.

   1. Откройте подсистему балансировки нагрузки, выберите **Зонды работоспособности** и щелкните **Добавить**.
   1. Введите имя нового зонда работоспособности (например, **hana-hp**).
   1. Выберите протокол **TCP** и порт 625**03**. Сохраните значение "5" для параметра **Интервал** и значение "2" для параметра **Порог состояния неработоспособности**.
   1. Нажмите кнопку **ОК**.

1. Если используется SAP HANA 1.0, создайте правила балансировки нагрузки.

   1. Откройте подсистему балансировки нагрузки, выберите **Правила балансировки нагрузки** щелкните **Добавить**.
   1. Введите имя нового правила балансировки нагрузки (например, hana-lb-3**03**15).
   1. Выберите интерфейсный пул IP-адресов, серверный пул и зонд работоспособности, который вы создали ранее (например, **hana-frontend**).
   1. Для параметра **Протокол** сохраните значение **TCP** и введите порт 3**03**15.
   1. Увеличьте **время ожидания** до 30 минут.
   1. Не забудьте **включить плавающий IP-адрес**.
   1. Нажмите кнопку **ОК**.
   1. Повторите эти шаги для порта 3**03**17.

1. Если используется SAP HANA 2.0, создайте правила балансировки нагрузки для системной базы данных.

   1. Откройте подсистему балансировки нагрузки, выберите **Правила балансировки нагрузки** щелкните **Добавить**.
   1. Введите имя нового правила балансировки нагрузки (например, hana-lb-3**03**13).
   1. Выберите интерфейсный пул IP-адресов, серверный пул и зонд работоспособности, который вы создали ранее (например, **hana-frontend**).
   1. Для параметра **Протокол** сохраните значение **TCP** и введите порт 3**03**13.
   1. Увеличьте **время ожидания** до 30 минут.
   1. Не забудьте **включить плавающий IP-адрес**.
   1. Нажмите кнопку **ОК**.
   1. Повторите эти шаги для порта 3**03**14.

1. Если используется SAP HANA 2.0, создайте правила балансировки нагрузки для базы данных клиента.

   1. Откройте подсистему балансировки нагрузки, выберите **Правила балансировки нагрузки** щелкните **Добавить**.
   1. Введите имя нового правила балансировки нагрузки (например, hana-lb-3**03**40).
   1. Выберите пул внешних IP-адресов, внутренний пул и зонд работоспособности, созданные ранее (например, **hana-frontend**).
   1. Для параметра **Протокол** сохраните значение **TCP** и введите порт 3**03**40.
   1. Увеличьте **время ожидания** до 30 минут.
   1. Не забудьте **включить плавающий IP-адрес**.
   1. Нажмите кнопку **ОК**.
   1. Повторите эти шаги для портов 3**03**41 и 3**03**42.

Дополнительные сведения о портах для SAP HANA см. в главе о [подключениях к базам данных клиентов](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) руководства по [базам данных клиентов SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) или в [примечании к SAP № 2388694][2388694].


## <a name="create-a-pacemaker-cluster"></a>Создание кластера Pacemaker

Следуйте указаниям из статьи [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Настройка Pacemaker на SUSE Linux Enterprise Server в Azure), чтобы создать базовый кластер Pacemaker для этого сервера HANA. Также вы можете применить один кластер Pacemaker для SAP HANA и SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Установка SAP HANA

Во всех действиях этого раздела используются следующие префиксы.
- **[A]** — шаг применяется ко всем узлам.
- **[1]** — шаг применяется только к узлу 1.
- **[2]** — шаг применяется только к узлу 2 в кластере Pacemaker.

1. **[A]** Настройте разметку диска (для **диспетчера логических томов**).

   Мы рекомендуем использовать диспетчер логических томов для всех томов, предназначенных для хранения данных и файлов журнала. В приведенном ниже примере предполагается, что к виртуальным машинам подключены четыре диска данных, на которых созданы два тома.

   Вывод списка всех доступных дисков:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Выходные данные примера:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Создайте физические тома для всех дисков, которые вы хотите использовать.

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Создайте группу томов для файлов данных. Используйте одну группу томов для файлов журналов и другую — для общей папки SAP HANA.

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Создайте логические тома. Линейный том создается при использовании `lvcreate` без параметра `-i`. Чтобы улучшить производительность операций ввода-вывода, создайте чередующийся том. Значение аргумента `-i` обозначает количество базовых физических томов. В этом документе для тома данных используется 2 физических тома, поэтому аргумент `-i` имеет значение **2**. Журнальный том использует один физический том, поэтому параметр `-i` не используется явно. Параметр `-i`, значение которого соответствует числу базовых физических томов, необходимо указывать для всех томов данных, журналов или общих томов, для которых используется более одного физического тома.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Создайте каталоги подключения и скопируйте идентификаторы UUID всех логических томов.

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Создайте записи `fstab` для трех логических томов.       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Вставьте следующую строку в файл `/etc/fstab`:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Подключите новые тома:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Настройте разметку диска (для **обычных дисков**).

   Для демонстрационных систем файлы данных и журналов HANA можно поместить на один диск. Создайте раздел в пути /dev/disk/azure/scsi1/lun0 и отформатируйте его для файловой системы XFS.

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Добавьте такую строку в файл /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Создайте целевой каталог и подключите диск:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Настройка разрешения имен узлов для всех узлов.

   Вы можете использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере используется файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Вставьте следующие строки в файл /etc/hosts. Измените IP-адрес и имя узла в соответствии с параметрами среды.

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Установите пакеты высокого уровня доступности SAP HANA.

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Чтобы установить системную репликацию SAP HANA, выполните указания в главе 4 руководства [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/) (Сценарий системной репликации SAP HANA, оптимизированной для высокой производительности).

1. **[A]** Выполните программу **hdblcm** с DVD-диска HANA. Введите следующие значения на запрос в командной строке:
   * Choose installation (Выберите установку): введите **1**.
   * Select additional components for installation (Выберите дополнительные компоненты для установки): введите **1**.
   * Enter Installation Path (Введите путь установки) [/hana/shared]: нажмите клавишу "ВВОД".
   * Enter Local Host Name (Введите имя локального узла) [..]: нажмите клавишу "ВВОД".
   * "Do you want to add additional hosts to the system? (y/n)" (y/n) [n]: нажмите клавишу ВВОД.
   * Enter SAP HANA System ID (Введите идентификатор системы SAP HANA): введите идентификатор безопасности HANA, например **HN1**.
   * Enter Instance Number (Введите номер экземпляра) [00]: введите номер экземпляра HANA. Введите **03**, если вы использовали шаблон Azure или выполняли развертывание вручную по инструкциям из этой статьи.
   * Select Database Mode / Enter Index (Выберите режим базы данных / введите индекс) [1]: нажмите клавишу "ВВОД".
   * Select System Usage / Enter Index (Выберите использование системы / введите индекс) [4]: выберите значение использования системы.
   * Enter Location of Data Volumes (Введите расположение томов данных) [/hana/data/HN1]: нажмите клавишу ВВОД.
   * Enter Location of Log Volumes (Введите расположение томов журнала) [/hana/log/HN1]: нажмите клавишу ВВОД.
   * "Restrict maximum memory allocation?" [n]: нажмите клавишу ВВОД.
   * Enter Certificate Host Name For Host (Введите имя узла сертификатов для узла) "…" [...]: нажмите клавишу ВВОД.
   * Enter SAP Host Agent User (sapadm) Password (Введите пароль пользователя агента узла SAP (sapadm)): введите пароль пользователя для агента узла.
   * Confirm SAP Host Agent User (sapadm) Password (Подтвердите пароль пользователя агента узла SAP (sapadm)): повторно введите пароль пользователя для агента узла.
   * Enter System Administrator (hdbadm) Password (Введите пароль системного администратора (hdbadm)): введите пароль системного администратора.
   * Confirm System Administrator (hdbadm) Password (Подтвердите пароль системного администратора (hdbadm)): повторно введите пароль системного администратора.
   * Enter System Administrator Home Directory (Введите домашний каталог системного администратора) [/usr/sap/HN1/home]: нажмите клавишу ВВОД.
   * Enter System Administrator Login Shell (Укажите оболочку для входа администратора системы) [/bin/sh]: нажмите клавишу "ВВОД".
   * Enter System Administrator User ID (Введите идентификатор пользователя администратора системы) [1001]: нажмите клавишу "ВВОД".
   * Enter ID of User Group (sapsys) (Введите идентификатор для группы пользователей (sapsys)) [79]: нажмите клавишу "ВВОД".
   * Enter Database User (SYSTEM) Password (Введите пароль пользователя базы данных (SYSTEM)): введите пароль пользователя базы данных.
   * Confirm Database User (SYSTEM) Password (Подтвердите пароль пользователя базы данных (SYSTEM)): повторно введите пароль пользователя базы данных.
   * "Restart system after machine reboot?" [n]: нажмите клавишу ВВОД.
   * "Do you want to continue? (y/n)" (y/n): подтвердите введенные данные. Для продолжения введите **y**.

1. **[A]**. Обновите агент узла SAP.

   Скачайте последний архив агента узла SAP с сайта [SAP Softwarecenter][sap-swcenter] и выполните следующую команду, чтобы обновить агент. Замените путь к архиву, чтобы он указывал на скачанный файл.

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Настройка репликации системы SAP HANA 2.0

Во всех действиях этого раздела используются следующие префиксы.

* **[A]** — шаг применяется ко всем узлам.
* **[1]** — шаг применяется только к узлу 1.
* **[2]** — шаг применяется только к узлу 2 в кластере Pacemaker.

1. **[1]** Создайте базу данных клиента.

   Если вы используете SAP HANA 2.0 или MDC, создайте базу данных клиента для системы SAP NetWeaver. Замените **NW1** идентификатором SID для системы SAP.

   Войдите от имени пользователя \<hanasid>adm и выполните следующую команду.

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Настройте репликацию системы на первом узле.

   Войдите от имени пользователя \<hanasid>adm и выполните резервное копирование баз данных.

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Скопируйте системные файлы PKI на вторичный сайт.

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Создайте основной сайт:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Настройте системную репликацию на втором узле
    
   Зарегистрируйте второй узел, чтобы запустить репликацию системы. Войдите от имени пользователя \<hanasid>adm и выполните следующую команду.

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Настройка репликации системы SAP HANA 1.0

Во всех действиях этого раздела используются следующие префиксы.

* **[A]** — шаг применяется ко всем узлам.
* **[1]** — шаг применяется только к узлу 1.
* **[2]** — шаг применяется только к узлу 2 в кластере Pacemaker.

1. **[1]** Создайте обязательных пользователей.

   Войдите от имени привилегированного пользователя и выполните следующую команду. Обязательно замените строки, выделенные полужирным шрифтом (идентификатор системы HANA **HN1** и номер экземпляра **03**), значениями для своей системы SAP HANA.

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Создайте запись в хранилище ключей.

   Войдите от имени привилегированного пользователя и выполните следующую команду, чтобы создать новую запись в хранилище ключей:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Создайте резервную копию базы данных.

   Войдите от имени привилегированного пользователя и выполните резервное копирование баз данных:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Если вы используете мультитенантную систему, создайте резервные копии и для баз данных клиентов.

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Настройте репликацию системы на первом узле.

   Войдите от имени пользователя \<hanasid>adm и создайте основной сайт.

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Настройте репликацию системы на вторичном узле

   Войдите от имени пользователя \<hanasid>adm и зарегистрируйте вторичный сайт.

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Создание ресурсов кластера SAP HANA

Прежде всего создайте топологию HANA. Выполните следующие команды на любом из узлов кластера Pacemaker.

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Теперь создайте ресурсы HANA.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" is-managed="true" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
  params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Убедитесь, что состоянию кластера соответствует значение ОК и все ресурсы запущены. Не важно, на каком узле выполняются ресурсы.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описано, как проверить настроенную систему. Для каждого теста предполагается, что он выполняется от имени привилегированного пользователя, а главный узел SAP HANA выполняется на виртуальной машине **hn1-db-0**.

### <a name="test-the-migration"></a>Тестирование миграции

Перед началом теста убедитесь, что в Pacemaker не зарегистрировано действий, завершившихся ошибкой (с помощью команды crm_mon - r), нет непредвиденных ограничений на расположение (например, лишних элементов после теста миграции) и HANA находится в синхронизированном состоянии (например, с помощью SAPHanaSR-showAttr):

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Чтобы выполнить миграцию главного узла SAP HANA, выполните следующую команду.

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Если вы указали `AUTOMATED_REGISTER="false"`, такая последовательность команд перенесет главный узел SAP HANA и группу, которая содержит виртуальный IP-адрес, на узел hn1-db-1.

По окончании миграции выходные данные команды crm_mon -r будут выглядеть так:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Не удается запустить ресурс SAP HANA с узла hn1-db-0 на вторичном узле. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

При миграции создаются дополнительные ограничения расположения, которые следует удалить:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Очистите также состояние ресурса на вторичном узле:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Для отслеживания состояния ресурса HANA используйте команду crm_mon -r. Когда HANA запущена на hn1-db-0, эта команда должна возвращать следующий результат:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Проверка агента ограждения Azure (не SBD)

Чтобы проверить конфигурацию агента ограждения, отключите сетевой интерфейс на узле hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Виртуальная машина должна быть перезагружена или остановлена, в зависимости от настройки кластера.
Если для параметра `stonith-action` задано значение Off (отключено), то виртуальная машина будет остановлена, а ресурсы перенесены на работающую виртуальную машину.

Если вы задали `AUTOMATED_REGISTER="false"`, то после повторного запуска виртуальной машины ресурс SAP HANA не будет запущен в режиме вторичного узла. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Проверка ограждения SBD

Чтобы проверить настройку SBD, прервите выполнение процесса inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Узел кластера hn1-db-0 должен автоматически перезагрузиться. Службf Pacemaker может после этого не запуститься. Обязательно запустите ее.

### <a name="test-a-manual-failover"></a>Тестирование отработки отказа вручную

Чтобы проверить отработку отказа вручную, остановите службу `pacemaker` на узле hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

После отработки отказа можно снова запустить эту службу. Если вы указали `AUTOMATED_REGISTER="false"`, ресурс SAP HANA на узле hn1-db-0 не будет запущен в роли вторичного узла. В этом случае настройте экземпляр HANA в качестве вторичного узла, выполнив следующую команду.

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Тесты SUSE

> [!IMPORTANT]
> Убедитесь, что выбранная операционная система сертифицирована для использования SAP HANA на определенных типах виртуальных машин. Список сертифицированных для SAP HANA типов виртуальных машин и выпусков ОС см. [здесь](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Чтобы просмотреть подробные сведения о поддерживаемых SAP HANA на определенных типах виртуальных машин выпусках ОС, щелкните необходимый тип виртуальной машины.

Выполните все тестовые случаи, которые перечислены в руководствах по системной репликации SAP HANA, оптимизированной для высокой производительности или с оптимизированной стоимостью, в зависимости от параметров вашей среды. Эти руководства вы найдете на [странице рекомендаций SLES для SAP][sles-for-sap-bp].

Описания следующих тестов взяты из руководства по системной репликации SAP HANA, оптимизированной для высокой производительности, на сервере SUSE Linux Enterprise Server for SAP Applications версии 12 с пакетом обновлений 1 (SP1). Чтобы получить самую свежую версию этих описаний, используйте оригинал руководства. Прежде чем начинать тест, обязательно проверьте состояние синхронизации HANA и конфигурацию Pacemaker.

В следующих описаниях тестов мы предполагаем, что PREFER_SITE_TAKEOVER = true, а AUTOMATED_REGISTER = false.
Примечание. Описанные тесты должны выполняться поочередно и с учетом состояния завершения всех предыдущих тестов.

1. Тест 1. Остановка основной базы данных на узле 1

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker должен обнаружить, что экземпляр HANA остановлен, и выполнить отработку отказа на другой узел. Когда отработка отказа завершится, экземпляр HANA на узле hn1-db-0 останавливается, так как Pacemaker не выполняет автоматическую регистрацию вторичного узла HANA.

   Выполните следующие команды, чтобы зарегистрировать hn1-db-0 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. Тест 2. Остановка основной базы данных на узле 2

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker должен обнаружить, что экземпляр HANA остановлен, и выполнить отработку отказа на другой узел. Когда отработка отказа завершится, экземпляр HANA на узле hn1-db-1 останавливается, так как Pacemaker не выполняет автоматическую регистрацию вторичного узла HANA.

   Выполните следующие команды, чтобы зарегистрировать hn1-db-1 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. Тест 3. Сбой основной базы данных на узле 1

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker должен обнаружить, что экземпляр HANA прекратил работу, и выполнить отработку отказа на другой узел. Когда отработка отказа завершится, экземпляр HANA на узле hn1-db-0 останавливается, так как Pacemaker не выполняет автоматическую регистрацию вторичного узла HANA.

   Выполните следующие команды, чтобы зарегистрировать hn1-db-0 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. Тест 4. Сбой основной базы данных на узле 2

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker должен обнаружить, что экземпляр HANA прекратил работу, и выполнить отработку отказа на другой узел. Когда отработка отказа завершится, экземпляр HANA на узле hn1-db-1 останавливается, так как Pacemaker не выполняет автоматическую регистрацию вторичного узла HANA.

   Выполните следующие команды, чтобы зарегистрировать hn1-db-1 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. Тест 5. Сбой первичного узла сайта (узел 1)

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени привилегированного пользователя следующую команду на узле hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker должен обнаружить, что узел прекратил работу, и оградить этот узел. Завершив ограждение, Pacemaker выполнит перехват экземпляра HANA. После перезагрузки огражденного узла Pacemaker не запускается автоматически.

   Выполните следующие команды, чтобы запустить Pacemaker, очистить сообщения SBD для узла hn1-db-0, зарегистрировать hn1-db-0 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. Тест 6. Сбой вторичного узла сайта (узел 2)

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Выполните от имени привилегированного пользователя следующую команду на узле hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker должен обнаружить, что узел прекратил работу, и оградить этот узел. Завершив ограждение, Pacemaker выполнит перехват экземпляра HANA. После перезагрузки огражденного узла Pacemaker не запускается автоматически.

   Выполните следующие команды, чтобы запустить Pacemaker, очистить сообщения SBD для узла hn1-db-1, зарегистрировать hn1-db-1 в качестве вторичного узла и очистить сбойный ресурс.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. Тест 7. Остановка базы данных-получателя на узле 2

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker обнаружит, что экземпляр HANA остановлен, и пометит этот ресурс как сбойный на узле hn1-db-1. Pacemaker должен автоматически перезапустить экземпляр HANA. Выполните следующую команду, чтобы очистить состояние сбоя.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. Тест 8. Сбой базы данных-получателя на узле 2

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени пользователя \<hanasid>adm следующую команду на узле hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker обнаружит, что экземпляр HANA прекратил работу, и пометит ресурс как сбойный на узле hn1-db-1. Выполните следующую команду, чтобы очистить состояние сбоя. Pacemaker должен автоматически перезапустить экземпляр HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. Тест 9. Сбой вторичного узла сайта (узел 2), на котором выполняется база данных-получатель HANA

   Состояние ресурсов перед запуском теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Выполните от имени привилегированного пользователя следующую команду на узле hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker должен обнаружить, что узел прекратил работу, и оградить этот узел. После перезагрузки огражденного узла Pacemaker не запускается автоматически.

   Выполните следующие команды, чтобы запустить Pacemaker, очистить сообщения SBD для узла hn1-db-1 и очистить сбойный ресурс.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Состояние ресурсов после теста:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Дополнительная информация

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md).
