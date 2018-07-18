---
title: Перемещение базы данных Azure AD Connect с SQL Server Express на SQL Server | Документация Майкрософт
description: В этом документе описано, как переместить базу данных Azure AD Connect с локального сервера SQL Server Express на удаленный сервер SQL Server.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 03/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 16b8cb843b7081c2489f1b8048d896858c5424c2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231790"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Перемещение базы данных Azure AD Connect с SQL Server Express на SQL Server 

В этом документе описано, как переместить базу данных Azure AD Connect с локального сервера SQL Server Express на удаленный сервер SQL Server.  Для выполнения этой задачи можно использовать описанные ниже процедуры.

## <a name="about-this-scenario"></a>Сведения об этом сценарии
Ниже представлена краткая информация об этом сценарии.  В соответствии с ним база данных Azure AD Connect версии (1.1.819.0) установлена на одном контроллере домена Windows Server 2016.  Для своей базы данных он использует встроенный SQL Server 2012 Express Edition.  База данных будет перемещена на сервер SQL Server 2017.

![](media/active-directory-aadconnect-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Перемещение базы данных Azure AD Connect
Выполните следующие действия, чтобы переместить базу данных Azure AD Connect на удаленный сервер SQL Server.

1.  На сервере Azure AD Connect перейдите в раздел **Службы** и остановите службу **Microsoft Azure AD Sync**.
2. Найдите папку **%Program Files%\Microsoft Azure AD Sync/Data/** и скопируйте файлы **ADSync.mdf** и **ADSync_log.mdf** на удаленный сервер SQL Server.
3. Перезапустите службу **Microsoft Azure AD Sync** на сервере Azure AD Connect.
4. Удалите базу данных Azure AD Connect, последовательно выбрав "Панель управления" > "Программы" > "Программы и компоненты".  Выберите Microsoft Azure AD Connect и нажмите кнопку "Удалить" вверху.
5. На удаленном сервере SQL Server откройте SQL Server Management Studio.
6. Правой кнопкой мыши щелкните элемент "Базы данных" и выберите пункт "Вложить".
7. На экране **Присоединение баз данных** щелкните **Добавить** и перейдите к файлу ADSync.mdf.  Последовательно выберите **ОК**.
![](media/active-directory-aadconnect-move-db/move2.png)

8. После присоединения базы данных вернитесь на сервер Azure AD Connect и установите базу данных Azure AD Connect.
9. По завершении установки MSI мастер Azure AD Connect запускается с настройкой режима Express. Закройте экран, щелкнув значок "Выход".
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db1.png)
10. Запустите новую командную строку или сеанс PowerShell. Перейдите к папке <drive>\Program Files\Microsoft Azure AD Connect. Выполните команду \AzureADConnect.exe /useexistingdatabase, чтобы запустить мастер Azure AD Connect в режиме установки "Использовать существующую базу данных".
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
11. Появится экран приветствия Azure AD Connect. После принятия условий лицензии и заявления о конфиденциальности, щелкните **Продолжить**.
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db3.png)
12. На экране **Установить требующиеся компоненты** включен параметр **Использовать существующий SQL Server**. Укажите имя сервера SQL, на котором размещена база данных ADSync. Если экземпляр ядра SQL, используемый для размещения базы данных, не является экземпляром по умолчанию сервера SQL, необходимо указать ядро SQL и имя экземпляра. Кроме того, если просмотр SQL не включен, также необходимо указать номер порта экземпляра ядра SQL. Например:          
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db4.png)           

13. На экране **Подключение к Azure AD** необходимо предоставить учетные данные глобального администратора для каталога Azure AD. Рекомендуется использовать учетную запись в домене onmicrosoft.com по умолчанию. Эта учетная запись используется только для создания учетной записи службы в Azure AD и не используется после завершения работы мастера.
![Подключение](media/active-directory-aadconnect-existing-database/db5.png)
 
14. На экране **Подключить каталоги** имеющийся лес AD, настроенный для синхронизации каталогов, помечен красным значком с крестиком. Для синхронизации изменений из локального леса AD необходима учетная запись AD DS. Мастер Azure AD Connect не может извлечь учетные данные учетной записи AD DS, хранимые в базе данных ADSync, так как они зашифрованы и могут быть расшифрованы только предыдущим сервером Azure AD Connect. Щелкните **Изменить учетные данные**, чтобы указать учетную запись AD DS для леса AD.
![Directories](media/active-directory-aadconnect-existing-database/db6.png)
 
 
15. Во всплывающем диалоговом окне можно (а) предоставить учетные данные администратора предприятия и позволить Azure AD Connect создать учетную запись AD DS или (б) создать учетную запись AD DS самостоятельно и предоставить ее учетные данные Azure AD Connect. После выбора варианта и предоставления необходимых учетных данных щелкните **ОК**, чтобы закрыть всплывающее диалоговое окно.
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db7.png)
 
 
16. После предоставления учетных данных красный значок с крестиком заменяется зеленым значком с галочкой. Нажмите кнопку **Далее**.
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db8.png)
 
 
17. На экране **Все готово к настройке** щелкните **Установить**.
![Добро пожаловать!](media/active-directory-aadconnect-existing-database/db9.png)
 
 
18. По завершении установки сервер Azure AD Connect автоматически включается в промежуточном режиме. Перед отключением промежуточного режима рекомендуется проверить конфигурацию сервера и ожидающие операции экспорта на наличие неожиданных изменений. 

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).
- [Установка Azure AD Connect с помощью имеющейся базы данных ADSync](active-directory-aadconnect-existing-database.md)
- [Установка Azure AD Connect с помощью разрешений делегированного администратора SQL](active-directory-aadconnect-sql-delegation.md)

