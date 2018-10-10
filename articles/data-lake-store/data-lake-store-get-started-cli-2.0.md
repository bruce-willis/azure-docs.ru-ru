---
title: Начало работы с Azure Data Lake Storage 1-го поколения с помощью Azure CLI | Документация Майкрософт
description: Использование Azure CLI для создания учетной записи хранения Azure Data Lake Storage 1-го поколения и выполнения базовых операций
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: d8232b1e29a3d2585e79cf56d0f180a5084fd13e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979072"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Начало работы с Azure Data Lake Store с помощью Azure CLI
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [интерфейс командной строки Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Узнайте, как с помощью Azure CLI создать учетную запись Azure Data Lake Storage 1-го поколения и выполнять базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения об Azure Data Lake Storage 1-го поколения см. в статье [Общие сведения об Azure Data Lake Storage 1-го поколения](data-lake-store-overview.md).

Azure CLI — это интерфейс командной строки Azure для управления ресурсами Azure. Его можно использовать в Windows, Linux и macOS. Дополнительные сведения см. в статье [Инфраструктура Azure CLI](https://docs.microsoft.com/cli/azure). Полный список команд и синтаксис см. в [справочнике интерфейса командной строки Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/cli/azure/dls).


## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI**. См. инструкции в статье об [установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="authentication"></a>Authentication

В этой статье используется более простой подход к аутентификации в службе Data Lake Storage 1-го поколения, в которую выполняется вход от имени пользователя. Уровень доступа к учетной записи Data Lake Storage 1-го поколения и файловой системе зависит от уровня доступа пользователя, который вошел в систему. Существуют разные способы аутентификации в Data Lake Storage 1-го поколения, включая **аутентификацию пользователей** и **аутентификацию между службами**. Инструкции и дополнительные сведения об аутентификации см. в разделах [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md) и [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Вход в подписку Azure

1. Войдите в подписку Azure.

    ```azurecli
    az login
    ```

    Получите код для использования на следующем шаге. Откройте браузер и перейдите на страницу https://aka.ms/devicelogin. Введите код аутентификации. Вам будет предложено выполнить вход с использованием учетных данных.

2. После входа вы увидите список всех подписок Azure, связанных с вашей учетной записи. Чтобы выбрать определенную подписку, выполните следующую команду.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Создание учетной записи Azure Data Lake Storage 1-го поколения

1. Создайте новую группу ресурсов. В следующей команде укажите значения параметров, которые требуется использовать. Если имя расположения содержит пробелы, заключите его в кавычки. Например, "East US 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Создайте учетную запись Data Lake Storage 1-го поколения.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Создание папок в учетной записи Data Lake Storage 1-го поколения

Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи Azure Data Lake Storage 1-го поколения. Используйте следующую команду, чтобы создать папку с именем **mynewfolder** в корневом каталоге Data Lake Storage 1-го поколения.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Команда создаст папку, используя параметр `--folder`. Если этот параметр отсутствует, команда создаст пустой файл с именем mynewfolder в корневом каталоге учетной записи Data Lake Storage 1-го поколения.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Отправка данных в учетную запись Data Lake Storage 1-го поколения

Данные можно передавать в Data Lake Storage 1-го поколения непосредственно на корневой уровень или в папку, созданную в учетной записи. Фрагменты кода ниже показывают, как передать некоторые примеры данных в папку (**mynewfolder**), которая была создана в предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Загрузите файл и сохраните его в локальном каталоге на компьютере, например в расположении C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Укажите полный путь в качестве назначения, включая имя файла.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Вывод списка файлов в учетной записи Data Lake Storage 1-го поколения

Чтобы вывести список файлов в учетной записи Data Lake Storage 1-го поколения, используйте следующую команду.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Результат этой команды должен выглядеть примерно так:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Переименование, скачивание и удаление данных из учетной записи Data Lake Storage 1-го поколения 

* **Чтобы переименовать файл**, используйте следующую команду:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Чтобы загрузить файл**, используйте следующую команду. Убедитесь, что указанный конечный путь уже существует.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Команда создаст целевую папку, если она не существует.
    > 
    >

* **Чтобы удалить файл**, используйте следующую команду:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Если вы хотите удалить папку **mynewfolder** и файл **vehicle1_09142014_copy.csv** с помощью одной команды, используйте параметр --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Работа с разрешениями и списками управления доступом для учетной записи Azure Data Lake Storage 1-го поколения

Из этого раздела вы узнаете, как управлять списками ACL и разрешениями с помощью Azure CLI. Подробные сведения о реализации списков ACL в Azure Data Lake Storage 1-го поколения см. в статье [Контроль доступа в Azure Data Lake Storage 1-го поколения](data-lake-store-access-control.md).

* **Чтобы обновить владельца файла или папки**, используйте следующую команду:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Чтобы обновить разрешения для файла или папки**, используйте следующую команду:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Чтобы получить списки ACL для определенного пути**, используйте следующую команду:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Должен быть получен результат, аналогичный приведенному ниже:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Чтобы создать запись для ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Чтобы удалить запись для ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Чтобы удалить весь стандартный список ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Чтобы удалить весь настраиваемый список ACL**, используйте следующую команду:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Удаление учетной записи Data Lake Storage 1-го поколения
Чтобы удалить учетную запись Data Lake Storage 1-го поколения, используйте следующую команду.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

При появлении запроса введите **Y** , чтобы удалить учетную запись.

## <a name="next-steps"></a>Дополнительная информация
* [Использование Data Lake Storage Gen1 для обеспечения соответствия требованиям больших данных](data-lake-store-data-scenarios.md) 
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Использование Azure Data Lake Analytics с Azure Data Lake Storage 1-го поколения](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
