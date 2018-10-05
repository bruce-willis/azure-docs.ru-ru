---
title: Использование назначаемого системой управляемого удостоверения на виртуальной машине Linux для доступа к Azure Key Vault
description: Из этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux.
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
ms.openlocfilehash: 274005d7cea5833f9653038b710a7dbc248f0c04
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221525"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Руководство. Использование назначаемого системой управляемого удостоверения на виртуальной машине Linux для доступа к Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описано, как получить доступ к Azure Key Vault с помощью назначаемого системой управляемого удостоверения для виртуальной машины Linux. При применении в качестве начальной загрузки хранилище Key Vault разрешает клиентскому приложению использовать секрет для доступа к ресурсам, которые не защищены с помощью Azure Active Directory (AD). Управляемыми удостоверениями для ресурсов Azure автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * предоставлять виртуальной машине доступ к секрету в Key Vault; 
> * Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для получения секрета из Key Vault. 
 
## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Linux](/azure/virtual-machines/linux/quick-create-portal).

- [Включите назначаемое системой управляемое удостоверение на виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Предоставление виртуальной машине доступа к секрету в Key Vault  

С помощью управляемых удостоверений службы для ресурсов Azure код может получить маркеры доступа, чтобы пройти проверку подлинности и получить доступ к ресурсам, поддерживающим аутентификацию Azure Active Directory. Однако не все службы Azure поддерживают аутентификацию Azure AD. Чтобы использовать управляемые удостоверения для ресурсов Azure с такими службами, сохраните учетные данные службы в Azure Key Vault и используйте управляемые удостоверения ресурсов Azure для доступа к Key Vault и получения учетных данных. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и его использование для получения секрета из Key Vault.  

Для выполнения этих действий вам потребуется клиент SSH.  Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**. 
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала выполните запрос к управляемым удостоверениям для локальной конечной точки ресурсов Azure с помощью CURL, чтобы получить маркер доступа для Azure Key Vault.  
 
    Запрос cURL маркера доступа приведен ниже.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Ответ включает маркер доступа, необходимый для доступа к Resource Manager. 
    
    Ответ:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Этот маркер доступа можно использовать для аутентификации в Azure Key Vault.  В следующем запросе cURL показано, как считать секрет из хранилища Key Vault с помощью cURL и REST API для Key Vault.  Вам потребуется URL-адрес вашего хранилища ключей, который находится в разделе **Основные компоненты** на странице **Обзор** в этом хранилище.  а также маркер доступа, полученный ранее. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Ответ будет выглядеть следующим образом: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
После получения секрета из хранилища Key Vault, его можно использовать для аутентификации в службе, требующей имя пользователя и пароль.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как получить доступ к Azure Key Vault с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux.  Дополнительные сведения об Azure Key Vault см. здесь:

> [!div class="nextstepaction"]
>[Хранилище ключей Azure](/azure/key-vault/key-vault-whatis)




