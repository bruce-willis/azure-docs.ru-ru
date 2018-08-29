---
title: Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure | Документация Майкрософт
description: Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 03623c64aad875ed46e7f578350e77cbd17c7c3b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42142609"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure.

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Существуют два варианта настройки кластера Pacemaker в Azure. Вы можете использовать агент ограждения, который выполняет перезапуск неисправного узла через интерфейсы API Azure, или можете использовать устройство SBD.

Для устройства SBD требуется по крайней мере одна дополнительная виртуальная машина, выступающая в роли сервера цели iSCSI и предоставляющая устройство SBD. Эти серверы цели iSCSI могут совместно использоваться другими кластерами Pacemaker. Преимуществом использования устройства SBD является ускоренная отработка отказа. Кроме того, при использовании устройств SBD в локальной среде не требуется вносить изменения на эксплуатацию кластера Pacemaker. В кластере Pacemaker можно использовать до трех устройств SBD, чтобы устройство SBD можно было сделать недоступным, например, во время установки исправлений ОС на сервере цели iSCSI. Если вы хотите использовать несколько устройств SBD в Pacemaker, разверните несколько серверов цели iSCSI и подключите одно устройство SBD из каждого из этих серверов. Мы рекомендуем использовать одно устройство SBD или три. Pacemaker не сможет автоматически ограждать узел кластера, если вы настроите только два устройства SBD и одно из них будет недоступно. Если вы хотите иметь возможность ограждения, когда один целевой сервер iSCSI не работает, необходимо использовать три устройства SBD и, следовательно, три сервера цели iSCSI.

Если вы не хотите тратить средства на дополнительную виртуальную машину, можно также использовать агент ограждения Azure. Недостатком этого подхода является то, что отработка отказа может занять от 10 до 15 минут, если не удается выполнить остановку ресурсов или узлам кластера не удается связаться друг с другом.

![Обзор кластера Pacemaker на SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> При планировании и развертывании Linux Pacemaker кластеризованных узлов и устройств SBD очень важно для общей надежности полной конфигурации кластера, чтобы маршрутизация между задействованными виртуальными машинами и виртуальными машинами, на которых размещены устройства SBD, не проходила через другие устройства, такие как [виртуальные сетевые устройства](https://azure.microsoft.com/solutions/network-appliances/). В противном случае проблемы и события обслуживания с виртуальным сетевым устройством могут иметь негативное влияние на стабильность и надежность общей конфигурации кластера. Во избежание таких препятствий при планировании и развертывании кластерных узлов Linux Pacemaker и устройств SBD не определяйте правила маршрутизации виртуальных сетевых устройств или [определяемые пользователем правила маршрутизации](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview), которые маршрутизируют трафик между кластерными узлами и устройствами SBD через виртуальные сетевые устройства и аналогичные устройства. 
>

## <a name="sbd-fencing"></a>Ограждение SBD

Если вы хотите использовать устройство SBD для ограждения, выполните следующие действия.

### <a name="set-up-iscsi-target-servers"></a>Настройка серверов цели iSCSI

Сначала необходимо создать целевые виртуальные машины iSCSI. Серверы цели iSCSI могут использовать совместно несколько кластеров Pacemaker.

1. Разверните новую виртуальную машину под управлением SLES 12 с пакетом обновления 1 (SP1) или более поздней версии и подключите ее по протоколу SSH. Эта виртуальная машина не обязательно должна быть большой. Будет достаточно выбрать размер Standard_E2s_v3 или Standard_D2s_v3. Убедитесь в том, что для диска ОС используется хранилище класса Premium.

Выполните следующие команды на всех **целевых виртуальных машинах iSCSI**.

1. Обновите SLES.

   <pre><code>sudo zypper update
   </code></pre>

1. Удалите пакеты.

   Чтобы избежать проблем с targetcli и SLES 12 с пакетом обновления 3 (SP3), удалите указанные ниже пакеты. Можно игнорировать сообщения об ошибках со сведениями о том, что пакет не удается обнаружить.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Установите пакеты цели iSCSI.

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Включите службу цели iSCSI.

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Создайте устройство iSCSI на сервере цели iSCSI.

Выполните следующие команды на всех **целевых виртуальных машинах iSCSI**, чтобы создать диски iSCSI для кластеров, используемых вашими системами SAP. В следующем примере создаются устройства SBD для нескольких кластеров. В нем показано, как использовать один сервер цели iSCSI для нескольких кластеров. Устройства SBD размещаются на диске операционной системы. Убедитесь, что на диске достаточно места.

**nfs** означает кластер NFS, **ascsnw1** — кластер ASCS **NW1**, **dbnw1** — кластер базы данных **NW1**, **nfs-0** и **nfs-1** — имена узлов кластера NFS, **nw1-xscs-0** и **nw1-xscs-1** — имена узлов кластера ASCS **NW1**, а **nw1-db-0** и **nw1-db-1** — имена узлов кластера базы данных. Замените их именами узлов кластера и SID системы SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluter of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Проверить правильность настройки можно следующим образом:

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Настройка устройства SBD

Подключитесь к устройству iSCSI, созданному на предыдущем шаге, из кластера.
Выполните следующие команды для узлов нового кластера, которые нужно создать.
Ниже приведены элементы с префиксами: **[A]**  — применяется ко всем узлам, **[1**] — применяется только к узлу 1, **[2]**  — применяется только к узлу 2.

1. **[A]**  Подключитесь к устройствам iSCSI.

   Сначала включите службы iSCSI и SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Измените имя инициатора на первом узле.

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Измените содержимое файла в соответствии со списками управления доступом, использованными при создании устройства iSCSI на сервере цели iSCSI, например, на NFS-сервере.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Измените имя инициатора на втором узле.

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Измените содержимое файла в соответствии со списками управления доступом, использованными при создании устройства iSCSI на сервере цели iSCSI.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Запустите службу iSCSI.

   Теперь запустите службу iSCSI для применения изменений.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Подключитесь к устройствам iSCSI. В следующем примере 10.0.0.17 является IP-адресом сервера цели iSCSI, а 3260 — это порт по умолчанию. <b>iqn.2006-04.nfs.local:nfs</b> является одним из целевых имен, которые возвращаются при выполнении первой приведенной ниже команды (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Убедитесь, что устройства iSCSI доступны, и запишите имя устройства (в следующем примере это /dev/sde).

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Теперь получите идентификаторы устройств iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Команда выдает три идентификатора для каждого устройства SBD. Мы рекомендуем использовать идентификатор, начинающийся со scsi-3. В приведенном примере это

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Создайте устройство SBD.

   Используйте идентификаторы устройств iSCSI, чтобы создать устройства SBD на первом узле кластера.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Адаптируйте конфигурацию SBD.

   Откройте файл конфигурации SBD.

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Измените значение свойства устройства SBD, включите интеграцию Pacemaker и измените режим запуска SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Создание файл конфигурации Softdog.

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Теперь загрузите модуль.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Установка кластера

Ниже приведены элементы с префиксами: **[A]**  — применяется ко всем узлам, **[1**] — применяется только к узлу 1, **[2]**  — применяется только к узлу 2.

1. **[A]** Обновите SLES.

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Настройка операционной системы

   В некоторых случаях Pacemaker создает много процессов и тем самым исчерпывает число допустимых процессов. В таком случае пульс между узлами кластера может завершиться с ошибкой и привести к отработке отказа ресурсов. Рекомендуется увеличить максимальное число разрешенных процессов, установив следующий параметр.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Уменьшите размер "грязного" кэша. Дополнительные сведения см. в статье [Low write performance on SLES 11/12 servers with large RAM](https://www.suse.com/support/kb/doc/?id=7010287) (Низкая производительность записи на серверах SLES 11/12 с большим объем ОЗУ).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[1]** Включите доступ по протоколу SSH.

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Включите доступ по протоколу SSH.

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Включите доступ по протоколу SSH.

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Установите агенты ограждения
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]** Установите разрешения имен.

   Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах. Преимущество использования /etc/hosts в том, что кластер становится независимым от службы DNS, которая также может являться единой точкой отказа.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Установите кластер.

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Добавьте узел в кластер.

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Измените пароль hacluster на тот же пароль.

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Настройте corosync для использования другого транспорта и добавьте список узлов. Иначе кластер не будет работать.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Добавьте следующее содержимое, выделенное полужирным шрифтом, в файл, если значения отсутствуют или отличаются. Не забудьте заменить токен на 30 000, чтобы разрешить обслуживание с сохранением памяти. Дополнительные сведения см. в [этой статье для Linux][virtual-machines-linux-maintenance] или [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Затем перезапустите службу corosync.

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Создание устройства STONITH с ролью агента ограждения Azure

Устройство STONITH использует субъект-службу для авторизации в Microsoft Azure. Чтобы создать субъект-службу, выполните следующее.

1. Перейдите на сайт <https://portal.azure.com>.
1. Откройте колонку "Azure Active Directory".  
   Перейдите в колонку "Свойства" и запишите идентификатор каталога. Это **идентификатор клиента**.
1. Щелкните "Регистрация приложений".
1. Нажмите "Добавить"
1. Введите имя, выберите тип приложения "Веб-приложение или API", введите URL-адрес входа (например, http://localhost)) и нажмите кнопку "Создать".
1. URL-адрес входа не используется и может быть любым допустимым URL-адресом.
1. Выберите новое приложение и щелкните "Ключи" на вкладке "Параметры".
1. Введите описание нового ключа, выберите "Срок действия не ограничен" и нажмите кнопку "Сохранить".
1. Запишите его значение. Он используется в качестве **пароля** субъекта-службы.
1. Запишите идентификатор приложения. Он используется в качестве имени пользователя (**идентификатора для входа** в следующих шагах) субъекта-службы.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Создайте пользовательскую роль для агента ограждения.

У субъекта-службы по умолчанию нет разрешений на доступ к ресурсам Azure. Необходимо предоставить ему разрешения на запуск и остановку (освобождение) всех виртуальных машин кластера. Если вы еще не создали эту пользовательскую роль, ее можно создать с помощью [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) или [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role).

Используйте следующее содержимое для входного файла. Необходимо адаптировать содержимое для ваших подписок, поэтому замените c276fc76-9cd4-44c9-99a7-4fd71546436e и e91d47c4-76f3-4271-a796-21b4ecfe3624 идентификаторами своих подписок. Если у вас имеется только одна подписка, удалите вторую запись в AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Назначьте пользовательскую роль субъекту-службе.

Назначьте субъекту-службе пользовательскую роль Linux Fence Agent Role, созданную в последней главе. Больше не используйте роль владельца!

1. Перейдите на сайт https://portal.azure.com.
1. Откройте колонку "Все ресурсы".
1. Выберите виртуальную машину первого узла кластера.
1. Выберите "Управление доступом (IAM)".
1. Нажмите "Добавить"
1. Выберите роль Linux Fence Agent Role.
1. Введите имя созданного ранее приложения.
1. Нажмите кнопку "ОК"

Повторите предыдущие шаги для второго узла кластера.

### <a name="1-create-the-stonith-devices"></a>**[1]** Создайте устройства STONITH.

После изменения разрешений для виртуальных машин можно настроить устройства STONITH в кластере.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Конфигурация Pacemaker по умолчанию для SBD

1. **[1]** Активируйте использование устройства STONITH и задайте задержку ограничения.

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="next-steps"></a>Дополнительная информация

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* [Обеспечение высокого уровня доступности NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server][sles-nfs-guide]
* [Руководство по обеспечению высокого уровня доступности виртуальных машин Azure для SAP NetWeaver на SUSE Linux Enterprise Server для приложений SAP][sles-guide]
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].
