---
title: Использование URI в Azure Data Lake Storage Gen2 (предварительная версия)
description: Использование URI в Azure Data Lake Storage Gen2 (предварительная версия)
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a6130d8440b16e5a72c939fc07f6bf32c0946418
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114298"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Использование URI в Azure Data Lake Storage Gen2

Драйвер [файловой системы Hadoop](http://www.aosabook.org/en/hdfs.html), совместимый с Azure Data Lake Storage Gen2 (предварительная версия), обозначается идентификатором схемы `abfs` (Azure Blob File System — файловая система BLOB-объектов Azure). Драйвер ABFS совместим с другими драйверами файловой системы Hadoop и использует формат URI для обращения к файлами и каталогам в учетных записях, поддерживающих Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Синтаксис URI

Синтаксис URI для Data Lake Storage Gen2 зависит от того, настроена ли служба Data Lake Storage Gen2 в качестве файловой системы по умолчанию в вашей учетной записи хранения.

Если вы хотите обратиться к учетной записи, которая поддерживает Data Lake Storage 2-го поколения и при создании которой эта файловая система **не** была настроена как файловая система по умолчанию, используется сокращенный URI со следующим синтаксисом:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Scheme identifier** (Идентификатор схемы) — указывает протокол `abfs`. Вы можете использовать или не использовать подключение SSL. Выберите `abfss`, чтобы подключаться по протоколу SSL.

2. **File system** (Файловая система) — указывает на родительское расположение, в котором хранятся все файлы и папки. Этот параметр аналогичен контейнерам в службе Azure Storage Blob.

3. **Account name** (Имя учетной записи) — содержит имя, которое вы присвоили учетной записи хранения при ее создании.

4. **Paths** (Пути) — содержит представление структуры каталогов, в котором сегменты разделяются косыми чертами (`/`).

5. **File Name** (Имя файла) — содержит имя конкретного файла. Этот параметр можно не указывать, если вы обращаетесь к каталогу.

Если при создании учетной записи, к которой нужно обратиться, эта файловая система была настроена как файловая система по умолчанию, используется сокращенный URI со следующим синтаксисом:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Path** (Путь) — содержит представление структуры каталогов, в котором сегменты разделяются косыми чертами (`/`).

2. **File Name** (Имя файла) — содержит имя конкретного файла.


## <a name="next-steps"></a>Дополнительная информация

- [Использование Azure Data Lake Storage Gen2 с кластерами Azure HDInsight](use-hdi-cluster.md)