---
title: Использование Управляемого удостоверения службы виртуальной машины Linux для доступа к Службе хранилища Azure
description: Из этого руководства вы узнаете, как получить доступ к Службе хранилища Azure с помощью Управляемого удостоверения службы виртуальной машины Linux.
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
ms.openlocfilehash: 97009f526d405fe99fc732963e44c607f53018b6
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887822"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Руководство. Получение доступа к службе хранилища Azure с помощью ключа доступа и управляемого удостоверения службы виртуальной машины Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описывается, как получить ключи доступа для учетной записи хранения с помощью удостоверения виртуальной машины Linux, назначаемого системой. Вы можете использовать ключ доступа к хранилищу стандартным образом при выполнении операций с хранилищем, например при использовании пакета SDK для службы хранилища Azure. В рамках этого руководства мы отправим и скачаем большие двоичные объекты с помощью Azure CLI. Вы научитесь:

> [!div class="checklist"]
> * Включение Управляемого удостоверения службы на виртуальной машине Linux 
> * предоставлять виртуальной машине доступ к ключам учетной записи хранения в Resource Manager; 
> * получать маркер доступа с помощью удостоверения виртуальной машины и использовать его для извлечения ключей доступа к хранилищу из Resource Manager.  

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Linux](/azure/virtual-machines/linux/quick-create-portal).

- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="create-a-storage-account"></a>Создание учетной записи хранения 

Если у вас еще нет учетной записи хранения, создайте ее.  Этот шаг можно пропустить и предоставить доступ Управляемому удостоверению службы виртуальной машины к ключам существующей учетной записи хранения. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Щелкните **Хранилище**, а затем **Учетная запись хранения**, после чего отобразится новая панель "Создание учетной записи хранения".
3. Введите **имя** учетной записи хранения, которая будет использоваться далее.  
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
7. И, наконец, в разделе **Выбрать** выберите в раскрывающемся списке нужную виртуальную машину Linux и нажмите кнопку **Сохранить**. 

    ![Замещающий текст](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Azure Resource Manager с его помощью

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Linux и вверху в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине. 
2. Подключитесь к виртуальной машине c помощью клиента SSH.  
3. После этого вам предложат ввести **пароль**, добавленный при создании **виртуальной машины Linux**. Вы должны без проблем войти в систему.  
4. Используйте cURL для получения маркера доступа для Azure Resource Manager.  

    Запрос CURL маркера доступа и соответствующий ответ приведены ниже:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > В предыдущем запросе значение параметра resource должно точно совпадать со значением, которое ожидает Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    > В следующем ответе элемент access_token сокращен для удобства восприятия.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Получение ключей учетной записи хранения из Azure Resource Manager для обращения к хранилищу  

Теперь мы с помощью CURL обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к хранилищу. После получения ключа доступа к хранилищу можно вызвать операции отправки или скачивания в хранилище. Не забудьте заменить значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<STORAGE ACCOUNT NAME>` своими значениями. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили ранее:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Текст в приведенном выше URL-адресе чувствителен к регистру, поэтому используйте строчные и заглавные буквы, как указано в названиях групп ресурсов. Кроме того, убедитесь, что используется запрос POST, а не GET, а также, что вы передали значение для регистрации максимальной длины с помощью параметра -d, который может иметь значение NULL.  

В ответе cURL будет содержаться список ключей:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Создайте образец файла большого двоичного объекта, чтобы отправить его в контейнер хранилища BLOB-объектов. На виртуальной машине Linux это можно сделать с помощью следующей команды. 

```bash
echo "This is a test file." > test.txt
```

Теперь выполните аутентификацию с помощью команды CLI `az storage`, указав в ней ключ доступа к хранилищу. Отправьте файл в контейнер больших двоичных объектов. Для выполнения этого шага нужно [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) на виртуальной машине, если вы не сделали этого ранее.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Ответ: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Кроме того, можно скачать файл с помощью Azure CLI и выполнить аутентификацию, используя ключ доступа к хранилищу. 

Запрос: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Ответ: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы узнали, как получить доступ к службе хранилища Azure с помощью ключа доступа, используя компонент "Управляемое удостоверение службы" виртуальной машины Linux.  Дополнительные сведения о ключах доступа к службе хранилища Azure см. здесь:

> [!div class="nextstepaction"]
>[Управление ключами доступа к хранилищу](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)
