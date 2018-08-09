---
title: Обновление пакета содержимого централизованных отчетов службы Azure Backup
description: Сведения об обновлениях к пакету содержимого службы Azure Backup в Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267178"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Обновление пакета содержимого централизованных отчетов службы Azure Backup 

[Пакет содержимого службы Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) можно использовать для централизованного просмотра отчетов о резервном копировании. Пакет содержимого регулярно обновляется для добавления дополнительных функций, а также для исправления ошибок. В этой статье описываются действия по обновлению пакета содержимого, при задержке обновления и для обновлений, выполненных с течением времени.

## <a name="how-to-get-updates-to-the-content-pack"></a>Получение обновлений к пакету содержимого

### <a name="to-get-the-updated-content-pack"></a>Сведения об обновлении пакета содержимого
Если не были внесены изменения, то копия пакета содержимого будет обновляться автоматически. Если пакет содержимого был изменен, в Power BI и по электронной почте придут соответствующие уведомления. Обновленный пакет содержимого можно получить удобным способом. 

### <a name="to-delay-the-update"></a>Сведения о задержке обновления
Рекомендуется импортировать пакет содержимого в [настраиваемую рабочую область](https://youtu.be/26zyOtyHPJM?t=1m57s). Теперь имеется возможность редактировать отчеты.
Как упоминалось выше, при изменении пакета содержимого появится уведомление в Power BI. Можно выбрать вариант с задержкой получения пакета содержимого. 

## <a name="coming-soon"></a>Скоро
   
Пакет содержимого службы Azure Backup обновляется для поддержки большего количества рабочих нагрузок, таких как SQL в резервном копировании виртуальных машин IaaS и SC DPM, в дополнение к текущей поддержке MAB и резервного копирования виртуальных машин службы Azure. Это значит, что скоро появится возможность просматривать и анализировать данные резервных копий в одном централизованном месте. [Отчеты также можно настроить](https://youtu.be/26zyOtyHPJM) в соответствии с потребностями организации.

Чтобы сделать отчеты более значимыми для рабочих нагрузок, был изменен набор отчетов, предварительно настроенный с помощью пакета содержимого службы Azure Backup. Краткий обзор предстоящего набора отчетов доступен ниже.

### <a name="summary"></a>Сводка
   
![Сводка](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Выставление счетов

![Выставление счетов](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Соответствие нормативным требованиям

![Соответствие нормативным требованиям](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Хранилище

![Хранилище](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Резервные элементы
![Резервные элементы](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Оповещения

![Оповещения](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Задания

![Задания](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Дальнейшие действия

* Видеоролик [How to share reports using email | Azure Backup Tips and Tricks](https://youtu.be/26zyOtyHPJM) (Как обмениваться отчетами по электронной почте | Советы и хитрости службы Azure Backup)
* [Часто задаваемые вопросы о службе архивации Azure](backup-azure-backup-faq.md)
