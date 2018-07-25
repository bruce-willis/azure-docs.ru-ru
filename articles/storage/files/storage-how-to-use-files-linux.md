---
title: Использование файлов Azure в Linux | Документы Майкрософт
description: Узнайте, как подключить файловый ресурс Azure через протокол SMB в Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.openlocfilehash: d4f77460ea6b0a31ed40286f33aa4296bafc9087
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007298"
---
# <a name="use-azure-files-with-linux"></a>Использование файлов Azure в Linux
[Файлы Azure](storage-files-introduction.md) — это простая в использовании облачная файловая система от Майкрософт. Файловые ресурсы Azure можно подключить в дистрибутивах Linux с помощью [SMB-клиента в ядре](https://wiki.samba.org/index.php/LinuxCIFS). В этой статье описаны два способа подключения файлового ресурса Azure: по запросу с помощью команды `mount` и при загрузке путем создания записи в `/etc/fstab`.

> [!NOTE]  
> Чтобы подключить файловый ресурс Azure за пределами региона Azure, в котором она размещается, например локально или в другом регионе Azure, операционная система должна поддерживать функции шифрования SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Предварительные требования для подключения файлового ресурса Azure с помощью Linux и пакета cifs-utils
<a id="smb-client-reqs"></a>
* **Выберите дистрибутив Linux, соответствующий вашим требованиям к подключению.**  
      Службу файлов Azure можно подключить с помощью SMB 2.1 или SMB 3.0. Для подключений, поступающих от клиентов в локальной среде или в других регионах Azure, служба файлов Azure отклонит SMB 2.1 (или SMB 3.0 без шифрования). Если для учетной записи хранения включен параметр *Требуется безопасное перемещение*, то служба файлов Azure разрешит только подключения по протоколу SMB 3.0 с шифрованием.
    
    Поддержка шифрования SMB 3.0 появилась в версии ядра Linux 4.11 и была добавлена в предшествующие версии ядер для популярных дистрибутивов Linux. На момент публикации этого документа функцию подключения, указанную в заголовках таблиц, поддерживают представленные ниже дистрибутивы из коллекции Azure. 

* ** Минимальные рекомендуемые версии с соответствующими возможностями подключения (SMB версии 2.1 и SMB версии 3.0) **    
    
    |   | SMB 2.1 <br>(подключение к виртуальным машинам в одном регионе) | SMB 3.0 <br>(подключение из локальной среды и между регионами) |
    | --- | :---: | :---: |
    | Сервер Ubuntu | 14.04 или более поздней версии | 16.04 или выше |
    | RHEL | 7 или выше | 7.5 или выше |
    | CentOS | 7 или выше |  7.5 или выше |
    | Debian | 8 или выше |   |
    | openSUSE | 13.2 или выше | 42.3 или выше |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 или выше |
    
    Если ваш дистрибутив Linux отсутствует в списке, можно проверить версию ядра Linux с помощью следующей команды.    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**Установите пакет cifs-utils.**  
    Пакет cifs-utils можно установить с помощью диспетчера пакетов в дистрибутиве Linux по своему усмотрению. 

    В дистрибутивах **Ubuntu** и **на основе Debian** используйте диспетчер пакетов `apt-get`:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    В **RHEL** и **CentOS** используйте диспетчер пакетов `yum`:

    ```bash
    sudo yum install cifs-utils
    ```

    В **openSUSE** используйте диспетчер пакетов `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    В других дистрибутивах используйте соответствующий диспетчер пакетов или выполните [компиляцию из источника](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).
    
* **Выберите разрешения для каталога и файлов подключаемого общедоступного ресурса**. В приведенных ниже примерах используется разрешение `0777`, чтобы все пользователи имели разрешения на чтение, запись и выполнение файлов. При необходимости можно заменить его другим [разрешением chmod](https://en.wikipedia.org/wiki/Chmod). 

* **Имя учетной записи хранения**: чтобы подключить файловый ресурс Azure, требуется имя учетной записи хранения.

* **Ключ учетной записи хранения**: чтобы подключить файловый ресурс Azure, требуется первичный (или вторичный) ключ к хранилищу данных. В настоящее время ключи SAS для подключения не поддерживаются.

* **Откройте порт 445**. Взаимодействие SMB выполняется через TCP-порт 445. Проверьте, чтобы брандмауэр не блокировал TCP-порты 445 с клиентского компьютера.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Подключение файлового ресурса Azure по запросу с помощью `mount`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Папку для точки подключения можно создать в любом месте файловой системы, но общепринято создавать ее в папке `/mnt`. Например: 

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Используйте команду mount для подключения файлового ресурса Azure**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` и `<mount-point>` соответствующими значениями для своей среды. Если ваш дистрибутив Linux поддерживает SMB 3.0 с шифрованием (дополнительные сведения см. в разделе [Использование файлов Azure в Linux](#smb-client-reqs)), укажите `3.0` для `<smb-version>`. Для дистрибутивов Linux, которые не поддерживают SMB 3.0 с шифрованием, укажите `2.1` для `<smb-version>`. Обратите внимание на то, что с помощью SMB 3.0 файловый ресурс Azure может быть подключен только за пределами региона Azure (включая локальную среду или другой регион Azure). 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Завершив работу с файловым ресурсом Azure, вы можете отключить его, выполнив команду `sudo umount <mount-point>`.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Создание постоянной точки подключения для файлового ресурса Azure с помощью `/etc/fstab`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Папку для точки подключения можно создать в любом месте файловой системы, но общепринято создавать ее в папке `/mnt`. Где бы вы ни создали ее, запишите абсолютный путь к этой папке. Например, следующая команда создает новую папку в папке `/mnt` (путь является абсолютным).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Создайте файл учетных данных, в котором будет храниться имя пользователя (имя учетной записи хранения) и пароль (ключ учетной записи хранения) для имени файлового ресурса.** Не забудьте заменить `<storage-account-name>` и `<storage-account-key>` на соответствующую информацию из вашей среды. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Измените разрешения для файла учетных данных таким образом, чтобы только пользователь с правами root мог читать или изменять файл пароля.** Поскольку ключ учетной записи хранения является паролем управляющего администратора учетной записи хранения, необходимо установить разрешения для файла, чтобы только у пользователя с правами root был доступ к файлу, а пользователи с более низким статусом прав не могли получать ключ учетной записи хранения.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Используйте следующую команду, чтобы добавить следующую строку в `/etc/fstab`**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<mount-point>` соответствующими значениями для своей среды. Если ваш дистрибутив Linux поддерживает SMB 3.0 с шифрованием (дополнительные сведения см. в разделе [Использование файлов Azure в Linux](#smb-client-reqs)), укажите `3.0` для `<smb-version>`. Для дистрибутивов Linux, которые не поддерживают SMB 3.0 с шифрованием, укажите `2.1` для `<smb-version>`. Обратите внимание на то, что с помощью SMB 3.0 файловый ресурс Azure может быть подключен только за пределами региона Azure (включая локальную среду или другой регион Azure). 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Вместо перезагрузки можно использовать `sudo mount -a`, чтобы подключить файловый ресурс после изменения `/etc/fstab`.

## <a name="feedback"></a>Отзыв
Пользователи Linux, нам очень интересно ваше мнение!

Файлы Azure для группы пользователей Linux включают форум, на котором можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи файлов Azure в Linux](mailto:azurefileslinuxusers@microsoft.com).

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию о службе файлов Azure см. по следующим ссылкам.
* [Общие сведения о службе файлов Azure](storage-files-introduction.md)
* [Планирование развертывания службы файлов Azure](storage-files-planning.md)
* [Часто задаваемые вопросы](../storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-linux-file-connection-problems.md)
