---
title: Устранение неполадок, связанных с отсутствием данных в журналах действий Azure Active Directory | Документация Майкрософт
description: В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в журналах действий.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 871dd3fda0ee5dc350a468f16e8f389ac3c71d34
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41917633"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Устранение неполадок, связанных с отсутствием данных в журналах действий Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Я не могу найти журналы аудита для последних действий на портале Azure

### <a name="symptoms"></a>Проблемы

Действия, выполненные на портале Azure, не отображаются в журнале аудита в колонке `Activity logs > Audit Logs`.

 ![Отчеты](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Причина:

Действия отображаются в журнале действий спустя некоторое время. В таблице ниже приводятся наши показатели задержки для журналов действий. 

| Отчет | &nbsp; | Задержка (P95) | Задержка (P99) |
|--------|--------|---------------|---------------|
| Аудит каталогов | &nbsp; | 2 мин | 5 мин |
| Действия при входе | &nbsp; | 2 мин | 5 мин | 

### <a name="resolution"></a>Способы устранения:

Подождите от 15 минут до двух часов и проверьте, появились ли действия в журнале. Если журналы не появились даже спустя два часа, [отправьте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) и мы рассмотрим вашу проблему.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Не удается найти последние действия входа пользователя в систему в журнале действий входа Azure Active Directory

### <a name="symptoms"></a>Проблемы

После входа на портал Azure я ожидал увидеть журналы входа для этих действий в колонке `Activity logs > Sign-ins`, но не могу их найти.

 ![Отчеты](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Причина:

Действия отображаются в журнале действий спустя некоторое время. В таблице ниже приводятся наши показатели задержки для журналов действий. 

| Отчет | &nbsp; | Задержка (P95) | Задержка (P99) |
|--------|--------|---------------|---------------|
| Аудит каталогов | &nbsp; | 2 мин | 5 мин |
| Действия при входе | &nbsp; | 2 мин | 5 мин | 

### <a name="resolution"></a>Способы устранения:

Подождите от 15 минут до двух часов и проверьте, появились ли действия в журнале. Если журналы не появились даже спустя два часа, [отправьте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) и мы рассмотрим вашу проблему.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Я не могу просмотреть на портале Azure данные отчета, полученные более чем за более 30 дней

### <a name="symptoms"></a>Проблемы

Я не могу просмотреть на портале Azure данные входа и аудита, полученные более чем за 30 дней. Почему? 

 ![Отчеты](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Причина:

В зависимости от типа лицензии Azure Active Directory сохраняет отчеты о действиях за такие периоды времени:

| Отчет           | &nbsp; |  Azure AD уровня "Бесплатный" | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Аудит каталога  | &nbsp; |   7 дней     | 30 дней             | 30 дней             |
| Действия при входе | &nbsp; | Недоступен. Доступ к данным собственного входа в систему можно получить в течение 7 дней в колонке профиля пользователя | 30 дней | 30 дней             |

Дополнительные сведения см. в статье [Политики хранения отчетов Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Способы устранения:

У вас есть два варианта сохранения данных дольше чем в течение 30 дней. Вы можете использовать [API отчетов Azure AD](concept-reporting-api.md), чтобы программным способом извлекать данные и хранить их в базе данных. Кроме того, вы можете интегрировать журналы аудита в стороннюю систему SIEM, такую как Splunk или SumoLogic.

## <a name="next-steps"></a>Дополнительная информация

* [Политики хранения отчетов Azure Active Directory](reference-reports-data-retention.md).
* [Задержки в отчетах Azure Active Directory](reference-reports-latencies.md).
* [Часто задаваемые вопросы об отчетах Azure Active Directory](reports-faq.md).

