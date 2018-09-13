---
title: Безопасность на уровне столбцов в хранилище данных SQL Azure | Документы Майкрософт
description: С помощью функции безопасности на уровне столбцов (CLS) можно управлять доступом к столбцам таблицы базы данных на основе контекста выполнения пользователя или членства в группе. Безопасность на уровне столбцов (CLS) упрощает проектирование и программирование безопасности в приложении. CLS позволяет реализовать ограничения на доступ к столбцам.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 1765c92ad10fa35af98e7c7314eb44c3a119f422
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301061"
---
# <a name="column-level-security"></a>Безопасность на уровне столбцов 
С помощью функции безопасности на уровне столбцов (CLS) можно управлять доступом к столбцам таблицы базы данных на основе контекста выполнения пользователя или членства в группе.  

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS упрощает проектирование и программирование безопасности в приложении. CLS позволяет реализовать ограничения на доступ к столбцам для защиты конфиденциальных данных. Например, можно предоставить конкретным пользователям доступ только к определенным столбцам таблицы, имеющей отношение к их отделу. Логика ограничения доступа находится на уровне базы данных, а не на другом уровне приложения отдельно от данных. Базы данных применяет ограничения доступа при каждой попытке получения доступа к данным независимо от уровня. Это ограничение делает систему безопасности более надежной и устойчивой за счет уменьшения ее контактной зоны. Кроме того, безопасность на уровне столбцов также позволяет отказаться от ввода представлений для фильтрации столбцов с целью наложения на пользователей ограничений доступа. 

Реализовать CLS можно с помощью инструкции T-SQL [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql). При использовании этого механизма поддерживаются проверки подлинности SQL и Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Синтаксис 

```sql
GRANT <permission> [ ,...n ] ON    
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]   
    TO <database_principal> [ ,...n ]    
    [ WITH GRANT OPTION ]   
    [ AS <database_principal> ]   
<permission> ::=   
    SELECT 
  | UPDATE  
<database_principal> ::=    
      Database_user    
    | Database_role    
    | Database_user_mapped_to_Windows_User    
    | Database_user_mapped_to_Windows_Group    
```

## <a name="example"></a>Пример 
В следующем примере показано, как ограничить доступ пользователя "TestUser" к столбцу "SSN" таблицы "Membership". 

Создайте таблицу "Membership" со столбцом "SSN" для хранения номеров социального страхования.

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Предоставьте пользователю "TestUser" доступ ко всем столбцам, за исключением столбца "SSN", который содержит конфиденциальные данные. 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Запросы, выполняемые от имени "TestUser", завершатся ошибкой, если они содержат столбец "SSN".

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Варианты использования
Ниже приведены некоторые примеры использования CLS в современном мире. 
- В компании, предоставляющей финансовые услуги, обращаться к номерам социального страхования (SSN), номерам телефонов и другим персональным данным клиентов могут только менеджеры по работе с клиентами.
- В медицинской организации доступ к конфиденциальным медицинским записям имеют только врачи и медсестры. Сотрудники отдела выставления счетов не могут просматривать эти данные.
