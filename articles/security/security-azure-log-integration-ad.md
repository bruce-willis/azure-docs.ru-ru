---
title: Интеграция журналов Azure с журналами аудита Azure Active Directory | Документация Майкрософт
description: Узнайте, как установить службу интеграции журналов Azure и интегрировать журналы аудита Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 07299b960747528dde8dcefc70055442c2150486
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235997"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Интеграция журналов аудита Azure Active Directory

События аудита Azure Active Directory (Azure AD) помогают определить привилегированные действия, выполняемые в Azure Active Directory. Просмотреть типы событий, доступных для отслеживания, можно с помощью [событий отчета об аудите Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).


>[!IMPORTANT]
> Использование службы интеграции журналов Azure будет прекращено до 01.06.2019. Скачивания AzLog будут отключены до 27 июня 2018 г. Сведения о том, что делать дальше, см. в [этой записи блога](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Интеграция журналов аудита Azure Active Directory

> [!NOTE]
> Прежде чем приступить к действиям из этой статьи, обязательно изучите статью [Интеграция журналов данных Azure с ведением журнала системы диагностики Azure и пересылкой событий Windows](security-azure-log-integration-get-started.md) и выполните соответствующие инструкции.

1. Откройте командную строку и выполните следующую команду:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Выполните следующую команду: 
 
   ``azlog createazureid``

   Эта команда запрашивает имя для входа Azure. Затем она создает субъект-службу Azure Active Directory в клиентах Azure AD, содержащих подписки Azure, для которых вошедший пользователь является администратором, соадминистратором или владельцем. Команда завершится ошибкой, если пользователь является только гостем в клиенте Azure AD. Проверка подлинности в Azure осуществляется через Azure AD. Создание субъекта-службы для службы интеграции Azure приводит к созданию удостоверения Azure AD, которому предоставлен доступ на чтение из подписок Azure.

3. Выполните следующую команду, чтобы указать идентификатор клиента. Для выполнения команды необходимо быть членом роли администратора клиента.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Пример:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Проверьте следующие папки, в которых должны создаваться JSON-файлы журнала аудита Azure Active Directory:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

В следующем видео показаны шаги, описанные в данной статье:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> За конкретными инструкциями по переносу данных JSON-файлов в систему управления сведениями о безопасности и событиями (SIEM) обратитесь к поставщику SIEM.

Помощь сообщества можно получить на [форуме MSDN по интеграции журналов Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). С помощью этого форума участники сообщества по интеграции журналов Azure могут помогать друг другу, делясь вопросами, ответами, советами и рекомендациями. Кроме того, команда разработчиков службы интеграции журналов Azure отслеживает этот форум и помогает при любой возможности.

Можно также отправить [запрос в службу поддержки](../azure-supportability/how-to-create-azure-support-request.md). Выберите **Интеграция с журналом** в качестве службы, в которую необходимо отправить запрос поддержки.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше об интеграции журналов Azure, ознакомьтесь со следующей информацией:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Служба интеграции журналов Microsoft Azure). На этой странице Центра загрузки можно получить дополнительные сведения, изучить требования к системе и получить инструкции по установке службы интеграции журналов Azure.
* [Введение в службу интеграции журналов Microsoft Azure ](security-azure-log-integration-overview.md). В этой статье рассказывается о службе интеграции журналов Azure, ее основных возможностях и принципах работы.
* [Интеграция журналов Azure: часто задаваемые вопросы](security-azure-log-integration-faq.md). В этой статье содержатся ответы на часто задаваемые вопросы об интеграции журналов Azure.
* [New features for Azure diagnostics and Azure Audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Новые возможности системы диагностики Azure и журналов аудита Azure). В этой записи блога рассказывается о журналах аудита Azure и других функциях, помогающих лучше понять, как работают ваши ресурсы Azure.
