---
title: Начало работы с Azure Data Lake Storage Gen1 с помощью PowerShell | Документация Майкрософт
description: Использование Azure PowerShell для создания учетной записи Azure Data Lake Storage 1-го поколения и выполнения базовых операций
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 7a465559bd4e46777f67121e9b3c7d2b0b8a0a22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986342"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Начало работы с Azure Data Lake Storage Gen1 с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [интерфейс командной строки Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Узнайте, как с помощью Azure PowerShell создать учетную запись Azure Data Lake Storage 1-го поколения и выполнять базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения об Azure Data Lake Storage 1-го поколения см. в статье [Общие сведения об Azure Data Lake Storage 1-го поколения](data-lake-store-overview.md).

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 или более поздней версии**. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
В этой статье используется более простой подход к аутентификации в Data Lake Storage 1-го поколения, при котором нужно ввести учетные данные учетной записи Azure. Уровень доступа к учетной записи Data Lake Storage 1-го поколения и файловой системе зависит от уровня доступа пользователя, который вошел в систему. Существуют разные способы аутентификации в Data Lake Storage 1-го поколения, включая **аутентификацию пользователей** и **аутентификацию между службами**. Инструкции и дополнительные сведения об аутентификации см. в разделах [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md) и [Аутентификация между службами](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Создание учетной записи Data Lake Storage 1-го поколения
1. На рабочем столе откройте новое окно Windows PowerShell. Введите следующий фрагмент кода, чтобы войти в свою учетную запись Azure, выбрать подписку и зарегистрировать поставщик Data Lake Storage 1-го поколения. Когда вам будет предложено войти, введите учетные данные администратора или владельца подписки:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Учетная запись Data Lake Storage 1-го поколения связывается с группой ресурсов Azure. Для начала создайте группу ресурсов Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Создание группы ресурсов Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Создание группы ресурсов Azure")
3. Создайте учетную запись Data Lake Storage 1-го поколения. Указанное имя должно содержать только строчные буквы и цифры.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Создание учетной записи Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Создание учетной записи Data Lake Storage 1-го поколения")
4. Убедитесь, что учетная запись создана.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Результат должен иметь значение **True**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Создание структуры каталогов в учетной записи Data Lake Storage 1-го поколения
Чтобы хранить данные и управлять ими, вы можете создать каталоги в своей учетной записи Data Lake Storage 1-го поколения.

1. Укажите корневой каталог.

        $myrootdir = "/"
2. Создайте новый каталог с именем **mynewdirectory** в указанном корневом каталоге.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Убедитесь, что новый каталог создан.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Отобразятся выходные данные, как показано на следующем снимке экрана:

    ![Проверка каталога](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Проверка каталога")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Отправка данных в учетную запись Data Lake Storage 1-го поколения
Данные можно передавать в Data Lake Storage 1-го поколения непосредственно на корневой уровень или в каталог, созданный в учетной записи. Фрагменты кода в этом разделе показывают, как отправить некоторые образцы данных в каталог (**mynewdirectory**), который был создан на предыдущем шаге.

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Загрузите файл и сохраните его в локальном каталоге на компьютере, например в расположении C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Переименование, скачивание и удаление данных из учетной записи Data Lake Storage 1-го поколения
Чтобы переименовать файл, используйте следующую команду:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Чтобы загрузить файл, используйте следующую команду:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Чтобы удалить файл, используйте следующую команду:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

При появлении запроса введите **Y** , чтобы удалить элемент. Если нужно удалить несколько файлов, можно указать все пути через запятую.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Удаление учетной записи Data Lake Storage 1-го поколения
Чтобы удалить учетную запись Data Lake Storage 1-го поколения, используйте следующую команду.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

При появлении запроса введите **Y** , чтобы удалить учетную запись.

## <a name="next-steps"></a>Дополнительная информация
* [Рекомендации по настройке производительности для использования PowerShell с Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Использование Data Lake Storage Gen1 для обеспечения соответствия требованиям больших данных](data-lake-store-data-scenarios.md) 
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Использование Azure Data Lake Analytics с Azure Data Lake Storage 1-го поколения](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
