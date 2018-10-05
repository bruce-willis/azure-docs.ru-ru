---
title: Условный доступ. База данных SQL Azure и хранилище данных | Документация Майкрософт
description: Узнайте, как настроить условный доступ для базы данных SQL Azure и хранилища данных.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: e7386e2f926132f5657bdb17011dafe1e1c7b101
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063087"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Условный доступ (MFA) и база данных SQL Azure и хранилище данных  

Службы [База данных SQL](sql-database-technical-overview.md) и [Хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) поддерживают условный доступ Майкрософт. 

> [!NOTE]
> Этот раздел относится к Azure SQL Server, а также к базам данных SQL и хранилища данных SQL, создаваемым на сервере Azure SQL Server. Для простоты база данных SQL используется как для базы данных SQL, так и для хранилища данных SQL.

Ниже показано, как настроить базу данных SQL для принудительного применения политики условного доступа.  

## <a name="prerequisites"></a>Предварительные требования  
- Вы должны настроить базу данных SQL или хранилище данных SQL для поддержки аутентификации Azure Active Directory. Дополнительные сведения см. в статье [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](sql-database-aad-authentication-configure.md).  
- При включении многофакторной идентификации необходимо выполнить подключение с помощью поддерживаемого средства, например последней версии SSMS. Дополнительные сведения см. в статье [Настройка Многофакторной идентификации Базы данных SQL Azure для SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Настройка центра сертификации для базы данных или хранилища данных SQL Azure  
1.  Войдите на портал и выберите **Azure Active Directory**, а затем выберите **Условный доступ**. Дополнительные сведения см. в статье [Техническая информация об условном доступе в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![колонка условного доступа](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  В колонке **политик условного доступа** щелкните **Создать политику**, укажите имя, а затем щелкните **Configure rules** (Настройка правил).  
3.  В разделе **Назначения** выберите **Пользователи и группы**, установите флажок **Выбрать пользователей и группы**, а затем выберите пользователя или группу для условного доступа. Щелкните **Выбрать** , а затем **Готово** ,чтобы подтвердить выбор.  
  ![выбор пользователей и групп](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Выберите **Облачные приложения** и щелкните **Выбрать приложения**. Вы увидите все приложения, доступные для условного доступа. Выберите **База данных SQL Azure**, в нижней области щелкните **Выбрать**, а затем щелкните **Готово**.  
  ![выбор базы данных SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Если не удается найти **базу данных SQL Azure**, как на третьем снимке экрана, выполните следующие действия:   
  - Войдите на свой экземпляр базы данных или хранилища данных SQL Azure с помощью SSMS, используя учетную запись администратора AAD.  
  - Выполните `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Войдите в AAD и проверьте наличие базы данных SQL Azure и хранилища данных в приложениях в AAD.  

5.  Выберите **Элементы управления доступом**, выберите **Предоставить**, а затем просмотрите политику, которую вы хотите применить. Для этого примера мы выберем **Требовать многофакторную проверку подлинности**.  
  ![выбор предоставления доступа](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Сводка  
Выбранное приложение (база данных SQL Azure), позволяющее подключаться к базе данных или хранилищу данных SQL Azure, с помощью Azure AD Premium, теперь принудительно применяет выбранную политику условного доступа **Требовать многофакторную проверку подлинности.**  
Если у вас есть вопросы о базе данных SQL Azure и хранилище данных в отношении многофакторной проверки подлинности, используйте MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Дополнительная информация  

Ознакомьтесь с руководством [Защита базы данных SQL Azure](sql-database-security-tutorial.md).
