---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью пакета SDK службы хранилища для Java версии 10 | Документация Майкрософт
description: В этом кратком руководстве вы создаете контейнер в хранилище объектов (большой двоичный объект), отправляете файл, список объектов и скачиваете с помощью пакета SDK службы хранилища для Java.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704476"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>Краткое руководство. Отправка, скачивание и отображение больших двоичных объектов с помощью пакета SDK для Java версии 10 (предварительная версия)

Из этого краткого руководства вы узнаете, как передать, скачать и создать список блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure с помощью нового пакета SDK службы хранилища для Java. Новый пакет SDK службы хранилища для Java использует модель реактивного программирования RxJava путем обеспечения асинхронных операций. Дополнительные сведения о RxJava см. [здесь](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее:

* Установите и настройте [Maven](http://maven.apache.org/download.cgi) для работы из командной строки или любую интегрированную среду разработки Java (на свой выбор).
* Установите и настройте [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart), используемый в этом кратком руководстве, является простым консольным приложением. 

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git.

После завершения импорта проекта откройте **Quickstart.java** (в **src/main/java/quickstart**).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища
Этому решению требуется, чтобы имя и ключ учетной записи хранения безопасно хранились в переменных среды локального компьютера, на котором выполняется пример. Чтобы создать переменную среды, выполните один из приведенных ниже примеров в зависимости от операционной системы.

### <a name="for-linux"></a>Для Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>Для Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Запуск примера

В этом примере создается тестовый файл в каталоге по умолчанию (AppData\Local\Temp, для пользователей Windows), затем предлагается ввести команды для передачи тестового файла в хранилище BLOB-объектов, после чего выводится список больших двоичных объектов в контейнере, а затем файл скачивается с новым именем, чтобы можно было сравнить старый и новый файлы. 

Если вы хотите запустить пример, используя Maven в командной строке, откройте оболочку и перейдите в папку **storage-blobs-java-v10-quickstart** в клонированном каталоге. Затем введите `mvn compile exec:java`.

Ниже приведен пример выходных данных для запуска приложения в Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

У вас есть контроль над образцом, поэтому введите команды для выполнения кода. Имейте в виду, что входные данные учитывают регистр.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

После проверки файлов нажмите клавишу **ВВОД**, чтобы завершить работу демонстрационной версии и удалить тестовые файлы. Теперь вы знаете, что делает этот пример. Давайте откроем файл **Quickstart.java** и изучим его код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга — каждый объект используется следующим в списке объектом.

* Создайте экземпляр объекта StorageURL, указывающий на учетную запись хранения.

    Объект [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) является представлением вашей учетной записи хранения и позволяет создать конвейер. Конвейер ([конвейер HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)) — это набор политик, которые используются для манипулирования запросами и ответами с помощью механизмов авторизации, ведения журналов и повторов. С конвейером вы можете создать экземпляр объекта [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview), который позволяет создать экземпляр [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), необходимый для запуска операций в контейнерах больших двоичных объектов.

* Создайте экземпляр объекта ContainerURL, представляющий контейнер, к которому осуществляется доступ. Контейнеры используются для организации BLOB-объектов аналогично папкам для упорядочения файлов на компьютере.

    **ContainerURL** предоставляет точку доступа к службе контейнеров. С помощью **ContainerURL** можно создать экземпляр объекта [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), который необходим для создания большого двоичного объекта.

* Создайте экземпляр объекта [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview), который указывает на конкретный интересующий вас большой двоичный объект.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

### <a name="create-a-container"></a>Создание контейнера 

В этом разделе вы создадите экземпляр объекта ContainerURL и контейнер с ним. В примере контейнер называется **quickstartblobs**. 

В этом примере используется [CreateIfNotExists](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview), так как при каждом запуске примера требуется создавать новый контейнер. Вы можете также создать контейнер заранее, чтобы не создавать его в коде.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. Именно этот тип представлен в этом кратком руководстве. 

Чтобы отправить файл в BLOB-объект, получите ссылку на BLOB-объект в целевом контейнере. Получив ссылку на двоичный объект, вы можете передать файл с помощью либо низкоуровневых API, например [Отправка](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) — PutBlob, [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) — называемые также PutBLock, в экземпляре BlockBlobURL или высокоуровневых API, предоставляемых в классе [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview), например, в методе [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview). Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует.

Пример кода создает локальный файл для отправки и скачивания. Файл для отправки хранится как **sourceFile**, а URL-адрес большого двоичного объекта — как **blob**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Блочный BLOB-объект может представлять собой текстовый или двоичный файл любого типа. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска, применяемых для поддержки виртуальных машин IaaS. Большие двоичные объекты добавления предназначены для добавления данных в конец. Оно часто используется для ведения журнала. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Вы можете получить список объектов в контейнере с помощью [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview). Этот метод возвращает до 5000 объектов одновременно с маркером продолжения (следующий маркер), если в контейнере есть дополнительные объекты. Для этого мы создаем вспомогательную функцию, которая вызывает себя саму несколько раз, пока в предыдущем ответе listBlobsFlatSegment не появляется следующий маркер.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Чтобы скачать большие двоичные объекты на локальный диск, воспользуйтесь методом [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview).

Следующий код скачивает BLOB-объект, отправленный в предыдущем разделе, добавляя к имени BLOB-объекта суффикс "_DOWNLOADED", чтобы вы увидели оба файла на локальном диске. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны большие двоичные объекты, отправленные при работе с этим руководством, удалите весь контейнер с помощью метода [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview). Он также удаляет файлы в контейнере.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Java. 

> [!div class="nextstepaction"]
> [Хранилища пакета SDK, версия 10 для исходного кода Java ](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [Client](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview) (Клиент) 
> [Примеры для службы хранилища Azure с использованием Java](https://github.com/ReactiveX/RxJava)