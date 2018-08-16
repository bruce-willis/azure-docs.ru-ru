---
title: Отключение конвергентной регистрации методов SSPR и MFA в Azure AD (общедоступная предварительная версия)
description: Отключение регистрации методов Многофакторной идентификации и самостоятельного сброса пароля в Azure AD (общедоступная предварительная версия)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 3ce08f67f001a7c43602627b9eeda3ad60f867c1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623222"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Отключение конвергентной регистрации в Azure AD (общедоступная предварительная версия)

Когда пользователь регистрирует свой номер телефона или мобильное приложение в новом интерфейсе конвергентной регистрации, наша служба маркирует набор флагов (StrongAuthenticationMethods) с учетом этих методов для этого пользователя. Эта функция позволяет пользователю при необходимости выполнять Многофакторную Идентификацию Azure (MFA) с использованием этих методов.

Для методов, которые пользователи регистрируют в новом интерфейсе, задается свойство StrongAuthenticationMethods. Такое поведение также будет наблюдаться после выхода общедоступной предварительной версии. Если администратор включает предварительную версию, пользователи регистрируются с помощью нового интерфейса, а когда администратор отключает ее, пользователи могут также неосознанно быть зарегистрированы и для MFA.

Если пользователь, выполнивший конвергентную регистрацию, перейдет на текущую страницу регистрации SSPR по адресу [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), ему будет предложено выполнить MFA, после чего он сможет получить доступ к этой странице. Этот шаг является ожидаемым поведением с технической точки зрения, но для пользователей, которые ранее были зарегистрированы только для SSPR, это неожиданно. Хотя этот дополнительный шаг улучшает состояние безопасности пользователя, обеспечивая дополнительный уровень защиты, администраторы могут не использовать его для своих пользователей, чтобы они не выполняли Многофакторную идентификацию.  

## <a name="how-to-roll-back-users"></a>Как откатить пользователей

Если вы, как администратор, хотите сбросить параметры MFA пользователя, мы создали сценарий PowerShell, который очистит свойство StrongAuthenticationMethods с мобильным приложением или номером телефона пользователя. Запуск этого сценария для ваших пользователей означает, что им потребуется повторная регистрация для MFA в случае необходимости. Мы рекомендуем проверять откаты с одним или двумя пользователями перед откатом всех затронутых пользователей.

Следующие шаги помогут вам откатить пользователя или группу пользователей.

### <a name="pre-requisites"></a>Предварительные требования

1. Вам необходимо будет установить соответствующие модули Azure AD PowerShell. В окне PowerShell выполните следующие команды для установки модулей:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Сохраните список затронутых идентификаторов объектов пользователей на своем компьютере в виде текстового файла с одним идентификатором в строке. Запишите расположение файла.
1. Сохраните следующий сценарий на компьютере и запишите расположение сценария:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Откат

В окне PowerShell выполните следующую команду после обновления выделенных расположений. При появлении запроса введите учетные данные глобального администратора. Сценарий выведет результат каждой операции обновления пользователя.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Отключение использования предварительной версии

Чтобы отключить функцию предварительной версии для пользователей, выполните следующие действия:

1. Войдите на портал Azure как глобальный администратор или администратор пользователей.
2. Перейдите в раздел **Azure Active Directory**, **Параметры пользователя**, **Управление параметрами для функций предварительной версии панели доступа**.
3. В разделе **Пользователи могут использовать функции предварительной версии, чтобы регистрировать конфиденциальные сведения и управлять ими** установите для селектора значение **Нет**, а затем нажмите кнопку **Сохранить**.

Пользователи больше не будут получать предложения о регистрации от предварительной версии.

## <a name="next-steps"></a>Дополнительная информация

[Конвергентная регистрация методов самостоятельного сброса пароля и Многофакторной идентификации Azure](concept-registration-mfa-sspr-converged.md)