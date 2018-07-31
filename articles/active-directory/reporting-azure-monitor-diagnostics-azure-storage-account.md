---
title: Руководство. Архивация журналов Azure Active Directory в учетной записи хранения Azure (предварительная версия) | Документация Майкрософт
description: Узнайте, как настроить систему диагностики Azure для отправки журналов Azure Active Directory в учетную запись хранения (предварительная версия)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240288"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Руководство. Архивация журналов Azure Active Directory в учетной записи хранения Azure (предварительная версия)

В этом руководстве вы узнаете, как настроить параметры диагностики Azure Monitor для передачи журналов Azure Active Directory в учетную запись хранения Azure.

## <a name="prerequisites"></a>Предварительные требования 

Вам необходимы:

* Подписка Azure с учетной записью хранения Azure. Если у вас нет подписки Azure, можно [зарегистрироваться и получить бесплатную пробную версию](https://azure.microsoft.com/free/).
* Клиент Azure Active Directory.
* Пользователь, который является глобальным администратором или администратором безопасности для этого клиента.

## <a name="archive-logs-to-an-azure-storage-account"></a>Архивация журналов в учетную запись хранения Azure

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Щелкните вкладку **Azure Active Directory** -> **Действия** -> **Журналы аудита**. 
3. Щелкните **Экспортировать параметры**, чтобы открыть колонку "Параметры диагностики". Выберите **Настройка параметра**, чтобы изменить существующие параметры, или **Добавить параметр диагностики**, чтобы добавить новый. Можно задать не более трех параметров. 
    ![Экспорт параметров](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Экспорт параметров")

4. Добавьте параметру понятное имя, чтобы помнить о его назначении. Например, «Отправить в учетную запись хранения Azure». 
5. Установите флажок **Архивировать в учетной записи хранения** и щелкните **Учетная запись хранения**, чтобы выбрать учетную запись хранения Azure. 
6. Выберите подписку и учетную запись хранения Azure, в которую необходимо направить журналы, и щелкните **ОК**, чтобы закрыть конфигурацию.
7. Установите флажок **AuditLogs**, чтобы отправить журналы аудита в учетную запись хранения. 
8. Установите флажок **SignInLogs**, чтобы отправить журналы входа в учетную запись хранения.
9. С помощью ползунка установите период хранения данных журнала. По умолчанию это значение равно "0", и журналы будут храниться в учетной записи хранения бессрочно. В противном случае можно задать значение, и события, старше указанного количества дней, будут автоматически очищены.
10. Щелкните **Сохранить**, чтобы сохранить параметры.
    ![Параметры диагностики](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Параметры диагностики")

11. Примерно через 15 минут убедитесь, что журналы переместились в учетную запись хранения. Перейдите на портал Azure, щелкните **Учетные записи хранения**, выберите учетную запись хранения, которая использовалась ранее, и щелкните **Большие двоичные объекты**. 
12. Чтобы просмотреть **журналы аудита**, щелкните **insights-log-audit** (аналитические сведения журнала аудита). Чтобы просмотреть **журналы входа**, щелкните **insights-logs-signin** (аналитические сведения журналов входа).
    ![Учетная запись хранения](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Учетная запись хранения")

## <a name="next-steps"></a>Дополнительная информация

* [Interpret audit logs schema in Azure monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md) (Интерпретация схемы журналов аудита в Azure Monitor)
* [Interpret sign-in logs schema in Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) (Интерпретация схемы журналов входа в Azure Monitor)
* [Frequently asked questions](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions) (Часто задаваемые вопросы и известные проблемы)