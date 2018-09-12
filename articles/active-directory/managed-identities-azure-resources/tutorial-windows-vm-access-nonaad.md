---
title: Использование назначенного системой управляемого удостоверения на виртуальной машине Windows для доступа к Azure Key Vault
description: Из этого руководства вы узнаете, как получить доступ к хранилищу Azure Key Vault с помощью назначенного системой управляемого удостоверения на виртуальной машине Windows.
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
ms.openlocfilehash: fffa77990b0af3c710a60d2077962257ab50d5e7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163352"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Руководство: использование назначенного системой управляемого удостоверения на виртуальной машине Windows для доступа к Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описано, как получить доступ к Azure Key Vault с помощью назначенного системой управляемого удостоверения на виртуальной машине Windows. При применении в качестве начальной загрузки хранилище Key Vault разрешает клиентскому приложению использовать секрет для доступа к ресурсам, которые не защищены с помощью Azure Active Directory (AD). Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. 

Вы узнаете, как выполнять следующие задачи:


> [!div class="checklist"]
> * предоставлять виртуальной машине доступ к секрету в Key Vault; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для получения секрета из Key Vault. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Включите назначаемое системой управляемое удостоверение на виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Предоставление виртуальной машине доступа к секрету в Key Vault 
 
С помощью управляемых удостоверений для ресурсов Azure код может получить маркеры доступа, чтобы пройти проверку подлинности и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD.  Однако не все службы Azure поддерживают аутентификацию Azure AD. Чтобы использовать управляемые удостоверения для ресурсов Azure с такими службами, сохраните учетные данные службы в Azure Key Vault и используйте управляемое удостоверение виртуальной машины для доступа к Key Vault и получения учетных данных. 

Сначала нужно создать хранилище Key Vault и предоставить удостоверению виртуальной машины доступ к нему.   

1. В верхней части меню навигации слева последовательно выберите **Создать ресурс** > **Безопасность+идентификация** > **Key Vault**.  
2. Укажите **имя** для нового хранилища Key Vault. 
3. Найдите хранилище Key Vault в той же подписке и группе ресурсов, где ранее была создана виртуальная машина. 
4. Выберите **Политики доступа** и щелкните **Добавить**. 
5. В раскрывающемся списке Configure from template (Настроить на основе шаблона) выберите **Управление секретами**. 
6. Выберите **Выбор субъекта** и в поле поиска введите имя созданной ранее виртуальной машины.  Выберите виртуальную машину в списке результатов и щелкните **Выбрать**. 
7. Чтобы завершить добавление новой политики доступа, нажмите кнопку **ОК**, а затем нажмите кнопку **ОК**, чтобы завершить выбор политики доступа. 
8. Чтобы завершить создание хранилища ключей, нажмите кнопку **Создать**. 

    ![Замещающий текст](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Затем добавьте секрет в хранилище Key Vault, чтобы позже извлечь его с помощью кода, который вы запустите на виртуальной машине: 

1. Щелкните **Все ресурсы**, а затем найдите и выберите созданное хранилище Key Vault. 
2. Выберите **Секреты** и щелкните **Добавить**. 
3. В разделе **Параметры отправки** выберите **Вручную**. 
4. Введите имя и значение для секрета.  Значение может быть любым. 
5. Не указывайте дату активации и окончания срока действия и для параметра **Включено** оставьте значение **Да**. 
6. Щелкните **Создать**, чтобы создать секрет. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для получения секрета из Key Vault.  

Если вы не установили версию PowerShell 4.3.1 или выше, [загрузите и установите последнюю версию](https://docs.microsoft.com/powershell/azure/overview).

Сначала получим маркер доступа для аутентификации в Key Vault с помощью управляемого удостоверения виртуальной машины:
 
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

Из этого руководства вы узнали, как получить доступ к хранилищу Azure Key Vault с помощью назначенного системой управляемого удостоверения на виртуальной машине Windows.  Дополнительные сведения об Azure Key Vault см. здесь:

> [!div class="nextstepaction"]
>[Хранилище ключей Azure](/azure/key-vault/key-vault-whatis)
