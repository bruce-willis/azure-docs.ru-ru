---
title: Формат файла свойств и файла метаданных импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как указать метаданные и свойства для одного или нескольких больших двоичных объектов, входящих в задание импорта или экспорта.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526238"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Формат файла свойств и файла метаданных службы импорта и экспорта Azure
В рамках задания импорта или экспорта можно указать метаданные и свойства для одного или нескольких больших двоичных объектов. Чтобы задать метаданные и свойства для больших двоичных объектов, созданных в рамках задания импорта, на жестком диске необходимо создать файл метаданных или файл свойств, содержащий импортируемые данные. Для задания экспорта метаданные и свойства записываются в файл метаданных или файл свойств, содержащийся на жестком диске, возвращаемом пользователю.  
  
## <a name="metadata-file-format"></a>Формат файла метаданных  
Файл метаданных имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-элемент|type|ОПИСАНИЕ|  
|-----------------|----------|-----------------|  
|`Metadata`|Корневой элемент|Корневой элемент файла метаданных.|  
|`metadata-name`|Строка|Необязательный элемент. XML-элемент указывает имя метаданных большого двоичного объекта, а его значение определяет значение параметра метаданных.|  
  
## <a name="properties-file-format"></a>Формат файла свойств  
Файл свойств имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-элемент|type|ОПИСАНИЕ|  
|-----------------|----------|-----------------|  
|`Properties`|Корневой элемент|Корневой элемент файла свойств.|  
|`Last-Modified`|Строка|Необязательный элемент. Время последнего изменения большого двоичного объекта. Только для заданий экспорта.|  
|`Etag`|Строка|Необязательный элемент. Значение ETag большого двоичного объекта. Только для заданий экспорта.|  
|`Content-Length`|Строка|Необязательный элемент. Размер большого двоичного объекта в байтах. Только для заданий экспорта.|  
|`Content-Type`|Строка|Необязательный элемент. Тип содержимого большого двоичного объекта|  
|`Content-MD5`|Строка|Необязательный элемент. Хэш MD5 большого двоичного объекта.|  
|`Content-Encoding`|Строка|Необязательный элемент. Кодирование содержимого большого двоичного объекта.|  
|`Content-Language`|Строка|Необязательный элемент. Язык содержимого большого двоичного объекта.|  
|`Cache-Control`|Строка|Необязательный элемент. Строка управления кэшем для большого двоичного объекта.|  

## <a name="next-steps"></a>Дополнительная информация

Дополнительные правила настройки свойств и метаданных больших двоичных объектов см. в статьях [Set Blob Properties](/rest/api/storageservices/set-blob-properties) (Задание свойств большого двоичного объекта), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Задание метаданных большого двоичного объекта) и [Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Задание и получение свойств и метаданных для ресурсов больших двоичных объектов).
