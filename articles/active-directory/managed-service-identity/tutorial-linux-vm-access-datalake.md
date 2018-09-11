---
title: Использование назначаемого системой управляемого удостоверения на виртуальной машине Linux для доступа к Azure Data Lake Storage
description: В этом руководстве описано, как получить доступ к Azure Data Lake Storage с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 583c94c6e065fb07704ac4470534b364b57e98ab
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341382"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Руководство. Получение доступа к Azure Data Lake Storage с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описано, как получить доступ к Cosmos DB с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux. Вы узнаете, как выполнять следующие задачи: 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Предоставление виртуальной машине доступа к Azure Data Lake Store.
> * Получение маркера доступа и получение доступа к Azure Data Lake Storage с помощью назначаемого системой управляемого удостоверения виртуальной машины.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Linux](/azure/virtual-machines/linux/quick-create-portal).

- [Включите назначаемое системой удостоверение на виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Предоставление виртуальной машине доступа к Azure Data Lake Store

Теперь можно предоставить виртуальной машине доступ к файлам и папкам в Azure Data Lake Store. Для выполнения этого шага можно использовать имеющийся экземпляр хранилища Data Lake Store или создать новый. Чтобы создать новый экземпляр хранилища Data Lake Store с помощью портала Azure, следуйте указаниям в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). В статье [Обзор Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) также есть краткие руководства по использованию Azure CLI и Azure PowerShell.

Создайте папку в Data Lake Storage и предоставьте назначаемому системой управляемому удостоверению виртуальной машины Linux разрешение на чтение, запись и выполнение файлов в этой папке:

1. В левой области на портале Azure выберите **Data Lake Store**.
2. Выберите экземпляр Data Lake Store, который необходимо использовать.
3. Щелкните **Обозреватель данных** на панели команд.
4. Будет выбрана корневая папка экземпляра Data Lake Store. На панели команд щелкните **Доступ**.
5. Выберите **Добавить**.  В поле **Выбрать** введите имя виртуальной машины, например **DevTestVM**. Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
6. Щелкните **Выбор разрешений**.  Выберите разрешения **Чтение** и **Выполнение** и добавьте их в раздел **Эта папка**, затем добавьте их в качестве **разрешений только для доступа**. Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.
7. Закройте панель **Доступ**.
8. В рамках этого руководства мы создадим папку. На панели команд щелкните **Создать папку** и введите имя новой папки, например **TestFolder**.  Нажмите кнопку **ОК**.
9. Выберите созданную папку, а затем на панели команд щелкните **Доступ**.
10. Как и на шаге 5, щелкните **Добавить**. В поле **Выбрать** введите имя виртуальной машины. Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
11. Как и на шаге 6, щелкните **Выберите разрешения**. Выберите разрешения **Чтение**, **Запись** и **Выполнение**, добавьте их в раздел **Эта папка**, после чего установите для них значение **Запись разрешений доступа и запись разрешений по умолчанию**. Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.

Теперь управляемые удостоверения для ресурсов Azure могут выполнять все операции с файлами в созданной папке. Дополнительные сведения об управлении доступом к Data Lake Store см. в статье [Контроль доступа в Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Получение маркера доступа и вызов файловой системы Data Lake Store

В репозитории Azure Data Lake Storage реализована поддержка аутентификации Azure AD, поэтому он может напрямую принимать маркеры доступа, полученные с помощью управляемых удостоверений для ресурсов Azure. Для аутентификации в файловой системе Data Lake Store маркер доступа, выданный службой Azure AD, отправляется конечной точке файловой системы Data Lake Store. Маркер доступа отправляется в заголовок авторизации в формате "Bearer \<ЗНАЧЕНИЕ_МАРКЕРА_ДОСТУПА\>".  Дополнительные сведения о поддержке Data Lake Store для аутентификации Azure AD см. в статье [Аутентификация в Data Lake Store с помощью Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

В этом руководстве выполняется аутентификация в файловой системе Data Lake Store посредством REST API с использованием CURL для выполнения запросов REST.

> [!NOTE]
> Клиентские пакеты SDK файловой системы Data Lake Storage пока не поддерживают управляемые удостоверения для ресурсов Azure.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь со статьей [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. На портале перейдите к виртуальной машине Linux. В разделе **Обзор** выберите **Подключиться**.  
2. Подключитесь к виртуальной машине с помощью выбранного клиента SSH. 
3. В окне терминала с помощью cURL выполните запрос к локальной конечной точке управляемого удостоверения для ресурсов Azure, чтобы получить маркер доступа для файловой системы Data Lake Storage. Идентификатор ресурса для Data Lake Store: https://datalake.azure.net/.  Очень важно добавить в идентификатор ресурса конечную косую черту.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Успешный ответ содержит маркер доступа, используемый для аутентификации в Data Lake Store.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы вывести список папок в корневой папке. Это простой способ проверить, все ли настроено правильно. Скопируйте значение маркера доступа из предыдущего шага. Очень важно, чтобы слово Bearer в заголовке авторизации начиналось с заглавной буквы B. Имя экземпляра хранилища Data Lake Store можно найти в разделе **Обзор** панели **Data Lake Store** на портале Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Успешный ответ выглядит следующим образом.

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Теперь вы можете попробовать передать файл в экземпляр Data Lake Store. Сначала создайте файл для передачи.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. С помощью CURL выполните запрос к конечной точке REST файловой системы Data Lake Store, чтобы передать файл в папку, созданную ранее. Передача подразумевает перенаправление, и CURL выполняет перенаправление автоматически. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Успешный ответ выглядит следующим образом.

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

С помощью других интерфейсов API файловой системы Data Lake Store можно добавлять содержимое к файлам, скачивать файлы и многое другое.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как получить доступ к Azure Data Lake с помощью назначаемого системой управляемого удостоверения на виртуальной машине Linux. Дополнительные сведения об Azure Data Lake Store см. здесь:

> [!div class="nextstepaction"]
>[Хранилище озера данных Azure](/azure/data-lake-store/data-lake-store-overview)
