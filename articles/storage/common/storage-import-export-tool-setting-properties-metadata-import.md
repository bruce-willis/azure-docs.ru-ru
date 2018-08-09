---
title: Настройка свойств и метаданных с помощью службы импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как указать свойства и метаданные, используемые для настройки целевых больших двоичных объектов, при запуске инструмента импорта и экспорта Azure для подготовки дисков.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: d68de35591d1b17a62278a0bc4adf2b9ee20cf02
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525007"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Настройка свойств и метаданных во время импорта

При запуске инструмента импорта и экспорта Microsoft Azure для подготовки дисков вы можете указать свойства и метаданные, предназначенные для настройки целевых больших двоичных объектов. Выполните следующие действия.

1.  Чтобы задать свойства больших двоичных объектов, создайте текстовый файл на локальном компьютере, определяющий имена и значения свойств.
2.  Чтобы задать метаданные больших двоичных объектов, создайте текстовый файл на локальном компьютере, определяющий имена и значения метаданных.
3.  Передайте полный путь к одну или обоим файлам в инструмент импорта и экспорта Azure в ходе операции `PrepImport`.

> [!NOTE]
>  При указании файла свойств или метаданных в рамках сеанса копирования они задаются для каждого большого двоичного объекта, импортируемого в рамках этого сеанса копирования. Если вы хотите задать другой набор свойств или метаданных для определенных импортируемых больших двоичных объектов, необходимо создать отдельный сеанс копирования с другими файлами свойств и метаданных.

## <a name="specify-blob-properties-in-a-text-file"></a>Указание свойств большого двоичного объекта в текстовом файле

Чтобы задать свойства больших двоичных объектов, создайте локальный текстовый файл и добавьте элемент XML, определяющий имена свойств в качестве элементов, а значения свойств в качестве значений. Ниже приведен пример указания некоторых значений свойств.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Сохраните файл в локальном расположении, например `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Указание метаданных большого двоичного объекта в текстовом файле

Аналогичным образом, чтобы задать метаданные больших двоичных объектов, создайте локальный текстовый файл, определяющий имена метаданных в качестве элементов и значения метаданных в качестве значений. Ниже приведен пример указания некоторых значений метаданных.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Сохраните файл в локальном расположении, например `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Добавьте путь к файлам свойств и метаданных в dataset.csv.

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Дополнительная информация

* [Формат файла свойств и метаданных службы импорта и экспорта](../storage-import-export-file-format-metadata-and-properties.md)
