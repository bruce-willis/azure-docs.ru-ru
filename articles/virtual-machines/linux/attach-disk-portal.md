---
title: Подключение диска данных к виртуальной машине Linux | Документация Майкрософт
description: Подключение нового или имеющегося диска данных к виртуальной машине Linux с помощью портала.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 2823772787adf56dfbe216a68161f633eadba255
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001622"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Подключение диска данных к виртуальной машине Linux с помощью портала 
В этой статье показано, как подключить новый и существующий диски к виртуальной машине Linux на портале Azure. Вы также можете [подключить диск данных к виртуальной машине Windows на портале Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Прежде чем подключить диски к виртуальной машине, ознакомьтесь со следующими советами:

* Размер виртуальной машины определяет, сколько дисков данных к ней можно подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Для использования хранилища класса Premium вам необходимо будет использовать виртуальную машину серии DS или GS. Эти виртуальные машины позволяют использовать диски уровня "Премиум" и "Стандартный". Хранилище Premium доступно в определенных регионах. Дополнительные сведения см. в разделе [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* На самом деле диски, подключенные к виртуальным машинам, — это VHD-файлы, хранящиеся в Azure. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Подключив диск, необходимо [подключиться к виртуальной машине Linux для подключения нового диска](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Поиск виртуальной машины
1. Войдите на [портале Azure](https://portal.azure.com/).
2. В меню слева щелкните **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. На странице "Виртуальные машины" в разделе **Основные компоненты** щелкните **Диски**.
   
    ![Открытие параметров диска](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Подключение нового диска

1. В области **Диски** щелкните **+ Add data disk** (+ Добавить диск данных).
2. Щелкните раскрывающееся меню **Имя** и выберите пункт **Создать диск**:

    ![Создание управляемого диска Azure](./media/attach-disk-portal/create-new-md.png)

3. Введите имя управляемого диска. Просмотрите параметры по умолчанию, при необходимости измените их, а затем нажмите кнопку **Создать**.
   
   ![Просмотр параметров диска](./media/attach-disk-portal/create-new-md-settings.png)

4. Щелкните **Сохранить**, чтобы создать управляемый диск и обновить конфигурацию виртуальной машины:

   ![Сохранение нового управляемого диска Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. После того как Azure создаст диск и подключит его к виртуальной машине, он появится в параметрах дисков виртуальной машины в разделе **Диски данных**. Так как управляемые диски являются ресурсами верхнего уровня, диск отображается в корне группы ресурсов:

   ![Управляемый диск Azure в группе ресурсов](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Подключение существующего диска
1. В области **Диски** щелкните **+ Add data disk** (+ Добавить диск данных).
2. Щелкните раскрывающееся меню **Имя**, чтобы просмотреть список имеющихся управляемых дисков, доступных для подписки Azure. Выберите управляемый диск, который следует подключить:

   ![Подключение имеющегося управляемого диска Azure](./media/attach-disk-portal/select-existing-md.png)

3. Щелкните **Сохранить**, чтобы подключить имеющийся управляемый диск и обновить конфигурацию виртуальной машины:
   
   ![Сохранение обновлений управляемого диска Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. После того как Azure подключит диск к виртуальной машине, он будет отображаться в параметрах дисков виртуальной машины в разделе **Диски данных**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Подключение к виртуальной машине Linux для подключения нового диска
Чтобы разбить диск на разделы, отформатировать и подключить новый диск к виртуальной машине Linux, подключитесь к своей виртуальной машине по протоколу SSH. Дополнительные сведения см. в статье [Как использовать SSH с Linux в Azure](mac-create-ssh-keys.md). В следующем примере выполняется подключение к виртуальной машине с помощью общедоступной записи DNS *mypublicdns.westus.cloudapp.azure.com* и имени пользователя *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

После подключения к виртуальной машине можно подключить диск. Сначала найдите диск, используя `dmesg` (для обнаружения нового диска можно использовать и другой способ). В следующем примере используется команда dmesg для фильтрации по дискам *SCSI*:

```bash
dmesg | grep SCSI
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

В данном примере *sdc* — это диск, который нам нужен. Разобьем диск с помощью `fdisk`, установим его как основной диск в разделе 1 и примем остальные значения по умолчанию. В следующем примере запускается процесс `fdisk` в */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Используйте команду `n`, чтобы добавить новый раздел. В этом примере мы выбираем `p` для основного раздела и принимаем остальные значения по умолчанию. Результат будет выглядеть примерно так:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Распечатайте таблицу разделов, введя `p`, а затем используйте `w`, чтобы записать таблицу на диск и выйти. Результат должен выглядеть примерно следующим образом:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Теперь запишите файловую систему в раздел, выполнив команду `mkfs`. Укажите тип файловой системы и имя устройства. В следующем примере создается файловая система *ext4* в разделе */dev/sdc1*, созданном на предыдущем шаге:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Теперь создайте каталог для подключения файловой системы, используя `mkdir`. В следующем примере создается каталог в */datadrive*:

```bash
sudo mkdir /datadrive
```

Используйте `mount`, чтобы затем подключить файловую систему. В следующем примере раздел */dev/sdc1* подключается к точке подключения */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Чтобы обеспечить автоматическое повторное подключение диска после перезагрузки, его необходимо добавить в файл */etc/fstab*. Также для ссылки на диск настоятельно рекомендуется использовать в файле */etc/fstab* идентификатор UUID (глобальный уникальный идентификатор), а не просто имя устройства (например, */dev/sdc1*). Если операционная система обнаруживает ошибку диска во время загрузки, использование UUID позволяет избежать подключения ошибочного диска в это расположение. Остальные диски с данными затем получают те же идентификаторы устройств. Чтобы найти UUID нового диска, используйте служебную программу `blkid`:

```bash
sudo -i blkid
```

Результат должен быть аналогичным приведенному ниже:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Некорректное изменение файла **/etc/fstab** может привести к невозможности загрузить систему. Если у вас есть сомнения, см. инструкции по правильному изменению этого файла в документации дистрибутива. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

Затем откройте файл */etc/fstab* в текстовом редакторе, как показано ниже:

```bash
sudo vi /etc/fstab
```

В этом примере используйте значение UUID для устройства */dev/sdc1*, созданного на предыдущих шагах, и точку подключения */datadrive*. Добавьте следующую строку в конец файла */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Если вы позднее удалите диск данных без редактирования файла fstab, виртуальная машина может не загрузиться. Большинство дистрибутивов поддерживает параметры fstab *nofail* и (или) *nobootwait*. Эти параметры позволяют системе загружаться, даже если диск не подключится во время загрузки. Дополнительные сведения об этих параметрах см. в документации дистрибутива.
> 
> Параметр *nofail* обеспечивает запуск виртуальной машины даже в том случае, если файловая система повреждена или отсутствует диск во время загрузки. Без этого параметра может возникнуть ситуация, описанная в записи блога [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Не удается подключиться к виртуальной машине Linux по протоколу SSH из-за ошибок FSTAB).

### <a name="trimunmap-support-for-linux-in-azure"></a>Поддержка операций TRIM и UNMAP для Linux в Azure
Некоторые ядра Linux поддерживают операции TRIM и UNMAP для отмены неиспользуемых блоков на диске. Эту функцию особенно удобно использовать в хранилище уровня "Стандартный", чтобы сообщать Azure о том, что удаленные страницы больше не действительны и могут быть удалены. Это позволяет сократить затраты, если вы создаете большие файлы, а затем удаляете их.

Существует два способа включить поддержку операций TRIM в виртуальной машине Linux. Как обычно, обратитесь к документации дистрибутива, чтобы выбрать рекомендуемый метод.

* Используйте параметр подключения `discard` в */etc/fstab*. Ниже приведен пример.

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* В некоторых случаях параметр `discard` может негативно влиять на производительность. Кроме того, вы можете вручную выполнить команду `fstrim` из командной строки или добавить ее в crontab для регулярного выполнения.
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL или CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Дополнительная информация
Вы можете также [подключить диск данных](add-disk.md) с помощью Azure CLI.
