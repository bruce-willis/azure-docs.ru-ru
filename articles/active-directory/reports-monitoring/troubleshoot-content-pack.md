---
title: Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory | Документация Майкрософт
description: Содержит список сообщений об ошибках пакета содержимого действий Azure Active Directory и инструкции по их исправлению.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782181"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory 

|  |
|--|
|В настоящее время пакет содержимого Azure AD Power BI использует API-интерфейсы Azure AD Graph для получения данных от вашего клиента Azure AD. Таким образом, можно заметить некоторые несоответствия между данными, доступными в пакете содержимого и данными, полученными с помощью [API-интерфейсов Microsoft Graph для создания отчетов](concept-reporting-api.md). |
|  |

При работе с пакетом содержимого Power BI для предварительной версии Azure Active Directory могут произойти приведенные ниже ошибки. 

- [Сбой обновления](troubleshoot-content-pack.md#refresh-failed) 
- [Не удалось обновить учетные данные источников данных](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importing of data is taking too long](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) (Импорт данных занимает слишком много времени) 
 
Данная статья содержит сведения о возможных причинах и способах устранения этих ошибок.
 
## <a name="refresh-failed"></a>"Сбой обновления" 
 
**Как отображается эта ошибка**: электронное сообщение от Power BI или состояние ошибки в журнале обновлений. 


| Причина: | Как устранить |
| ---   | ---        |
| Сбои обновления могут возникнуть, если учетные данные пользователей, подключающихся к пакету содержимого, были сброшены, но не обновлены в параметрах подключения пакета содержимого. | В Power BI найдите набор данных, соответствующий панели мониторинга журналов действий Azure Active Directory (журналы действий Azure Active Directory), выберите "Запланировать обновление" и введите учетные данные Azure AD. |
| Обновление может завершиться сбоем из-за проблем с данными в базовом пакете содержимого. | Отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>"Не удалось обновить учетные данные источников данных" 
 
**Как отображается эта ошибка**: в Power BI, при подключении к пакету содержимого журналов действий Azure Active Directory (предварительная версия). 

| Причина: | Как устранить |
| ---   | ---        |
| Подключающийся пользователь не является глобальным администратором, читателем данных безопасности или администратором безопасности. | Используйте учетную запись глобального администратора, читателя безопасности или администратора безопасности для доступа к пакетам содержимого. |
| Клиент не имеет лицензии Premium или по крайней мере одного пользователя с файлом лицензии Premium. | Отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>"Importing of data is taking too long" (Импорт данных занимает слишком много времени) 
 
**Как отображается эта ошибка**: в Power BI после подключения к пакету содержимого процесс импорта данных приступает к подготовке панели мониторинга для журнала действий Azure Active Directory. Отображается сообщение: *Importing of data…* (Импорт данных…)  

| Причина: | Как устранить |
| ---   | ---        |
| В зависимости от размера клиента шаг может длиться от нескольких минут до получаса. | Просто подождите. Если в течение часа сообщение не исчезнет и не отобразится панель мониторинга, отправьте запрос в службу поддержки. Дополнительные сведения см. в разделе [Как получить поддержку для Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Дополнительная информация

Чтобы установить пакет содержимого Power BI для предварительной версии Azure Active Directory, щелкните [здесь](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


