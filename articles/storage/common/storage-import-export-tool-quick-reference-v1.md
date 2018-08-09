---
title: Краткий справочник по командам для заданий импорта инструмента импорта и экспорта Azure версии 1 | Документация Майкрософт
description: Справочник по командам инструмента импорта и экспорта Azure, которые наиболее часто используются для заданий импорта. Приведенная информация относится к инструменту импорта и экспорта версии 1.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 16e0fa6f7336b39f63b00564d37c1be308bebb16
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526289"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Краткий справочник по часто используемым командам для заданий импорта
Этот раздел содержит краткий справочник по часто используемым командам. Дополнительные сведения об использовании см. в разделе [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Подготовка жесткого диска, когда данные уже скопированы на жесткий диск
 Следующая команда подготавливает жесткий диск, когда данные уже скопированы на него, но еще не зашифрованы с помощью BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Копирование отдельного каталога на жесткий диск  
 Следующая команда копирует отдельный исходный каталог на жесткий диск, который еще не зашифрован с помощью BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Копирование двух каталогов на жесткий диск  
 Чтобы скопировать два исходных каталога на диск, используйте следующие команды:  
  
 Первая команда указывает общие параметры, каталог журнала, ключ учетной записи хранения, букву целевого диска и требования `format/encrypt`:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Вторая команда указывает файл журнала, новый идентификатор сеанса и исходное и целевое расположения.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Копирование большого файла на жесткий диск во втором сеансе копирования  
 Следующая команда копирует отдельный большой файл на жесткий диск, который был подготовлен в предыдущем сеансе копирования:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Дополнительная информация

* [Пример рабочего процесса по подготовке жестких дисков для задания импорта](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
