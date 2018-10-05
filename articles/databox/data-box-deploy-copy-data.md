---
title: Копирование данных в Microsoft Azure Data Box| Microsoft Docs
description: Узнайте, как копировать данные в Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: alkohli
ms.openlocfilehash: 9bc84a9b08c4cfbdf7f24416c923e0dbd7076556
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161936"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Руководство: копирование данных в Azure Data Box 

В этом руководстве рассказано, как подключиться и скопировать данные с главного компьютера с помощью локального пользовательского веб-интерфейса, а затем подготовить Data Box к отправке.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * подключение к Data Box;
> * копирование данных в Data Box;
> * подготовка Data Box к отправке.

## <a name="prerequisites"></a>Предварительные требования

Перед тем как начать, убедитесь в следующем.

1. Вы изучили статью [Руководство: настройка Azure Data Box](data-box-deploy-set-up.md).
2. Вы получили Data Box; состояние заказа на портале **Доставлено**.
3. У вас есть главный компьютер с данными, которые необходимо скопировать в Data Box. На главном компьютере должно быть следующее ПО:
    - [поддерживаемая операционная система](data-box-system-requirements.md);
    - Компьютер должен быть подключен к высокоскоростной сети. Настоятельно рекомендуем использовать хотя бы одно подключение 10 GbE. Если подключение 10 GbE недоступно, можно использовать канал передачи данных 1 GbE, но при этом скорость копирования будет снижена. 

## <a name="connect-to-data-box"></a>Подключение к Data Box

В зависимости от выбранной учетной записи хранения Data Box создает указанные ниже ресурсы.
- До трех общих папок для каждой связанной учетной записи хранения (GPv1 и GPv2).
- Одну общую папку для учетной записи хранилища BLOB-объектов или учетной записи хранения уровня "Премиум". 

В общих папках для блочных и страничных BLOB-объектов объектами первого уровня являются контейнеры, а второго — большие двоичные объекты. В общих папках для файлов Azure объекты первого уровня — общие папки, а объекты второго уровня — файлы.

Рассмотрим следующий пример. 

- Учетная запись хранения: *Mystoracct*.
- Общая папка для блочных BLOB-объектов: *Mystoracct_BlockBlob/my-container/blob*.
- Общая папка для страничных BLOB-объектов: *Mystoracct_PageBlob/my-container/blob*.
- Общая папка для файлов: *Mystoracct_AzFile/my-share*.

В зависимости от того, к какому главному компьютеру (с ОС Windows Server или с ОС Linux) подключено устройство Data Box, действия по подключению и копированию могут отличаться.

### <a name="connect-via-smb"></a>Подключение по протоколу SMB 

Если у вас главный компьютер с ОС Windows Server, для подключения к Data Box выполните указанные ниже действия.

1. В первую очередь необходимо выполнить проверку подлинности и начать сеанс. Перейдите на страницу **Подключение и копирование**. Чтобы получить учетные данные для доступа к общим папкам, связанным с вашей учетной записью хранения, щелкните **Получить учетные данные**. 

    ![Получение учетных данных для доступа к общей папке 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. В диалоговом окне "Доступ к общей папке и копирование данных" скопируйте **имя пользователя** и **пароль**, соответствующие необходимой общей папке. Последовательно выберите **ОК**.
    
    ![Получение учетных данных для доступа к общей папке 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Используйте свою учетную запись хранения (в примере ниже это учетная запись Mystoracct) для доступа к общим папкам. Для доступа к общим папкам используйте путь `\\<IP of the device>\ShareName`. Для подключения к общим папкам (с помощью имени общей папки) используйте указанные ниже адреса. Выбор конкретного адреса зависит от используемого вами формата данных. 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Чтобы подключиться к общим папкам на главном компьютере, откройте окно командной строки. В командной строке выполните следующую команду:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    При отображении соответствующего запроса введите пароль для общей папки. В примере ниже показан процесс подключения к общей папке с помощью указанной выше команды.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Нажмите клавиши WINDOWS+R. В окне **Выполнить** введите `\\<device IP address>`. Последовательно выберите **ОК**. Откроется проводник.
    
    ![Подключение к общей папке с помощью проводника 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. Должны отобразиться папки, в том числе общие. Создайте папку для файлов, которые вы собираетесь скопировать (в этом случае шаблоны). Иногда папки могут быть помечены серым крестом. Этот крест не означает ошибку. Папки помечены приложением для отслеживания их состояния.
    
    ![Подключение к общей папке с помощью проводника 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Подключение к общей папке с помощью проводника 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Подключение по протоколу NFS 

Если вы используете главный компьютер с ОС Linux, то выполните указанные ниже действия, чтобы настроить Data Box для доступа к NFS-клиентам.

1. Укажите IP-адреса клиентов, которым разрешен доступ к общей папке. В локальном пользовательском веб-интерфейсе перейдите на страницу **Подключение и копирование**. В разделе **Параметры NFS** щелкните **Клиентский доступ NFS**. 

    ![Настройка клиентского доступа NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Укажите IP-адрес NFS-клиента и щелкните **Добавить**. Вы можете настроить доступ для нескольких NFS-клиентов, выполнив это действие для каждого клиента. Последовательно выберите **ОК**.

    ![Настройка клиентского доступа NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Убедитесь, что на главном компьютере с ОС Linux установлен NFS-клиент [поддерживаемой версии](data-box-system-requirements.md). Используйте версию, подходящую для используемого вами дистрибутива ОС Linux. 

3. После установки NFS-клиента подключите общую папку NFS на устройстве Data Box, выполнив указанную ниже команду.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    В примере ниже показано, как подключиться к общей папке Data Box по протоколу NFS. Устройство Data Box имеет IP-адрес `10.161.23.130`, общая папка `Mystoracct_Blob` подключена в виртуальной машине ubuntuVM, а точка подключения имеет название `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Копирование данных в Data Box

После подключения к общим папкам Data Box можно скопировать данные. Прежде чем копировать данные, выполните указанные ниже проверки.

- Убедитесь, что вы собираетесь копировать данные в общие папки, предназначенные для используемого вами формата данных. Например, данные блочного BLOB-объекта необходимо копировать в общую папку для блочных BLOB-объектов. Если формат данных не соответствует общей папке, то на следующем этапе вам не удастся отправить данные в Azure.
-  При копировании данных убедитесь, что размер данных соответствует ограничениям на размер, указанным в статье [Ограничения для службы хранилища Azure и Data Box](data-box-limits.md). 
- Если данные, отправляемые Data Box, одновременно отправляются другими приложениями за пределами Data Box, это может привести к сбоям заданий отправки и повреждению данных.
- Рекомендуем не использовать протоколы SMB и NFS одновременно либо копировать одни и те же данные в одно и то же конечное расположение в Azure. В таких случаях невозможно предсказать окончательный результат.

### <a name="copy-data-via-smb"></a>Копирование данных по протоколу SMB

После подключения к общей папке SMB запустите копирование данных. 

Кроме того, для копирования данных можно использовать любое средство для копирования файлов, совместимое с протоколом SMB, например Robocopy. С помощью средства Robocopy можно запустить несколько заданий копирования. Используйте следующую команду:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Атрибуты команды описаны в таблице ниже.
    
|Атрибут  |ОПИСАНИЕ  |
|---------|---------|
|/e     |Копирование подкаталогов, включая пустые каталоги.         |
|/r:     |Указание количества повторных попыток для неудавшихся копий.         |
|/w:     |Указание времени ожидания между повторными попытками в секундах.         |
|/is     |Включает одни и те же файлы.         |
|/nfl     |Указание, что имена файлов не должны регистрироваться.         |
|/ndl    |Указание, что имена каталогов не должны регистрироваться.        |
|/np     |Указывает, что не нужно отображать ход выполнения операции копирования (количество копируемых файлов или каталогов). Отображение хода выполнения значительно снижает производительность.         |
|/MT     | Использование многопоточности; рекомендуется использовать 32 или 64 потока. Этот параметр не используется при работе с зашифрованными файлами. Вам может потребоваться отделить зашифрованные файлы от незашифрованных. Копирование с использованием одного потока значительно снижает производительность.           |
|/fft     | Используется для уменьшения степени детализации меток времени при работе с любыми файловыми системами.        |
|/b     | Копирует файлы в режиме резервного копирования.        |
|/z    | Копирует файлы в режиме перезапуска. Используйте этот параметр, если среда нестабильна. Этот параметр уменьшает пропускную способность из-за внесения дополнительных сведений в журналы.      |
| /zb     | Использует режим перезапуска. Если доступ запрещен, то для этого параметра используется режим резервного копирования. Этот параметр уменьшает пропускную способность из-за создания контрольных точек.         |
|/efsraw     | Копирование всех зашифрованных файлов в режиме необработанных данных в файловой системе EFS. Используется только при работе с зашифрованными файлами.         |
|log+:<LogFile>| Добавление выходных данных в существующий файл журнала.|    
 
Ниже приведен пример выходных данных команды robocopy для копирования файлов в Data Box.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Чтобы оптимизировать производительность, при копировании данных используйте указанные ниже параметры robocopy.

|    платформа    |    Главным образом малые файлы (менее 512 КБ)                           |    Главным образом файлы среднего размера (от 512 КБ до 1 МБ)                      |    Главным образом большие файлы (более 1 МБ)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 сеанса Robocopy <br> 16 потоков на сеансы    |    3 сеанса Robocopy <br> 16 потоков на сеансы    |    2 сеанса Robocopy <br> 24 потока на сеансы    |  |


Дополнительные сведения о команде Robocopy см. в статье [Robocopy и несколько примеров](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Откройте папку назначения для просмотра и проверки скопированных файлов. Если в процессе копирования возникли ошибки, скачайте файл с ошибками для устранения неполадок.

Чтобы обеспечить целостность данных, при копировании данных система вычисляет их контрольные суммы. По завершении копирования проверьте использованное и свободное место на устройстве.
    
   ![Проверка свободного и использованного места на панели мониторинга](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>Копирование данных по протоколу NFS

Если у вас главный компьютер с ОС Linux, используйте программу копирования, аналогичную Robocopy. Вот некоторые программы, доступные в ОС Linux: [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/), [Ultracopier](https://ultracopier.first-world.info/).  

Команда `cp` — один из лучших способов копировать каталоги. Дополнительные сведения об использовании команды см. на [страницах руководства команды cp](http://man7.org/linux/man-pages/man1/cp.1.html).

При копировании при помощи rsync с использованием нескольких потоков следуйте указанным ниже рекомендациям.

 - Установите пакет **CIFS Utils** или **NFS Utils** (в зависимости от того, какая файловая система используется в клиенте с ОС Linux).

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

 -  Установите программу **Rsync** и **Parallel** (в зависимости от версии используемого дистрибутива ОС Linux).

    `sudo apt-get install rsync`
    `sudo apt-get install parallel` 

 - Создайте точку подключения.

    `sudo mkdir /mnt/databox`

 - Подключите том.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Сделайте зеркальное отражение структуры каталогов.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Скопируйте файлы. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     здесь j — количество случаев распараллеливания, а X — количество параллельных копий

     Рекомендуем начать работу с 16 параллельных копий и увеличить количество потоков в зависимости от доступных ресурсов.

## <a name="prepare-to-ship"></a>Подготовка к отправке

Последнее действие — подготовка устройства для отправки. При выполнении этого действия все общие папки устройства станут недоступными. После того как вы начнете подготовку устройства к отправке, общие папки станут недоступными.
1. Перейдите на страницу **Подготовка к отправке** и щелкните **Начать подготовку**. 
   
    ![Подготовка к отправке 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. По умолчанию контрольные суммы вычисляются в непосредственно во время подготовки к отправке. Вычисление контрольных сумм может занять некоторое время в зависимости от размера данных. Щелкните **Начать подготовку**.
    1. Когда начнется подготовка к отправке устройства, его общие папки станут недоступными, и оно будет заблокировано.
        
        ![Подготовка к отправке 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. По завершении подготовки состояние устройства изменится на *Готово к отправке*. 
        
        ![Подготовка к отправке 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Скачайте список файлов (манифест), скопированных при выполнении этого процесса. Этот список можно использовать позже для проверки отправленных файлов в Azure.
        
        ![Подготовка к отправке 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Завершите работу устройства. Перейдите на страницу **Завершение работы или перезапуск** и щелкните **Завершить работу**. Когда отобразится запрос на подтверждение, нажмите кнопку **ОК**.
4. Отключите кабели. Далее необходимо отправить устройство в корпорацию Майкрософт.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве были освещены следующие темы относительно Azure Data Box.

> [!div class="checklist"]
> * подключение к Data Box;
> * Копирование данных в Data Box
> * Подготовка Data Box к отправке

Сведения о том, как настраивать и копировать данные в Data Box, см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Отправка Azure Data Box в корпорацию Майкрософт](./data-box-deploy-picked-up.md)

