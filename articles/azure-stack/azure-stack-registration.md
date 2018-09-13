---
title: Регистрация Azure для интегрированных систем Azure Stack | Документация Майкрософт
description: Описано, как зарегистрировать Azure для подключенных к Azure развернутым службам с несколькими узлами Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 5a6dcddce3337989a7a34515570ac3277aa1edd5
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841936"
---
# <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure

Регистрация Azure Stack в Azure позволяет скачивать элементы marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. После регистрации Azure Stack данные об использовании отсылаются в отдел коммерческих предложений Azure и их можно просматривать в подписке, используемой для регистрации.

В этой статье описывается регистрация интегрированных систем Azure Stack с помощью Azure. Сведения о регистрации ASDK с помощью Azure см. в статье [Регистрация Azure Stack](.\asdk\asdk-register.md).

> [!IMPORTANT]  
> Регистрация требуется для поддержки полной функциональности Azure Stack, включая предложение элементов в marketplace. Кроме того, вы нарушите условия лицензии на Azure Stack, если не выполните регистрацию при применении модели выставления счетов с оплатой по мере использования. Дополнительные сведения о лицензировании Azure Stack см. на странице [Как купить](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Предварительные требования

Перед регистрацией необходимо выполнить следующие задачи:

 - Проверка учетных данных
 - Установка языкового режима PowerShell.
 - Установка PowerShell для Azure Stack
 - Скачивание средств Azure Stack
 - Определение своего сценария регистрации.

### <a name="verify-your-credentials"></a>Проверка учетных данных

Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, войдите в Azure, щелкните **Больше служб** > **Подписки**, а затем выберите нужную подписку и найдите идентификатор подписки в разделе **Основные компоненты**.

  > [!Note]  
  > Облачные подписки для Германии в настоящее время не поддерживаются.

- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).

- Учетная запись пользователя должна иметь права администратора в клиенте Azure AD, в котором зарегистрирован экземпляр Azure Stack, например, `yourazurestacktenant.onmicrosoft.com`.

- Зарегистрированный поставщик ресурсов Azure Stack. Дополнительные сведения см. в приведенном ниже разделе о регистрации поставщика ресурсов Azure Stack.

  Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.

### <a name="powershell-language-mode"></a>Языковой режим PowerShell

Чтобы успешно зарегистрировать Azure Stack, необходимо установить для языкового режима PowerShell значение **FullLanguageMode**.  Чтобы проверить, что текущий языковой режим имеет значение full, откройте окно PowerShell с повышенным уровнем разрешений и выполните следующие командлеты PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Убедитесь, что в выходных данных возвращено значение **FullLanguageMode**. Если вернулось другое значение языкового режима, необходимо выполнить регистрацию на другом компьютере или присвоить языковому режиму значение **FullLanguageMode** и продолжить регистрацию.

### <a name="install-powershell-for-azure-stack"></a>Установка PowerShell для Azure Stack

Чтобы зарегистрировать систему в Azure, требуется последняя версия PowerShell для Azure Stack.

Если последняя версия еще не установлена, ознакомьтесь с [установкой PowerShell для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Скачивание средств Azure Stack

Репозиторий GitHub со средствами Azure Stack содержит модули PowerShell, которые поддерживают функциональные возможности Azure Stack, в том числе функции регистрации. Для регистрации экземпляра Azure Stack в Azure вам нужно импортировать и использовать модуль PowerShell **RegisterWithAzure.psm1**, который можно найти в репозитории средств Azure Stack.

Перед регистрацией в Azure удалите все существующие версии средств Azure Stack и [загрузите новейшую версию с сайта GitHub](azure-stack-powershell-download.md).

### <a name="determine-your-registration-scenario"></a>Определение своего сценария регистрации.

Развертывание Azure Stack может быть *подключено* или *отключено*.

 - **Подключено**  
 "Подключено" означает, что вы развернули экземпляр Azure Stack так, что он может подключиться к Интернету и к Azure. Для хранилища удостоверений используется Azure Active Directory (Azure AD) или службы федерации Active Directory (AD FS). Для подключенных развертываний можно выбрать из двух моделей выставления счетов: оплата по мере использования или на основе емкости.
    - [Регистрация подключенного Azure Stack в Azure с использованием модели выставления счетов **с оплатой по мере использования**](#register-connected-with-pay-as-you-go-billing)
    - [Регистрация подключенного Azure Stack в Azure с использованием модели выставления счетов **с оплатой на основе емкости**](#register-connected-with-capacity-billing)

 - **Отключено**  
 С помощью развертывания Azure без подключения можно развернуть и использовать Azure Stack без подключения к Интернету. Но при этом вы будете ограничены хранилищем удостоверений службы федерации Active Directory (AD FS) и моделью выставления счетов на основе емкости.
    - [Регистрация подключенного Azure Stack с использованием модели выставления счетов **с оплатой на основе емкости**](#register-disconnected-with-capacity-billing)

## <a name="register-connected-with-pay-as-you-go-billing"></a>Регистрация подключенного развертывания с оплатой по мере использования

Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования.

> [!Note]  
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке (PEP). Дополнительные сведения о PEP см. в статье об [использовании привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).

Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack, а затем настроить модель выставления счетов.

1. Чтобы зарегистрировать поставщик ресурсов Azure Stack в Azure, запустите интегрированную среду сценариев PowerShell и используйте следующие командлеты PowerShell с параметром **EnvironmentName**, заданным для соответствующего типа подписки Azure (см. параметры ниже).

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, выполните командлет **Add-AzureRmAccount**. Вам будет предложено ввести данные учетной записи глобального администратора Azure. Возможно, потребуется выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Параметр | ОПИСАНИЕ |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**.  |

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack в своей подписке Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Затем, в том же сеансе PowerShell, убедитесь, что вы вошли в правильный контекст Azure PowerShell. Это учетная запись Azure, которая использовалась для регистрации указанного выше поставщика ресурсов Azure Stack. Выполните эти команды PowerShell:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

7. В этом же сеансе PowerShell выполните командлет **Set-AzsRegistration**. Выполните эти команды PowerShell:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Дополнительные сведения о командлете Set-AzsRegistration см. в разделе [Справка по регистрации](#registration-reference).

  Процесс займет от 10 до 15 минут. После выполнения команды отобразится сообщение **Your environment is now registered and activated using the provided parameters** (Ваша среда зарегистрирована и активирована с помощью предоставленных параметров).

## <a name="register-connected-with-capacity-billing"></a>Регистрация подключенного развертывания с оплатой на основе емкости

Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования.

> [!Note]  
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке (PEP). Дополнительные сведения о PEP см. в статье об [использовании привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).

Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack, а затем настроить модель выставления счетов.

1. Чтобы зарегистрировать поставщик ресурсов Azure Stack в Azure, запустите интегрированную среду сценариев PowerShell и используйте следующие командлеты PowerShell с параметром **EnvironmentName**, заданным для соответствующего типа подписки Azure (см. параметры ниже).

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, выполните командлет **Add-AzureRmAccount**. Вам будет предложено ввести данные учетной записи глобального администратора Azure. Возможно, потребуется выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Параметр | ОПИСАНИЕ |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud**, **AzureUSGovernment** или, при использовании подписки Azure для Китая, **AzureChinaCloud**.  |

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack в своей подписке Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Отчеты о потреблении можно отключить с помощью параметра UsageReportingEnabled командлета **Set-AzsRegistration**. Задайте параметру значение false. Например, "UsageReportingEnabled".
   
  Дополнительные сведения о командлете Set-AzsRegistration см. в разделе [Справка по регистрации](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Регистрация отключенного развертывания с оплатой на основе емкости

При регистрации Azure Stack в отключенной среде (без подключения к Интернету) необходимо получить маркер регистрации из среды Azure Stack, а затем использовать этот маркер на компьютере, который можно подключить к Azure и на котором [установлена среда PowerShell для Azure Stack](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение маркера регистрации из среды Azure Stack

1. Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1**:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Чтобы получить маркер регистрации, выполните следующие командлеты PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Дополнительные сведения о командлете Get-AzsRegistrationToken см. в разделе [Справка по регистрации](#registration-reference).

   > [!Tip]  
   > Маркер регистрации сохранен в файле, указанном для *$FilePathForRegistrationToken*. Путь к файлу или его имя можно изменить по своему усмотрению.

3. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure. Файл или текст можно скопировать из $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация

На компьютере, подключенном к Интернету, выполните те же действия, чтобы импортировать модуль RegisterWithAzure.psm1 и войти в правильный контекст Azure Powershell. Затем вызовите командлет Register-AzsEnvironment. Укажите маркер регистрации для регистрации в Azure: При регистрации нескольких экземпляров Azure Stack с использованием одного идентификатора подписки Azure следует задать уникальное имя регистрации. Выполните следующий командлет:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Сохраните имя ресурса регистрации или токен регистрации для дальнейшего использования.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Извлечение ключа активации из ресурса регистрации Azure

Затем необходимо извлечь ключ активации из ресурса регистрации, созданного в Azure во время выполнения Register-AzsEnvironment.

Чтобы получить ключ активации, выполните следующие командлеты PowerShell:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Ключ активации сохраняется в файл, указанный для *$KeyOutputFilePath*. Путь к файлу или его имя можно изменить по своему усмотрению.

### <a name="create-an-activation-resource-in-azure-stack"></a>Создание ресурса активации в Azure Stack

Вернитесь в среду Azure Stack с файлом или текстом из ключа активации, созданного из Get-AzsActivationKey. Затем создайте ресурс активации в Azure Stack с помощью этого ключа активации. Чтобы создать ресурс активации, выполните следующие командлеты PowerShell:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Проверка регистрации Azure Stack

Чтобы убедиться, что инфраструктура Azure Stack успешно зарегистрирована в Azure, следуйте инструкциям ниже.

1. Войдите на [портал администратора](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) Azure Stack: https&#58;//adminportal.*&lt;регион>.&lt;полное доменное имя>*.
2. Щелкните **Больше служб** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно. Тем не менее в отключенных средах элементы Azure Marketplace не отображаются в Azure Stack Marketplace.

> [!Note]  
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться.

## <a name="renew-or-change-registration"></a>Обновление или изменение регистрации

### <a name="renew-or-change-registration-in-connected-environments"></a>Обновление или изменение регистрации в подключенных средах

Регистрацию необходимо обновлять или возобновлять в следующих случаях:

- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

#### <a name="change-the-subscription-you-use"></a>Изменение подписки

Если вы хотите изменить используемую подписку, сначала выполните командлет **Remove-AzsRegistration**. Убедитесь, что вы вошли в правильном контексте выполнения Azure PowerShell, и выполните командлет **Set-AzsRegistration** с измененными параметрами:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Изменение компонентов модели выставления счетов или способов предложения функций

Если вам нужно изменить компоненты модели выставления счетов или способов предложения функций для установки, чтобы задать новые значения, можно применить функцию регистрации. Для этого не требуется удалять текущую регистрацию:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Обновление или изменение регистрации в отключенных средах

Регистрацию необходимо обновлять или возобновлять в следующих случаях:

- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Удаление ресурса активации из Azure Stack

Сначала необходимо удалить ресурс активации из Azure Stack, а затем — из Azure.  

Чтобы удалить ресурс активации из Azure Stack, выполните следующие командлеты PowerShell в среде Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Затем, чтобы удалить ресурс регистрации в Azure, убедитесь, что используется компьютер, подключенный к Azure, войдите в правильный контекст Azure PowerShell и выполните соответствующие командлеты PowerShell, как описано ниже.

Вы можете использовать токен регистрации, с помощью которого создавался ресурс:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Или можно использовать имя регистрации:

  ```Powershell
  $registrationName = "AzureStack-<Cloud ID of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Повторная регистрация c использованием шагов для отключенной среды

Регистрация полностью отменена в сценарии с отключенной средой. Теперь вам следует повторить шаги для регистрации среды Azure Stack в сценарии с отключенной средой.

### <a name="disable-or-enable-usage-reporting"></a>Отключение или включение отчетов о потреблении

Для сред Azure Stack, которые применяют модель выставления счетов с оплатой на основе емкости, отключите отчеты о потреблении с помощью параметра **UsageReportingEnabled**, используя командлет **Set-AzsRegistration** либо **Get-AzsRegistrationToken**. Azure Stack передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении.

#### <a name="with-a-connected-azure-stack"></a>С подключенным Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>С отключенным Azure Stack

1. Чтобы изменить маркер регистрации, выполните следующие командлеты PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Маркер регистрации сохранен в файле, указанном для *$FilePathForRegistrationToken*. Путь к файлу или его имя можно изменить по своему усмотрению.

2. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure. Файл или текст можно скопировать из $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Перемещение ресурса регистрации
Перемещение ресурса регистрации между группами ресурсов в одной подписке **поддерживается** для всех сред. Однако перемещение ресурса регистрации между подписками поддерживается только для CSP, когда обе подписки разрешают один и тот же идентификатор партнера. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Справка по регистрации

### <a name="set-azsregistration"></a>Set-AzsRegistration

Командлет Set-AzsRegistration позволяет зарегистрировать Azure Stack в Azure, а также включить или отключить предложение элементов в marketplace и отчеты о потреблении.

Чтобы запустить командлет, вам потребуется:
- Глобальная подписке Azure любого типа.
- Также необходимо войти в Azure PowerShell с помощью учетной записи владельца или участника этой подписки.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Параметр | type | ОПИСАНИЕ |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Учетные данные, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Строка | Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | Строка |  |
| ResourceGroupLocation | Строка |  |
| BillingModel | Строка | Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development. |
| MarketplaceSyndicationEnabled |  |  |
| UsageReportingEnabled | True или False | Azure Stack передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении. Допустимые значения для этого параметра: true, false. |
| AgreementNumber | Строка |  |
| RegistrationName | Строка | Задайте уникальное имя регистрации, если сценарий регистрации выполняется на нескольких экземплярах Azure Stack с использованием одного идентификатора подписки Azure. Этот параметр имеет значение по умолчанию **AzureStackRegistration**. Тем не менее, если задать одно имя для нескольких экземпляров Azure Stack, сценарий завершится ошибкой. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Командлет Get-AzsRegistrationToken создает маркер регистрации из входных параметров.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Параметр | type | ОПИСАНИЕ |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Учетные данные, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Строка |  Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | Строка |  |
| ResourceGroupLocation | Строка |  |
| BillingModel | Строка | Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development. |
| MarketplaceSyndicationEnabled | True или False |  |
| UsageReportingEnabled | True или False | Azure Stack передает метрики использования по умолчанию. Операторам, использующим оплату за емкость или поддерживающим отключенную среду, необходимо отключить отчеты о потреблении. Допустимые значения для этого параметра: true, false. |
| AgreementNumber | Строка |  |


## <a name="next-steps"></a>Дополнительная информация

[Скачивание элементов Marketplace из Azure](azure-stack-download-azure-marketplace-item.md)
