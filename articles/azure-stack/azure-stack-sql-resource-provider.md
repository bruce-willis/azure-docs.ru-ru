---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307831"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Использование баз данных SQL в Microsoft Azure Stack

API адаптера поставщика ресурсов SQL Server используется для предоставления баз данных SQL в качестве службы [Azure Stack](azure-stack-poc.md). Когда вы установите поставщик ресурсов и подключите его к одному или нескольким экземплярам SQL Server, вы и ваши пользователи сможете создавать:

- базы данных для приложений, созданных для облака;
- Веб-сайты, использующие SQL.
- Рабочие нагрузки, использующие SQL.

Поставщик ресурсов поддерживает не все возможности [Базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/) по управлению базами данных. Например, не поддерживаются эластичные пулы, которые выделяют ресурсы автоматически. Но поставщик ресурсов поддерживает аналогичные операции создания, чтения, обновления и удаления (CRUD) для базы данных SQL Server. Дополнительные сведения об API поставщика ресурсов см. в разделе [Windows Azure Pack SQL Server Resource Provider REST API Reference](https://msdn.microsoft.com/library/dn528529.aspx) (справочник по REST API поставщика ресурсов SQL Server для Windows Azure Pack).

>[!NOTE]
API поставщика ресурсов SQL Server несовместимо с базой данных SQL Azure.

## <a name="sql-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов SQL

Поставщик ресурсов состоит из следующих компонентов:

- **Виртуальная машина адаптера поставщика ресурсов SQL** — виртуальная машина Windows Server, на которой выполняются службы поставщика.
- **Поставщик ресурсов** — обрабатывает запросы к ресурсам базы данных и предоставляет к ним доступ.
- **Серверы, на которых размещается SQL Server**. Эти серверы размещения поддерживают функционирование баз данных.

Нужно создать по меньшей мере один экземпляр SQL Server или предоставить доступ к внешним экземплярам SQL Server.

> [!NOTE]
> Серверы размещения, установленные в интегрированных системах Azure Stack, необходимо создавать на основе подписки клиента. Их нельзя создать из подписки поставщика по умолчанию. Эти серверы нужно создавать на портале клиента или с помощью PowerShell, используя соответствующее имя для входа. Все серверы размещения — это платные виртуальные машины, которые должны иметь соответствующие лицензии. Администратор служб может быть владельцем подписки клиента.

## <a name="next-steps"></a>Дополнительная информация

[Развертывание поставщика ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md)
