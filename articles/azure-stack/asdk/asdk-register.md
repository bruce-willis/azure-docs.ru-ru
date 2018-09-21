---
title: Регистрация ASDK в Azure | Документация Майкрософт
description: В этой статье объясняется, как зарегистрировать Azure Stack в Azure, чтобы включить синдикацию Marketplace и создание отчетов о потреблении.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: eacf8fc9335af2dacffa3e13da47ea39a2776f2b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714570"
---
# <a name="azure-stack-registration"></a>Регистрация Azure Stack
Вы можете зарегистрировать устанавливаемый Пакет средств разработки Azure Stack (ASDK) в Azure, чтобы скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. Регистрация требуется для поддержки полной функциональности Azure Stack, включая синдикацию marketplace. Рекомендуем использовать регистрацию, так как она позволяет протестировать важные функции Azure Stack, например синдикацию Marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. Но с пользователей ASDK не взимается плата на основе отчетов о потреблении.

Если не зарегистрировать пакет ASDK, отобразится предупреждение **Требуется активация**. Это значит, что вам необходимо зарегистрировать Пакет средств разработки Azure Stack. Это ожидаемое поведение.

## <a name="prerequisites"></a>Предварительные требования
Перед выполнением инструкций по регистрации ASDK в Azure установите PowerShell для Azure Stack и скачайте средства Azure Stack, как описано в статье о [настройке, выполняемой после установки ASDK](asdk-post-deploy.md).

Кроме того, на компьютере, используемом для регистрации ASDK в Azure, необходимо установить параметр языкового режима PowerShell **FullLanguageMode**. Чтобы проверить, что текущий языковой режим имеет значение full, откройте окно PowerShell с повышенным уровнем разрешений и выполните следующие команды PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Убедитесь, что в выходных данных возвращено значение **FullLanguageMode**. Если вернулось другое значение языкового режима, необходимо выполнить регистрацию на другом компьютере или задать для языкового режима значение **FullLanguageMode** и продолжить регистрацию.

## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Чтобы зарегистрировать ASDK в Azure, следуйте приведенным ниже инструкциям.

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для ASDK этот компьютер является главным компьютером, на котором размещается пакет средств разработки.

1. Откройте консоль PowerShell от имени администратора.  

2. Выполните следующие команды PowerShell, чтобы зарегистрировать установку ASDK в Azure. Вам понадобится войти в подписку Azure и локальную установку ASDK. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись здесь](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.<br><br>Если сценарий регистрации выполняется на нескольких экземплярах Azure Stack с использованием одного идентификатора подписки Azure, задайте уникальное имя регистрации, выполняя командлет **Set-AzsRegistration**. Параметр **RegistrationName** имеет значение по умолчанию **AzureStackRegistration**. Тем не менее, если задать одно имя для нескольких экземпляров Azure Stack, сценарий завершится ошибкой.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName "<Unique-name>"
    ```
3. После выполнения сценария должно отобразиться сообщение **Your environment is now registered and activated using the provided parameters** (Ваша среда зарегистрирована и активирована с помощью предоставленных параметров).

    ![Среда зарегистрирована](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Подтверждение успешного выполнения регистрации
Выполните следующие действия, чтобы проверить успешность регистрации ASDK в Azure.

1. Войдите на [портал администрирования Azure Stack](https://adminportal.local.azurestack.external).

2. Щелкните **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

    ![](media/asdk-register/2.PNG)

3. Если отобразится список элементов, доступных в Azure, значит активация прошла успешно.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Перемещение ресурса регистрации
Перемещение ресурса регистрации между группами ресурсов в одной подписке **поддерживается**. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Дополнительная информация
[Добавление элемента Marketplace Azure Stack](.\.\azure-stack-marketplace.md)
