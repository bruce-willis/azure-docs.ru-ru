---
title: Удаление персональных данных в Azure Active Directory Application Proxy | Документация Майкрософт
description: Удаление персональных данных из соединителей, установленных на устройствах для Azure Active Directory Application Proxy.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 94cf0464aca3c46e0c6425b0fb3e24fcd767f95c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608312"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Удаление персональных данных в Azure Active Directory Application Proxy  

Для работы Azure Active Directory Application Proxy требуется установка соединителей на устройствах. Это значит, что устройства могут содержать персональные данные. Эта статья содержит указания по удалению этих персональных данных для повышения конфиденциальности. 


## <a name="where-is-the-personal-data"></a>Где находится персональные данные?
Прокси приложений может записывать персональные данные в журналы следующих типов:

- Журналы событий соединителя
- Журналы событий Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Удаление персональных данных из журналов событий Windows

Сведения о том, как настроить хранение данных журналов событий Windows, приведены в разделе [Settings for Event Logs](https://technet.microsoft.com/library/cc952132.aspx) (Параметры журналов событий). Чтобы узнать о журналах событий Windows, ознакомьтесь с разделом [Using Windows Event Log](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx) (Использование журнала событий Windows).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Удаление персональных данных из журналов событий соединителя

Чтобы в журналах прокси приложения отсутствовали персональные данные, вы можете:

- удалить или просмотреть данные, когда это требуется, или
- отключить ведение журналов.

Используйте следующие разделы, чтобы удалить персональные данные из журналов событий соединителя. Необходимо выполнить процесс удаления на всех устройствах, на которых установлен соединитель.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Просмотр или экспорт определенных данных

Чтобы просмотреть или экспортировать определенные данные, найдите связанные записи в каждом из журналов событий соединителя. Журналы можно найти в папке `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Так как журналы — это текстовые файлы, для поиска текстовых записей, относящихся к пользователю, можно использовать [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr).  

Чтобы найти персональные данные, ищите в файлах журнала значение UserID. 

Чтобы найти персональные данные, записанные приложением, которое использует ограниченное делегирование Kerberos, выполните поиск приведенных ниже компонентов типа username:

- имя локального участника-пользователя;
- компонент, содержащий имя пользователя, в имени участника-пользователя;
- компонент, содержащий имя пользователя, в имени локального участника-пользователя;
- учетная запись локального диспетчера учетных записей безопасности (SAM). 


### <a name="delete-specific-data"></a>Удаление определенных данных

Вот как можно удалить определенные данные.

1. Перезапустите службу соединителя Microsoft Azure AD Application Proxy, чтобы создать новый файл журнала. После создания нового файла журнала можно удалить или изменить старые файлы журнала. 
2. Выполните процедуру [просмотра или экспорта определенных данных](#view-or-export-specific-data), описанную выше, чтобы найти сведения, которые необходимо удалить. Выполните поиск во всех журналах соединителя.
3. Удалите соответствующие файлы журнала или выборочно удалите поля, которые содержат персональные данные. Если старые файлы журнала не нужны, можно просто удалить их все сразу.

### <a name="turn-off-connector-logs"></a>Отключение журналов соединителя

Один из способов обеспечить отсутствие персональных данных в журналах соединителя — отключить ведение журнала. Чтобы остановить создание журналов соединителя, удалите выделенную ниже строку из `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Параметр Configuration](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Дополнительная информация

Общие сведения о прокси приложения см. в статье [Как обеспечить безопасный удаленный доступ к локальным приложениям](application-proxy.md).

