---
title: Доступ к Azure Key Vault с помощью Управляемого удостоверения службы виртуальной машины Windows
description: Из этого руководства вы узнаете, как получить доступ к Azure Key Vault с помощью Управляемого удостоверения службы виртуальной машины Windows.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 314e3ba608f41ff83565ddaa32e9cdeed2205211
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886549"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-key-vault"></a>Руководство. Получение доступа к Azure Key Vault с помощью Управляемого удостоверения службы виртуальной машины Windows 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описывается, как получить доступ к Azure Key Vault с помощью удостоверения виртуальной машины Windows, назначаемого системой. При применении в качестве начальной загрузки хранилище Key Vault разрешает клиентскому приложению использовать секрет для доступа к ресурсам, которые не защищены с помощью Azure Active Directory (AD). Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. 

Вы узнаете, как выполнять следующие задачи:


> [!div class="checklist"]
> * предоставлять виртуальной машине доступ к секрету в Key Vault; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для получения секрета из Key Vault. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Предоставление виртуальной машине доступа к секрету в Key Vault 
 
С помощью Управляемого удостоверения службы код может получить маркеры доступа, чтобы пройти проверку подлинности для доступа к ресурсам, поддерживающим проверку подлинности Azure AD.  Однако не все службы Azure поддерживают аутентификацию Azure AD. Чтобы использовать Управляемое удостоверение службы с этими службами, сохраните учетные данные служб в Azure Key Vault и используйте Управляемое удостоверение службы для доступа к Key Vault и получения учетных данных. 

Сначала необходимо создать хранилище Key Vault и предоставить удостоверению виртуальной машины доступ к этому хранилищу.   

1. В верхней части меню навигации слева последовательно выберите **Создать ресурс** > **Безопасность+идентификация** > **Key Vault**.  
2. Укажите **имя** для нового хранилища Key Vault. 
3. Найдите хранилище Key Vault в той же подписке и группе ресурсов, где ранее была создана виртуальная машина. 
4. Выберите **Политики доступа** и щелкните **Добавить**. 
5. В раскрывающемся списке Configure from template (Настроить на основе шаблона) выберите **Управление секретами**. 
6. Выберите **Выбор субъекта** и в поле поиска введите имя созданной ранее виртуальной машины.  Выберите виртуальную машину в списке результатов и щелкните **Выбрать**. 
7. Чтобы завершить добавление новой политики доступа, нажмите кнопку **ОК**, а затем нажмите кнопку **ОК**, чтобы завершить выбор политики доступа. 
8. Чтобы завершить создание хранилища ключей, нажмите кнопку **Создать**. 

    ![Замещающий текст](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Затем добавьте секрет в хранилище Key Vault, чтобы позже извлечь его с помощью кода, который вы запустите на виртуальной машине: 

1. Щелкните **Все ресурсы**, а затем найдите и выберите созданное хранилище Key Vault. 
2. Выберите **Секреты** и щелкните **Добавить**. 
3. В разделе **Параметры отправки** выберите **Вручную**. 
4. Введите имя и значение для секрета.  Значение может быть любым. 
5. Не указывайте дату активации и окончания срока действия и для параметра **Включено** оставьте значение **Да**. 
6. Щелкните **Создать**, чтобы создать секрет. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для получения секрета из Key Vault.  

Если вы не установили версию PowerShell 4.3.1 или выше, [загрузите и установите последнюю версию](https://docs.microsoft.com/powershell/azure/overview).

Сначала с помощью Управляемого удостоверения службы виртуальной машины мы получим маркер доступа для проверки подлинности в Key Vault:
 
1. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**.
2. Введите **имя пользователя** и **пароль**, добавленные при создании **виртуальной машины Windows**.  
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе.  
4. Отправьте веб-запрос к клиенту в PowerShell, чтобы получить токен для локального узла на конкретный порт для виртуальной машины.  

    Запрос PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Затем извлеките полный ответ, который хранится в виде форматированной строки JSON в объекте $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Затем извлеките маркер доступа из ответа.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Наконец используйте команду Invoke-WebRequest PowerShell для извлечения секрета, созданного ранее в хранилище Key Vault, и передайте маркер доступа в заголовок авторизации.  Вам потребуется URL-адрес вашего хранилища ключей, который находится в разделе **Основные компоненты** на странице **Обзор** в этом хранилище.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Ответ будет выглядеть следующим образом: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
После получения секрета из хранилища Key Vault, его можно использовать для аутентификации в службе, требующей имя пользователя и пароль. 

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать компонент "Управляемое удостоверение службы" и получить доступ к Azure Key Vault.  Дополнительные сведения об Azure Key Vault см. здесь:

> [!div class="nextstepaction"]
>[Хранилище ключей Azure](/azure/key-vault/key-vault-whatis)
