---
title: Использование назначаемого системой управляемого удостоверения на виртуальной машине Linux для доступа к API Graph AAD
description: Из этого руководства вы узнаете, как получить доступ к API Graph AAD с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux.
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
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 548111a6c2b9e0cf8c5b20eee5cc8fa45fe02da8
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47453121"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Руководство. Использование назначаемого системой управляемого удостоверения на виртуальной машине Linux для доступа к API Graph AAD

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice.md)]

В этом руководстве описано, как получить доступ к API Graph AAD с помощью назначенного системой управляемого удостоверения на виртуальной машине Linux для получения сведений о членстве в группах. Управляемыми удостоверениями для ресурсов Azure автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure Active Directory, без указания учетных данных в коде.  

В этом руководстве вы выполните запрос членства удостоверения виртуальной машины в группах AAD. Сведения о группе часто используются в решениях об авторизации. На уровне платформы AAD управляемое удостоверение виртуальной машины представляется **субъектом-службой**. 

> [!div class="checklist"]
> * Подключение к Azure AD
> * Добавление удостоверения виртуальной машины в группу Azure AD 
> * Предоставление удостоверению виртуальной машины доступа к Azure AD Graph 
> * Получение маркера доступа с использованием удостоверения виртуальной машины и применение маркера в вызове к API Graph AAD

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Linux](/azure/virtual-machines/linux/quick-create-portal).

- [Включите назначаемое системой управляемое удостоверение на виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

- [Установите последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Чтобы предоставить удостоверению виртуальной машины доступ к Azure AD Graph, необходимо назначить вашей учетной записи роль **глобального администратора** в Azure AD.

## <a name="connect-to-azure-ad"></a>Подключение к Azure AD

Необходимо подключиться к Azure AD, чтобы назначить виртуальную машину в группу, а также предоставить виртуальной машине разрешение на получение членства в группах.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Добавление удостоверения виртуальной машины в группу Azure AD

При включении назначаемого системой управляемого удостоверения на виртуальной машине Linux оно создает субъект-службу в Azure AD.  Теперь необходимо добавить виртуальную машину в группу. Инструкции по добавлению виртуальной машины в группу AAD вы найдете в следующей статье:

- [Добавление членов группы](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Предоставление виртуальной машине доступа к API Graph в Azure AD

С помощью управляемых удостоверений для ресурсов Azure код может получить маркеры доступа, чтобы пройти проверку подлинности и получить доступ к ресурсам, поддерживающим аутентификацию Azure AD. API Graph в Microsoft Azure AD поддерживает проверку подлинности AAD. На этом шаге вы предоставите субъекту-службе удостоверения виртуальной машины доступ к Azure AD Graph, чтобы он мог запрашивать членство в группах. Субъектам-службам доступ к Microsoft или Azure AD Graph предоставляется с помощью **разрешений приложения**. Тип разрешения приложения, которое требуется для предоставления доступа, зависит от сущности, к которой необходимо получить доступ в Microsoft или Azure AD Graph.

В этом руководстве вы предоставите удостоверению виртуальной машины возможность запрашивать членство в группах с помощью разрешения приложения `Directory.Read.All`. Чтобы предоставить это разрешение, необходима учетная запись пользователя, которой назначена роль глобального администратора в Azure AD. Как правило, чтобы предоставить разрешение приложения, можно открыть страницу регистрации приложения на портале Azure и добавить на ней разрешение. Однако управляемые удостоверения для ресурсов Azure не регистрируют в AAD объекты приложений, а только субъекты-службы. Чтобы зарегистрировать разрешение приложения, воспользуемся средством командной строки AAD PowerShell. 

Azure AD Graph:
- Идентификатор приложения субъекта-службы (используется при предоставлении разрешения приложения): 00000002-0000-0000-c000-000000000000
- Идентификатор ресурса (используется при запрашивании маркера доступа из управляемых удостоверений для ресурсов Azure): https://graph.windows.net
- Справочник по областям разрешений: [Справочник по разрешениям Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Предоставление разрешений приложения с помощью CURL

1. Получение маркера для выполнения запросов CURL:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Вам нужно получить и записать `objectId` для виртуальной машины. Он потребуется на последующих шагах для предоставления виртуальной машине разрешений на считывание членства в группах. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили на предыдущем шаге.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. С помощью идентификатора приложения Azure AD Graph со значением 00000002-0000-0000-c000-000000000000 извлеките `objectId` для `odata.type: Microsoft.DirectoryServices.ServicePrincipal` и `id` для разрешения роли приложения `Directory.Read.All` и запишите эти значения.  Замените `<ACCESS TOKEN>` маркером доступа, который вы получили ранее.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Ответ:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Теперь предоставьте субъекту-службе виртуальной машины права на чтение из объектов каталога AAD через API Graph AAD.  Значение `id` является значением для разрешение роли приложения `Directory.Read.All`, а `resourceId` — `objectId` для субъекта-службы `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (эти значения вы записали на предыдущем шаге).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-ad-graph"></a>Получение маркера доступа с использованием удостоверения виртуальной машины и применение маркера в вызове к API Graph AAD 

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале перейдите на виртуальную машину Linux и в разделе **Обзор** щелкните **Подключиться**.  
2. **Подключитесь** к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала выполните запрос к управляемым удостоверениям для локальной конечной точки ресурсов Azure с помощью CURL, чтобы получить маркер доступа к API Graph в AAD.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   Ответ содержит маркер доступа, необходимый для доступа к API Graph AAD.

   Ответ:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Используя идентификатор объекта субъекта-службы удостоверения виртуальной машины (который вы получили ранее), можно выполнить запрос к API Graph для Azure AD для получения членства в группах. Замените `<OBJECT-ID>` значением идентификатора объекта для субъекта-службы виртуальной машины, а `<ACCESS-TOKEN>` — полученным ранее маркером доступа:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Ответ:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как получить доступ к API Graph AAD с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux.  Дополнительные сведения об Azure AD Graph см. в следующей статье:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)