---
title: Доступ к службе хранилища Azure с помощью Управляемого удостоверения службы виртуальной машины Windows
description: В этом руководстве описано, как получить доступ к службе хранилища Azure с помощью Управляемого удостоверения службы виртуальной машины Windows.
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
ms.openlocfilehash: 12e17977d010b460681f72e62fb72e07ad713a3a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887530"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Руководство. Получение доступа к службе хранилища Azure с помощью ключа доступа и управляемого удостоверения службы виртуальной машины Windows

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описывается, как получить ключи доступа для учетной записи хранения с помощью удостоверения виртуальной машины Windows, назначаемого системой. Вы можете использовать ключи доступа к хранилищу стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK для службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью PowerShell службы хранилища Azure. Вы научитесь:


> [!div class="checklist"]
> * Создание учетной записи хранения
> * предоставлять виртуальной машине доступ к ключам учетной записи хранения в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения ключей доступа к хранилищу из Resource Manager. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="create-a-storage-account"></a>Создание учетной записи хранения 

Если у вас еще нет учетной записи хранения, создайте ее. Этот шаг можно пропустить и предоставить доступ Управляемому удостоверению службы виртуальной машины к ключам существующей учетной записи хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите имя учетной записи хранения, которая будет использоваться далее.  
4. Для параметра **Модель развертывания** необходимо выбрать "Resource Manager", а для параметра **Account kind** (Тип учетной записи) — "Общее назначение". 
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.
6. Нажмите кнопку **Создать**.

    ![Создание учетной записи хранения](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Создание контейнера больших двоичных объектов в учетной записи хранения

Позже мы отправим и скачаем файл в новую учетную запись хранения. Так как файлам необходимо хранилище BLOB-объектов, нужно создать контейнер больших двоичных объектов, в котором будет храниться файл.

1. Вернитесь к только что созданной учетной записи хранения.
2. Щелкните ссылку **Контейнеры** слева в разделе "Служба BLOB-объектов".
3. Щелкните **+ Контейнер** в верхней части страницы, после чего появится панель "Новый контейнер".
4. Присвойте контейнеру имя, выберите уровень доступа, а затем нажмите кнопку **ОК**. Указанное имя будет использоваться далее в этом руководстве. 

    ![Создание контейнера хранилища](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-storage-account-access-keys"></a>Предоставление Управляемому удостоверению службы виртуальной машины прав на использование ключей доступа для учетных записей хранения 

В службе хранилища Azure не встроена поддержка проверки подлинности Azure AD.  Тем не менее Управляемое удостоверение службы можно использовать для извлечения ключей доступа для учетных записей хранения из Resource Manager и последующего доступа к хранилищу с помощью ключа.  На этом шаге мы предоставим доступ Управляемому удостоверению службы виртуальной машины к ключам для учетной записи хранения.   

1. Вернитесь к только что созданной учетной записи хранения.  
2. Щелкните ссылку **Управление доступом (IAM)** на панели слева.  
3. Щелкните **+ Добавить** в верхней части страницы, чтобы добавить новое назначение роли для виртуальной машины.
4. Для параметра **Роль** в правой части страницы выберите значение "Роль службы оператора ключей учетной записи хранения". 
5. Рядом с ним в раскрывающемся списке **Назначение доступа к** выберите ресурс "Виртуальная машина".  
6. Убедитесь, что нужная подписка присутствует в раскрывающемся списке **Подписки**, и установите для параметра **Группы ресурсов** значение "Все группы ресурсов".  
7. И наконец, в разделе **Выбрать** выберите в раскрывающемся списке нужную виртуальную машину Windows и нажмите кнопку **Сохранить**. 

    ![Замещающий текст](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали. 

На этом этапе вы примените командлеты PowerShell для Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview), прежде чем продолжить.

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и вверху в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе.
4. С помощью командлета PowerShell Invoke-WebRequest выполните запрос к локальной конечной точке управляемого удостоверения службы, чтобы получить маркер доступа к Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките элемент content, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Получение ключей учетной записи хранения из Azure Resource Manager для обращения к хранилищу  

Теперь мы с помощью PowerShell обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к хранилищу. После получения ключа доступа к хранилищу можно вызвать операции отправки или скачивания в хранилище.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL-адрес чувствителен к регистру, поэтому должен использоваться тот же регистр, который использовался, когда вы присваивали имя группе ресурсов. Проверьте, чтобы в resourceGroups обязательно использовался символ "G" (прописная буква). 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Затем нужно создать файл с именем "test.txt". Используйте ключ доступа к хранилищу для аутентификации с помощью командлета `New-AzureStorageContent`, отправьте файл в контейнер больших двоичных объектов, а затем скачайте его.

```bash
echo "This is a test text file." > test.txt
```

Не забудьте сначала установить командлеты службы хранилища Azure с помощью `Install-Module Azure.Storage`, а затем отправьте созданный большой двоичный объект с помощью командлета PowerShell `Set-AzureStorageBlobContent`.

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Скачать отправленный большой двоичный объект с помощью командлета PowerShell `Get-AzureStorageBlobContent`.

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Ответ:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать компонент "Управляемое удостоверение службы" и получить доступ к службе хранилища Azure с помощью ключа доступа.  Дополнительные сведения о ключах доступа к службе хранилища Azure см. здесь:

> [!div class="nextstepaction"]
>[Управление ключами доступа к хранилищу](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)

