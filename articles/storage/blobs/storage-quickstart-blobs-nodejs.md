---
title: Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Node.js — служба хранилища Azure
description: Создайте учетную запись хранения и контейнер в хранилище объектов (больших двоичных объектов). Затем используете клиентскую библиотеку службы хранилища для Node.js, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/20/2018
ms.author: cshoe
ms.openlocfilehash: 1c62dbd6856ec7bf2663f0b70a47357b52528899
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040818"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Node.js

Из этого краткого руководства вы узнаете, как использовать Node.js для передачи, скачивания и перечисления BLOB-объектов и как управлять контейнерами с помощью хранилища BLOB-объектов Azure.

Для работы с этим кратким руководством вам потребуется [подписка Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git), используемый в этом кратком руководстве, является простым консольным приложением Node.js. Чтобы начать работу, клонируйте репозиторий на компьютер, используя следующую команду.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Чтобы открыть приложение, найдите папку *storage-blobs-node-quickstart* и откройте ее в избранной среде редактирования кода.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

Перед запуском приложения укажите строку подключения для учетной записи хранения. Образец репозитория включает в себя файл с именем *.env.example*. Вы можете переименовать этот файл, удалив расширение *.example*, после чего в названии файла будет расширение *.env*. Внутри файла формата *ENV* добавьте значение строки подключения после ключа *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Установка необходимых пакетов

В каталоге приложения выполните команду *npm install*, чтобы установить необходимые пакеты для приложения.

```bash
npm install
```

## <a name="run-the-sample"></a>Запуск примера
После установки требуемых компонентов вы можете запустить пример, выполнив следующую команду:

```bash
npm start
```

Результат этого сценария будет аналогичен следующему:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Обратите внимание, если вы используете новую учетную запись хранения для этого краткого руководства — вы можете не видеть имена контейнеров в списке с меткой "*Containers*".

## <a name="understanding-the-code"></a>Основные сведения о коде
Первое выражение используется, чтобы загружать значения в переменные среды.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Модуль *dotenv* загружает переменные среды, если приложение запущено в локальной среде с целью отладки. Значения определяются в файле с именем *.env* и загружаются в текущий контекст выполнения. В производственных контекстах конфигурация сервера предоставляет эти значения, и именно поэтому этот код запускается только тогда, когда сценарий выполняется не в контексте "production".

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Ниже представлены сведения о предназначении каждого модуля. 

файл с именем *.env* в текущем контексте выполнения
- *path* требуется, чтобы определить абсолютный путь к файлу для передачи в хранилище BLOB-объектов.
- *azure-storage* — модуль [SDK службы хранилища Azure](https://docs.microsoft.com/javascript/api/azure-storage) для Node.js.

Затем переменная **blobService** инициализируется как новый экземпляр службы BLOB-объектов Azure.

```javascript
const blobService = storage.createBlobService();
```

В следующей реализации каждая из функций *blobService* упакована в *Promise*, что позволяет получить доступ к функции JavaScript *async* и оператору *await*, чтобы упростить характер обратного вызова [API службы хранилища Azure](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Если для каждой функции возвращается ответ об успешном выполнении, объект promise разрешает соответствующие данные вместе с сообщением, связанным с действием.

### <a name="list-containers"></a>Перечисление контейнеров

Функция *listContainers* вызывает [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listcontainerssegmented), которая возвращает коллекции контейнеров в группах.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Размер групп можно настроить с помощью [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Вызов *listContainersSegmented* возвращает метаданные большого двоичного объекта в виде массива экземпляров [ContainerResult](/nodejs/api/azure-storage/blobresult). Результаты возвращаются в 5000 пакетов увеличения (сегментов). Если в контейнере имеется более чем 5000 больших двоичных объектов, то результаты включают в себя значение для маркера *continuationToken*. Чтобы вывести список последующих сегментов из контейнера больших двоичных объектов, можно передать маркер продолжения обратно в *listContainersSegment* в качестве второго аргумента.

### <a name="create-a-container"></a>Создание контейнера

Функция *CreateContainer* вызывает команду [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists) и задает соответствующий уровень доступа для большого двоичного объекта.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Второй параметр (*options*) для **createContainerIfNotExists** принимает значение параметра [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists). Значение *blob* для *publicAccessLevel* указывает, что данные конкретного большого двоичного объекта доступны из общей сети. Этот параметр отличается от уровня доступа *контейнера*, который предоставляет возможность составить список содержимого контейнера.

Применение команды **createContainerIfNotExists** позволяет приложению выполнить команду *createContainer* несколько раз, не возвращая ошибки, если контейнер уже существует. В рабочей среде команда **createContainerIfNotExists** часто вызывается только один раз, так как в приложении используется один и тот же контейнер. В таких случаях контейнер можно создать заранее на портале или с помощью Azure CLI.

### <a name="upload-text"></a>Отправка текста

Функция *uploadString* вызывает [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromtext), чтобы записать (или перезаписать) произвольную строку в контейнер больших двоичных объектов.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Передача локального файла

Функция *uploadLocalFile* использует [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) для передачи и записи (или перезаписи) файла из файловой системы в хранилище BLOB-объектов. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Другие методы, доступные для передачи содержимого в большие двоичные объекты, включают в себя работу с [текстом](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) и [потоками данных](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream). Чтобы проверить, передан ли файл в хранилище BLOB-объектов, можно использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для просмотра данных в учетной записи.

### <a name="list-the-blobs"></a>Список BLOB-объектов

Функция *listBlobs* вызывает метод [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText), чтобы получить список метаданных большого двоичного объекта в контейнере. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Вызов *listBlobsSegmented* возвращает метаданные большого двоичного объекта в качестве массива экземпляров [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Результаты возвращаются в 5000 пакетов увеличения (сегментов). Если в контейнере имеется более чем 5000 больших двоичных объектов, то результаты включают в себя значение для маркера **continuationToken**. Чтобы вывести список последующих сегментов из контейнера больших двоичных объектов, можно передать маркер продолжения обратно в **listBlobSegmented** в качестве второго аргумента.

### <a name="download-a-blob"></a>Загрузка BLOB-объектов

Функция *downloadBlob* использует команду [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtotext), чтобы скачать содержимое большого двоичного объекта в заданный абсолютный путь файла.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Показанная здесь реализация изменяет источник, который возвращает содержимое большого двоичного объекта в виде строки. Также можно загружать большой двоичный объект как [поток данных](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtostream), а также напрямую в [локальный файл](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtolocalfile).

### <a name="delete-a-blob"></a>Удаление большого двоичного объекта

Функция *deleteBlob* вызывает функцию [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Как и предполагает ее имя, эта функция не возвращает ошибку, если большой двоичный объект уже удален.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Удаление контейнера

Контейнеры удаляются путем вызова метода *deleteContainer* службы BLOB-объектов и передачей в него имени контейнера.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Код вызова

Для поддержки синтаксиса JavaScript *async/await* весь код вызова упакован в функцию с именем *execute*. Затем execute вызывается и обрабатывается как обещание.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Весь следующий код выполняется внутри функции execute, где размещается комментарий `// commands`.

Сперва объявляются соответствующие переменные для назначения имен, примера содержимого и указания локального файла для передачи в хранилище BLOB-объектов.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Чтобы перечислить контейнеры в учетной записи хранения, вызывается функция listContainers, и возвращаемый список контейнеров регистрируется в окне вывода.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

После того как список контейнеров стал доступен, вы можете использовать метод массива *findIndex*, чтобы увидеть, существует ли контейнер, который вы хотите создать. Если контейнер не существует — он будет создан.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Затем строка и локальный файл передаются в хранилище BLOB-объектов.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Процесс перечисления больших двоичных объектов аналогичен процессу перечисления контейнеров. Вызов *listBlobs* возвращает массив больших двоичных объектов в контейнере и регистрируется в окне вывода.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Чтобы загрузить большой двоичный объект, для доступа к значению этого объекта захватывается и используется отклик. Из отклика readableStreamBody преобразуется в строку и выводится в окно вывода.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

В результате большой двоичный объект и контейнер удаляются из учетной записи хранения.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Все данные, записанные на учетную запись хранения, автоматически удаляются в конце примера кода. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Ресурсы для разработки приложений Node.js с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки Node.js с использованием хранилища BLOB-объектов.

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Просмотрите и установите [исходный код клиентской библиотеки Node.js](https://github.com/Azure/azure-storage-node) для службы хранилища Azure на сайте GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке Node.js см. в [справочнике по API-интерфейсу Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob), написанные с использованием клиентской библиотеки Node.js.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве показано, как передавать файл между локальным диском и хранилищем BLOB-объектов Azure с помощью Node.js. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем репозитории GitHub.

> [!div class="nextstepaction"]
> [Репозиторий пакетов SDK службы хранилища Azure для JavaScript](https://github.com/Azure/azure-storage-node)