---
title: Ускорение работы в сети при аварийном восстановлении виртуальных машин Azure | Документация Майкрософт
description: В этой статье описывается, как включить функцию ускорения работы в сети с Azure Site Recovery для аварийного восстановления виртуальных машин Azure.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/24/2018
ms.author: manayar
ms.openlocfilehash: af29333956fb962ed31133260ecbc9607de38fd3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052069"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Ускорение работы в сети при аварийном восстановлении виртуальных машин Azure

Функция ускорения работы в сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery позволяет использовать преимущества ускорения работы в сети для виртуальных машин Azure, для которых выполнена отработка отказа в другой регион Azure. В этой статье описывается, как включить функцию ускорения работы в сети для виртуальных машин Azure, реплицированных с помощью Azure Site Recovery.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы убедитесь, что вы хорошо понимаете следующие концепции:
-   [архитектура репликации](azure-to-azure-architecture.md) виртуальных машин Azure;
-   [настройка репликации](azure-to-azure-tutorial-enable-replication.md) для виртуальных машин Azure;
-   [отработка отказа](azure-to-azure-tutorial-failover-failback.md) для виртуальных машин Azure.

## <a name="accelerated-networking-with-windows-vms"></a>Ускорение работы в сети для виртуальных машин Windows

Azure Site Recovery поддерживает включение функции ускорения работы в сети для реплицированных виртуальных машин, только если в исходной виртуальной машине включена эта функция. Если в исходной виртуальной машине не включена функция ускорения работы в сети, сведения о том, как ее включить в виртуальной машине Windows, см. в [этой статье](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Без дополнительной настройки из коллекции Azure поддерживаются следующие дистрибутивы:
* **Windows Server 2016 Datacenter**
* **Центр обработки данных Windows Server 2012 R2**

### <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 2.  Поддерживаемые серии: D/DSv2 и F/Fs

На экземплярах, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается в экземплярах виртуальных машин с количеством виртуальных ЦП от 4. Поддерживаемые серии: D/DSv3, E/ESv3, Fsv2 и Ms/Mms

Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Ускорение работы в сети для виртуальных машин Linux

Azure Site Recovery поддерживает включение функции ускорения работы в сети для реплицированных виртуальных машин, только если в исходной виртуальной машине включена эта функция. Если в исходной виртуальной машине не включена функция ускорения работы в сети, сведения о том, как ее включить в виртуальной машине Linux, см. в [этой статье](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Без дополнительной настройки из коллекции Azure поддерживаются следующие дистрибутивы:
* **Ubuntu 16.04**
* **SLES 12 с пакетом обновления 3 (SP3)**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" с бэкпортированным ядром**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 2.  Поддерживаемые серии: D/DSv2 и F/Fs

На экземплярах, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается в экземплярах виртуальных машин с количеством виртуальных ЦП от 4. Поддерживаемые серии: D/DSv3, E/ESv3, Fsv2 и Ms/Mms.

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Включение ускорения работы в сети для реплицированных виртуальных машин

После [включения репликации](azure-to-azure-tutorial-enable-replication.md) для виртуальных машин Azure служба Site Recovery автоматически обнаруживает, включена ли в сетевых интерфейсах виртуальных машин функция ускорения работы в сети. Если функция ускорения работы в сети уже включена, Site Recovery автоматически настроит эту функцию в сетевых интерфейсах реплицированной виртуальной машины.

Состояние ускорения работы в сети можно проверить в разделе **Сетевые интерфейсы** колонки параметров **Вычисления и сеть** для реплицируемой виртуальной машины.

![Параметр "Ускоренная сеть"](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Если вы включили ускорение работы в сети на исходной виртуальной машине после включения репликации, эту функцию можно настроить для сетевых интерфейсов реплицированной виртуальной машины с помощью следующего процесса:
1. Откройте параметры **Вычисления и сеть** реплицируемой виртуальной машины.
2. Щелкните имя сетевого интерфейса в разделе **Сетевые интерфейсы**.
3. В столбце **Цель** из раскрывающегося списка для функции ускорения работы в сети выберите **Включено**.

![Включение ускорения работы в сети](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Описанный выше процесс также выполняется для имеющихся реплицированных виртуальных машин, в которых ускорение работы в сети раньше не включалось автоматически с помощью Site Recovery.

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [преимуществах функции ускорения работы в сети](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Дополнительные сведения об ограничениях функции ускорения работы в сети для [виртуальных машин Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) и [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- См. сведения в статье [Создание плана восстановления с помощью Site Recovery](site-recovery-create-recovery-plans.md).
