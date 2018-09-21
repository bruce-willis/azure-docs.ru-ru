---
title: Конфиденциальность пользователей и простой единый вход Azure AD | Документация Майкрософт
description: В этой статье рассматривается простой единый вход Azure Active Directory (Azure AD) и соответствие требованиям GDPR.
services: active-directory
keywords: what is Azure AD Connect, GDPR, required components for Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c7fc6146db93938ed861c6302d2cd8f40b894977
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310393"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Конфиденциальность пользователей и простой единый вход Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Обзор


При простом едином входе Azure AD создается журнал следующего типа, который может содержать персональные данные: 

- файлы журнала трассировки Azure AD Connect;

Конфиденциальность пользователей для простого единого входа можно повысить двумя способами:

1.  по запросу извлекать данные для пользователя и удалять эти данные из установленных решений;
2.  Не хранить данные больше 48 часов.

Настоятельно рекомендуем использовать второй вариант, так как его проще реализовать и обслуживать. См приведенные ниже инструкции по каждому типу журналов.

### <a name="delete-azure-ad-connect-trace-log-files"></a>Удаление файлов журнала трассировки Azure AD Connect

Проверьте содержимое папки **%ProgramData%\AADConnect** и удалите содержимое журнала трассировки (файлы **trace-\*.log**) из этой папке в течение 48 часов после установки или обновления Azure AD Connect либо изменения конфигурации простого единого входа, так как при этих действиях создаются данные, на которые распространяются требования GDPR.

>[!IMPORTANT]
>Не удаляйте файл **PersistedState.xml** в этой папке — он используется для сохранения состояния предыдущей установки Azure AD Connect, а также после установки обновления. В этом файле никогда не хранятся личные данные о пользователях, и его не следует удалять.

Вы можете просмотреть и удалить файлы журналов трассировки с помощью проводника Windows или использовать приведенный ниже скрипт PowerShell.

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Сохраните скрипт в файле с расширением PS1. Запускайте его по мере необходимости.

Дополнительные сведения о требованиях GDPR для Azure AD Connect см. [в этой статье](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Примечание о журналах контроллеров домена

Если включено ведение журнала аудита, этот продукт может создавать журналы безопасности для контроллеров домена. Дополнительные сведения о настройке политик аудита см. в [этой статье](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр политики конфиденциальности корпорации Майкрософт в центре управления безопасностью](https://www.microsoft.com/trustcenter)
- [**Устранение неполадок**](tshoot-connect-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
