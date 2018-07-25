---
title: Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт
description: Узнайте, как управлять администраторами серверов служб Analysis Services в Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866973"
---
# <a name="manage-server-administrators"></a>Управление администраторами серверов
Администраторами сервера должны быть допустимые пользователи или группы безопасности в Azure Active Directory (Azure AD) для клиента, в котором размещен сервер. Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** для сервера на портале Azure или свойств сервера в SSMS. 

> [!NOTE]
> У групп безопасности свойство `MailEnabled` должно иметь значение `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Добавление администраторов на портале Azure
1. На портале выберите для сервера **Администраторы служб Analysis Services**.
2. В области **\<имя_сервера> — Администраторы служб Analysis Services** щелкните **Добавить**.
3. В области **добавления администраторов сервера** выберите учетные записи пользователей из Azure AD или пригласите внешних пользователей по электронной почте.

    ![Администраторы сервера на портале Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Добавление администраторов сервера с помощью SSMS
1. Щелкните правой кнопкой мыши сервер и выберите пункт **Свойства**.
2. В разделе **Свойства сервера анализа данных** выберите **Безопасность**.
3. Нажмите кнопку **Добавить**, а затем введите адрес электронной почты для пользователя или группы в Azure AD.
   
    ![Добавление администраторов сервера в SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Дополнительная информация 
[Аутентификация и пользовательские разрешения](analysis-services-manage-users.md)  
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)  
[Управление доступом на основе ролей](../role-based-access-control/overview.md)  

