---
title: Краткий справочник по командам для заданий импорта инструмента импорта и экспорта Azure | Документация Майкрософт
description: Справочник по командам инструмента импорта и экспорта Azure, которые наиболее часто используются для заданий импорта.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 8f3eb2bf2d9789b678849f9e2415816d15afc29e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526636"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Краткий справочник по часто используемым командам для заданий импорта

В этой статье приводится краткая справочная информация о некоторых часто используемых командах. Дополнительные сведения об использовании см. в разделе [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Первый сеанс

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Второй сеанс

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Прерывание последнего сеанса

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Возобновление последнего прерванного сеанса

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Добавление дисков к последнему сеансу

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Дополнительная информация

* [Пример рабочего процесса по подготовке жестких дисков для задания импорта](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
