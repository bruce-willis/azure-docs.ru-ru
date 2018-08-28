---
title: Добавление политики именования для групп Office 365 в Azure Active Directory | Документация Майкрософт
description: Инструкции по добавлению новых или удалению существующих пользователей в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8ebdb22ba5ca04a5c811b3b368055f5f4371c75f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208940"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Краткое руководство. Политика именования для групп в Azure Active Directory

В рамках этого краткого руководства вы настроите политику именования в клиенте Azure Active Directory (Azure AD) для созданных пользователем групп Office 365, чтобы отсортировать группы клиента и найти их. Например, политику именования можно использовать, чтобы:

* Указать функцию группы, членство, географический регион или создателя группы.
* Помочь классифицировать группы в адресной книге.
* Запретить использование конкретных слов в именах и псевдонимах групп.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="install-powershell-cmdlets"></a>Установка командлетов PowerShell

Обязательно удалите все старые версии Azure Active Directory PowerShell для модуля Graph для Windows PowerShell и установите [общедоступную предварительную версию Azure Active Directory PowerShell для Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137), прежде чем выполнять команды PowerShell. 

1. Запустите приложение Windows PowerShell от имени администратора.
2. Удалите все предыдущие версии AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Установите последнюю версию AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
При появлении запроса на доступ к ненадежному репозиторию введите **Y** (Да). Установка нового модуля может занять несколько минут.

## <a name="set-up-naming-policy"></a>Настройка политики именования

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Шаг 1. Вход с использованием командлетов PowerShell

1. Откройте приложение Windows PowerShell. Более высокий уровень привилегий не требуется.

2. Выполните следующие команды, чтобы подготовить среду к выполнению командлетов.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  На открывшемся экране **Вход в учетную запись** введите учетную запись администратора и пароль, чтобы подключиться к службе, затем выберите **Войти**.

3. Чтобы создать параметры группы для этого клиента, выполните инструкции в статье [Настройка параметров групп с помощью командлетов Azure Active Directory](groups-settings-cmdlets.md).

### <a name="step-2-view-the-current-settings"></a>Шаг 2. Просмотр текущих параметров

1. Просмотрите текущие параметры политики именования.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Отобразите на экране текущие параметры групп.
  
  ````
  $Setting.Values
  ````
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Шаг 3. Установка политики именований и любых настраиваемых запрещенных слов

1. Задайте префиксы и суффиксы имен групп в PowerShell для Azure AD.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Задайте настраиваемые запрещенные слова. В следующем примере показано, как можно добавить собственные настраиваемые слова.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Сохраните параметры новой политики, чтобы она вступила в действие, как показано в следующем примере.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Вот и все. Вы настроили политику именования и добавили настраиваемые запрещенные слова.

## <a name="clean-up-resources"></a>Очистка ресурсов

1. Задайте префиксы и суффиксы имен групп в PowerShell для Azure AD.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =""
  ````
  
2. Задайте настраиваемые запрещенные слова. В следующем примере показано, как можно добавить собственные настраиваемые слова.
  
  ````
  $Setting["CustomBlockedWordsList"]=""
  ````
  
3. Сохраните параметры новой политики, чтобы она вступила в действие, как показано в следующем примере.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как настроить политику именования для своего клиента Azure AD с помощью командлетов PowerShell.

Дополнительные сведения о технических ограничениях, добавлении списка настраиваемых запрещенных слов или работе пользователя в приложениях Office 365 см. в следующей статье, чтобы узнать больше о политике именования.
> [!div class="nextstepaction"]
> [Применение политики именования для групп Office 365 в Azure Active Directory (предварительная версия)](groups-naming-policy.md)