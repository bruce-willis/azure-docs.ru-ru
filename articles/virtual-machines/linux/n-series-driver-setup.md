---
title: Установка драйвера серии N для Linux | Документация Майкрософт
description: Как установить драйверы NVIDIA GPU для виртуальных машин серии N под управлением Linux в Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/30/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d85bc79ddd08cb051b2e4d978a931f460020c10
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364506"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Установка драйверов GPU NVIDIA на виртуальные машины серии N под управлением Linux

Чтобы воспользоваться преимуществами GPU виртуальных машин Azure серии N под управлением Linux, необходимо установить графические драйверы GPU NVIDIA. [Расширение драйвера GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) устанавливает необходимые драйверы CUDA или GRID NVIDIA на виртуальную машину серии N. Для установки расширения и управления им можно использовать портал Azure или такие инструменты, как Azure CLI и шаблоны Azure Resource Manager. Сведения о поддерживаемых дистрибутивах и этапах развертывания см. в [документации по расширению драйвера GPU NVIDIA](../extensions/hpccompute-gpu-linux.md).

Если вы решили установить драйверы GPU вручную, то в этой статье вы найдете сведения о поддерживаемых дистрибутивах, ссылки на драйвера и инструкции по установке и проверке. Сведения о ручной установке драйверов также доступны для [виртуальных машин Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Характеристики виртуальных машин серии N, сведения о дисках и объеме памяти см. в статье [Размеры виртуальных машин Linux, оптимизированных для GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Установка драйверов CUDA на виртуальные машины серии N

Ниже приведены инструкции по установке драйверов CUDA из набора средств NVIDIA CUDA Toolkit на виртуальные машины серии N. 


Разработчики на языках C и C++ могут дополнительно установить полный набор средств для создания приложений, использующих ускорение на GPU. Дополнительные сведения см. в [руководстве по установке CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Чтобы установить драйверы CUDA, установите SSH-подключение к каждой виртуальной машине. Убедитесь, что в системе есть графический процессор с архитектурой CUDA, выполнив следующую команду:

```bash
lspci | grep -i NVIDIA
```
Вы увидите данные, похожие на следующий пример (для адаптера NVIDIA Tesla K80):

![результаты выполнения команды lspci](./media/n-series-driver-setup/lspci.png)

Затем выполните команды установки, относящиеся к используемому дистрибутиву.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Скачайте и установите драйверы CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  Установка может занять несколько минут.

2. Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

  ```bash
  sudo apt-get install cuda
  ```

3. Перезапустите виртуальную машину и приступите к проверке установки.

#### <a name="cuda-driver-updates"></a>Обновления драйверов CUDA

Рекомендуется периодически обновлять драйверы CUDA после развертывания.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS или Red Hat Enterprise Linux 7.3 или 7.4

1. Обновите ядро.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Повторно подключитесь к виртуальной машине и продолжите установку с помощью следующих команд:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  Установка может занять несколько минут. 

4. Чтобы дополнительно установить полный набор средств CUDA, введите следующую команду.

  ```bash
  sudo yum install cuda
  ```

5. Перезапустите виртуальную машину и приступите к проверке установки.

### <a name="verify-driver-installation"></a>Проверка установки драйверов

Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит 0 %, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Сетевое подключение RDMA

Сетевое подключение RDMA можно включить на виртуальных машинах серии N с поддержкой RDMA, таких как NC24r, развернутых в одной группе доступности или в одной группе размещения в масштабируемом наборе виртуальных машин. Сеть RDMA поддерживает трафик MPI (Message Passing Interface) для приложений, использующих Intel MPI 5.x или более поздней версии. Дополнительные требования приведены ниже.

### <a name="distributions"></a>Дистрибутивы

Разверните виртуальные машины серии N с поддержкой RDMA из одного из следующих образов Azure Marketplace, поддерживающих подключение RDMA на виртуальных машинах серии N:
  
* **Ubuntu 16.04 LTS**. Настройте драйверы RDMA на виртуальной машине и выполните регистрацию на сайте Intel, чтобы скачать Intel MPI.

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **HPC версии 7.4 на основе CentOS.** Драйверы RDMA и Intel MPI 5.1 будут установлены на виртуальной машине.

## <a name="install-grid-drivers-on-nv-series-vms"></a>Установка драйверов CUDA на виртуальные машины серии NV

Чтобы установить драйверы NVIDIA GRID на виртуальных машинах серии NV, подключитесь по протоколу SSH к каждой виртуальной машине и выполните действия, необходимые для дистрибутива Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Выполните команду `lspci`. Убедитесь, что карта или карты NVIDIA M60 отображаются как устройства PCI.

2. Установите обновления.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Отключите драйвер ядра Nouveau, который несовместим с драйвером NVIDIA. (На виртуальных машинах NV используйте только драйвер NVIDIA.) Чтобы сделать это, создайте в `/etc/modprobe.d ` файл с именем `nouveau.conf` со следующим содержимым:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Перезагрузите виртуальную машину и подключитесь повторно. Выйдите из X-сервера:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Загрузите и установите драйвер GRID.

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 

6. Когда появится запрос на запуск служебной программы nvidia-xconfig для обновления файла конфигурации X, выберите **Да**.

7. После завершения установки скопируйте /etc/nvidia/gridd.conf.template в новый файл gridd.conf в расположении /etc/nvidia/.

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Добавьте следующую строку в файл `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=FALSE
  ```
9. Перезапустите виртуальную машину и приступите к проверке установки.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS или Red Hat Enterprise Linux 

1. Обновите ядро и DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Отключите драйвер ядра Nouveau, который несовместим с драйвером NVIDIA. (На виртуальных машинах NV используйте только драйвер NVIDIA.) Чтобы сделать это, создайте в `/etc/modprobe.d ` файл с именем `nouveau.conf` со следующим содержимым:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Перезагрузите виртуальную машину, повторно подключитесь и установите последнюю версию [служб интеграции Linux для Hyper-V и Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Повторно подключитесь к виртуальной машине и выполните команду `lspci`. Убедитесь, что карта или карты NVIDIA M60 отображаются как устройства PCI.
 
5. Загрузите и установите драйвер GRID.

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 
6. Когда появится запрос на запуск служебной программы nvidia-xconfig для обновления файла конфигурации X, выберите **Да**.

7. После завершения установки скопируйте /etc/nvidia/gridd.conf.template в новый файл gridd.conf в расположении /etc/nvidia/.
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Добавьте следующую строку в файл `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=FALSE
  ```
9. Перезапустите виртуальную машину и приступите к проверке установки.

### <a name="verify-driver-installation"></a>Проверка установки драйверов


Чтобы запросить состояние устройства GPU, установите SSH-подключение к виртуальной машине и запустите служебную программу командной строки [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface), установленную вместе с драйвером. 

Если драйвер установлен, то отобразятся выходные данные, аналогичные приведенным ниже. Обратите внимание, что **GPU-Util** отобразит 0 %, если в данный момент графический процессор не выполняет рабочую нагрузку на виртуальной машине. Версия драйвера и сведения о GPU могут отличаться от показанных на изображении.

![Состояние устройства NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Сервер X11
Если вам требуется сервер X11 для удаленных подключений к виртуальной машине NV, рекомендуется использовать [x11vnc](http://www.karlrunge.com/x11vnc/), так как он поддерживает аппаратное ускорение графики. BusID устройства M60 необходимо вручную добавить в конфигурационный файл X11 (обычно `etc/X11/xorg.conf`). Добавьте раздел `"Device"`, аналогичный приведенному ниже.
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Чтобы использовать это устройство, обновите раздел `"Screen"`.
 
Чтобы найти десятичное число BusID, выполните эту команду:

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID может измениться, если виртуальная машина будет перераспределена или перезагружена. Для обновления BusID в конфигурации X11 при перезагрузке виртуальной машины можно создать и использовать сценарий. Например, создайте сценарий `busidupdate.sh` (или выберите другое имя) примерно со следующим содержимым:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Затем создайте запись для сценария обновления в `/etc/rc.d/rc3.d`, чтобы этот сценарий вызывался в качестве корневого сценария во время загрузки.

## <a name="troubleshooting"></a>Устранение неполадок

* Когда необходимо запрашивать карты, для быстрого получения выходных данных команды можно задать режим сохранения с помощью команды `nvidia-smi`. Чтобы задать режим сохранения, выполните `nvidia-smi -pm 1`. Обратите внимание, что в случае перезапуска виртуальной машины настройка режима не сохранится. Всегда можно написать сценарий настройки режима для выполнения при запуске.

## <a name="next-steps"></a>Дополнительная информация

* Чтобы записать образ виртуальной машины Linux с установленными драйверами NVIDIA, см. статью [Как подготовить к работе и записать образ виртуальной машины Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
