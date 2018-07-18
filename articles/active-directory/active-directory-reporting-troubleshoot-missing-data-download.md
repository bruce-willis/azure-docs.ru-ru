---
title: Устранение неполадок, связанных с отсутствием данных в скачанных журналах действий Azure Active Directory | Документация Майкрософт
description: В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в скачанных журналах действий.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232231"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Не удается найти данные в скачанном журнале действий Azure Active Directory


## <a name="symptoms"></a>Проблемы

В скачанном журнале действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Причина:

К журналам действий, скачиваемым на портале Azure, применяется ограничение в 5000 последних записей, отсортированных в хронологическом порядке (начиная с самых новых). 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](active-directory-reporting-api-getting-started.md), чтобы извлечь до миллиона записей. Рекомендуем настроить расписание выполнения скрипта, который вызывает интерфейсы API отчетов для получения данных за определенный период (например, ежедневно или еженедельно).

## <a name="next-steps"></a>Дополнительная информация
См. сведения в статье [Часто задаваемые вопросы об отчетах Azure Active Directory](active-directory-reporting-faq.md).

