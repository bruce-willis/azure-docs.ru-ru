---
title: Использование хранилищ данных в службе машинного обучения Azure для доступа к данным
description: Использование хранилищ данных для получения доступа к хранилищу данных во время обучения
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990813"
---
# <a name="how-to-access-data-during-training"></a>Получение доступа к данным во время обучения
В службах машинного обучения Azure хранилище данных — это абстракция над [службой хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). Хранилище данных может ссылаться на контейнер [больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) или [общую папку Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) в качестве базового хранилища. Хранилища данных обеспечивают удобный доступ к хранилищу данных и взаимодействие с ним в рамках рабочих процессов машинного обучения Azure с помощью пакета SDK для Python или CLI.

## <a name="create-a-datastore"></a>Создание хранилища данных
Для использования хранилищ данных необходима [рабочая область](concept-azure-machine-learning-architecture.md#workspace). Вы можете создать новую рабочую область или получить существующую:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Использование хранилища данных по умолчанию
Каждой рабочей области назначено хранилище данных по умолчанию, которое можно начать использовать немедленно, что избавляет вас от необходимости создавать и настраивать собственные учетные записи хранения.

Чтобы получить хранилище данных рабочей области по умолчанию:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Регистрация хранилища данных
Если вас уже есть существующее хранилище Azure, его можно зарегистрировать как хранилище данных для рабочей области. Служба машинного обучения Azure предоставляет возможность регистрации контейнера больших двоичных объектов Azure или общей папки Azure в качестве хранилища данных. Все методы регистрации находятся в классе `Datastore` и имеют форму `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Хранилище данных "Контейнер BLOB-объектов Azure"
Чтобы зарегистрировать контейнер больших двоичных объектов Azure в качестве хранилища данных:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Хранилище данных "Общая папка Azure"
Чтобы зарегистрировать общую папку Azure в качестве хранилища данных:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Получение существующего хранилища данных
Чтобы запросить зарегистрированное хранилище данных по имени:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Также можно получить все хранилища данных для рабочей области:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)"
```

Если вы хотите задать одно из зарегистрированных хранилищ данных в качестве хранилище данных по умолчанию для рабочей области, это можно сделать следующим образом:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Отправка и загрузка данных
### <a name="upload"></a>Передать
Каталог или отдельные файлы можно отправить в хранилище данных с помощью пакета SDK для Python.

Чтобы отправить каталог в хранилище данных `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` указывает расположение в общей папке (или контейнере больших двоичных объектов) для отправки. По умолчанию используется значение `None`: в этом случае данные отправляются в корневой каталог. `overwrite=True` выполняет перезапись всех существующих данных в `target_path`.

Кроме того, можно отправить список отдельных файлов в хранилище данных с помощью метода хранилища данных `upload_files()`.

### <a name="download"></a>Загрузка
Аналогичным образом можно скачать данные из хранилища данных в локальную файловую систему.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` — это расположение локального каталога для загрузки данных. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет загружено все содержимое общей папки (или контейнера больших двоичных объектов).

## <a name="access-datastores-for-training"></a>Доступ к хранилищам данных для обучения
Для получения доступа к хранилищу данных во время учебного запуска (например, для обучения или проверки данных) на целевом объекте удаленных вычислений используется пакет SDK для Python. 

Существует два способа сделать хранилище данных доступным для объекта удаленных вычислений:
* **Подключение**  
`ds.as_mount()`: при выборе этого режима подключения хранилище данных будет подключено к объекту удаленных вычислений. 
* **Загрузка или отправка**  
    * `ds.as_download(path_on_compute='your path on compute')`: этот режим загрузки позволяет загружать данные из вашего хранилища данных в объект удаленных вычислений в расположении, заданном параметром `path_on_compute`.
    * И наоборот, можно отправлять данные, созданные в ходе учебного запуска, в хранилище данных. Например, если скрипт обучения создает файл `foo.pkl` в текущем рабочем каталоге в объекте удаленных вычислений, можно задать его отправку в хранилище данных после выполнения скрипта: `ds.as_upload(path_on_compute='./foo.pkl')`. Это приведет к отправке файла в корневой каталог хранилища данных.
    
Если вы хотите сослаться на определенную папку или файл в хранилище данных, можно использовать функцию хранилища данных **`path`**. Например, если в хранилище данных имеется каталог с относительным путем `./bar`, и вам нужно просто скачать содержимое этой папки в целевой объект вычислений, это можно сделать следующим образом: `ds.path('./bar').as_download()`

Любой объект `ds` или `ds.path` разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"`, значение которой представляет путь подключения или загрузки в объекте удаленных вычислений. Путь к хранилищу данных в объекте удаленных вычислений не может совпадать с путем выполнения для скрипта.

Чтобы получить доступ к хранилищу данных во время обучения, его можно передать в скрипт обучения как аргумент командной строки с помощью `script_params`:

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` — режим по умолчанию для хранилища данных, поэтому можно просто передать `ds` в аргумент `'--data_dir'` напрямую.

Кроме того, можно передать список хранилищ данных в параметр `inputs` конструктора средства оценки, чтобы выполнить подключение или копирование в хранилище данных или из него:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Приведенный выше код выполняет следующие действия:
* загружает все содержимое в хранилище данных `ds1` в объект удаленных вычислений перед запуском скрипта обучения `train.py`;
* загружает папку `'./foo'` в хранилище данных `ds2` в объект удаленных вычислений перед запуском `train.py`;
* отправляет файл `'./bar.pkl'` из объекта удаленных вычислений в хранилище данных `d3` после выполнения скрипта.

## <a name="next-steps"></a>Дополнительная информация
* [Обучение модели](how-to-train-ml-models.md)
