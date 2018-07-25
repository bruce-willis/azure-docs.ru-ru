---
title: Системные требования для диска Microsoft Azure Data Box | Документация Майкрософт
description: Дополнительные сведения о требованиях к программному обеспечению и сети для диска Microsoft Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
ms.openlocfilehash: 7138fa70c8b5615ad84196703f3bd76009ba5811
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011492"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Системные требования для диска Azure Data Box (предварительная версия)

В этой статье описываются важные требования к системе и рекомендации для решения на основе диска Microsoft Azure Data Box и клиентов, подключающихся к нему. Прежде чем развертывать диск Data Box, внимательно ознакомьтесь с приведенной ниже информацией и по мере необходимости возвращайтесь к ней во время развертывания и последующих действий.

> [!IMPORTANT]
> Диск Data Box предоставляется в режиме предварительной версии. Изучите [условия использования для предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), прежде чем развертывать это решение. 

В системных требованиях перечислены поддерживаемые платформы для клиентов, подключающихся к дискам, поддерживаемые типы учетных записей хранения и хранилищ.


## <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

Ниже приведен список поддерживаемых операционных систем для операций разблокировки диска и копирования данных при помощи клиентов, подключенных к диску Data Box.

| **Операционная система и (или) платформа** | **Версии** |
| --- | --- |
| Windows Server |2008 R2 с пакетом обновления 1 (SP1) <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4,0 |
| .NET Framework |4.5. |

> [!NOTE] 
> На клиентах, которые выполняют средство разблокировки диска и используются для копирования данных, должен быть включен BitLocker.


## <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

Ниже приведен список поддерживаемых типов хранилищ для диска Data Box.

| **Учетная запись хранения** | **Примечания** |
| --- | --- |
| Классический | Стандартная |
| Общего назначения  |"Стандартный" (поддерживаются версии V1 и V2). Поддерживаются "горячий" и "холодный" уровни. |


## <a name="supported-storage-types"></a>Поддерживаемые типы хранилищ

Ниже приведен список поддерживаемых типов хранилищ для диска Data Box.

| **Формат файлов** | **Примечания** |
| --- | --- |
| Блочный BLOB-объект Azure | |
| Страничный BLOB-объект Azure  | |


## <a name="next-step"></a>Дальнейшие действия

* [Deploy your Azure Data Box Disk](data-box-disk-deploy-ordered.md) (Развертывание диска Azure Data Box)

