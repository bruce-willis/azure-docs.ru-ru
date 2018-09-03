---
title: Как получить доступ к Azure Data Lake Store с помощью Управляемого удостоверения службы виртуальной машины Windows
description: В руководстве показано, как получить доступ к Azure Data Lake Store с помощью Управляемого удостоверения службы виртуальной машины Windows.
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
ms.openlocfilehash: a472a0f1fe052b0bc8130f5d81c91692c7723377
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885894"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-data-lake-store"></a>Руководство. Получение доступа к Azure Data Lake Store с помощью Управляемого удостоверения службы виртуальной машины Windows

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описывается, как получить доступ к Azure Data Lake Store с помощью удостоверения виртуальной машины Windows, назначаемого системой. Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Предоставление виртуальной машине доступа к Azure Data Lake Store.
> * Получение маркера доступа с использованием удостоверения виртуальной машины и получение доступа к Azure Data Lake Store с его помощью.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Предоставление виртуальной машине доступа к Azure Data Lake Store

Теперь можно предоставить виртуальной машине доступ к файлам и папкам в Azure Data Lake Store.  Для выполнения этого шага можно использовать имеющееся хранилище Azure Data Lake Store или создать новое.  Чтобы создать новое хранилище Data Lake Store с помощью портала Azure, следуйте этому [краткому руководству по Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). В [документации по Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) также есть краткие руководства, описывающие использование Azure CLI и Azure PowerShell.

Создайте новую папку в Data Lake Store и предоставьте разрешение Управляемому удостоверению службы виртуальной машины на чтение, запись и выполнение файлов в этой папке.

1. На портале Azure щелкните **Data Lake Store** в области навигации слева.
2. Выберите хранилище Data Lake Store, которое хотите использовать для этого руководства.
3. Щелкните **Обозреватель данных** на панели команд.
4. Выбрана корневая папка Data Lake Store.  На панели команд щелкните **Доступ**.
5. Щелкните **Добавить**.  В поле **Выбор** введите имя виртуальной машины, например **DevTestVM**.  Выберите свою виртуальную машину в результатах поиска, а затем щелкните **Выбрать**.
6. Щелкните **Выбор разрешений**.  Выберите разрешения **Чтение** и **Выполнение** и добавьте их в раздел **Эта папка**, затем добавьте их в качестве **разрешений только для доступа**.  Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.
7. Закройте колонку **Доступ**.
8. В рамках этого руководства мы создадим папку.  Щелкните **Создать папку** в командной строке и введите имя новой папки, например **TestFolder**.  Нажмите кнопку **ОК**.
9. Щелкните созданную папку, а затем щелкните **Доступ** на панели команд.
10. Как и на шаге 5, нажмите кнопку **Добавить**, в поле **Выбор** введите имя виртуальной машины, выберите ее и нажмите кнопку **Выбрать**.
11. Как и на шаге 6, щелкните **Выбор разрешений**, выберите разрешения **Чтение**, **Запись** и **Выполнение**, добавьте их в раздел **Эта папка**, после чего добавьте их в качестве **записи разрешения доступа и записи разрешений по умолчанию**.  Нажмите кнопку **ОК**.  Разрешения должны быть успешно добавлены.

Теперь Управляемое удостоверение службы виртуальной машины позволит выполнять все операции с файлами в созданной папке.  Дополнительные сведения об управлении доступом к Data Lake Store см. в статье [Контроль доступа в Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-managed-service-identity-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Получение маркера доступа с использованием Управляемого удостоверения службы виртуальной машины и выполнение вызова файловой системы Azure Data Lake Store с его помощью

В хранилище Azure Data Lake Store реализована поддержка аутентификации Azure AD, поэтому она может напрямую принимать маркеры доступа, полученные с использованием Управляемого удостоверения службы.  Для аутентификации в файловой системе Data Lake Store маркер доступа, выданный службой Azure AD конечной точке файловой системы Data Lake Store, отправляется в заголовке авторизации в формате "Bearer <ЗНАЧЕНИЕ_МАРКЕРА_ДОСТУПА>".  Дополнительные сведения о поддержке Data Lake Store для аутентификации Azure AD см. в разделе [Аутентификация в Data Lake Store с помощью Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

> [!NOTE]
> Клиентские пакеты SDK для файловой системы Data Lake Store еще не поддерживают управляемое удостоверение службы.  Это руководство будет обновлено, как только поддержка будет добавлена в пакет SDK.

В этом руководстве выполняется аутентификация в файловой системе Data Lake Store посредством REST API с использованием PowerShell для выполнения запросов REST. Чтобы использовать Управляемое удостоверение службы виртуальной машины для проверки подлинности, необходимо отправлять запросы с виртуальной машины.

1. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**.
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе. 
4. С помощью команды PowerShell `Invoke-WebRequest` выполните запрос к локальной конечной точке Управляемого удостоверения службы, чтобы получить маркер доступа к Azure Data Lake Store.  Идентификатор ресурса для Data Lake Store: https://datalake.azure.net/.  Data Lake проводит точное сопоставление с идентификатором ресурса, и косая черта в конце важна.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Преобразуйте ответ из объекта JSON в объект PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Извлеките маркер доступа из ответа.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. С помощью команды PowerShell Invoke-WebRequest выполните запрос к конечной точке REST Data Lake Store, чтобы вывести список папок в корневой папке.  Это простой способ проверить, все ли настроено правильно.  Очень важно, чтобы слово Bearer в заголовке авторизации начиналось с заглавной буквы B.  Имя хранилища Data Lake Store можно найти в разделе **Обзор** колонки "Data Lake Store" на портале Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Успешный ответ выглядит следующим образом.

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Теперь вы можете попробовать передать файл в Data Lake Store.  Сначала создайте файл для передачи.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. С помощью команды PowerShell `Invoke-WebRequest` выполните запрос к конечной точке REST Data Lake Store, чтобы передать файл в папку, созданную ранее.  Этот запрос выполняется в два этапа.  На первом этапе вы создаете запрос и получаете адрес перенаправления для передачи файла.  На втором этапе вы фактически передаете файл.  Не забудьте задать имена папки и файла соответствующим образом, если вы использовали значения, отличные от приведенных в этом руководстве. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Если проверить значение ответа `$HdfsRedirectResponse`, оно должно иметь следующий вид.

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Выполните передачу, отправив запрос к конечной точке перенаправления.

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Успешный ответ выглядит следующим образом.

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

С помощью других интерфейсов API файловой системы Data Lake Store можно добавлять содержимое к файлам, скачивать файлы и многое другое.

Поздравляем!  Вы прошли проверку подлинности в файловой системе Data Lake Store с помощью Управляемого удостоверения службы виртуальной машины.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как получить доступ к Azure Data Lake Store с помощью компонента "Управляемое удостоверение службы" виртуальной машины Windows. Дополнительные сведения об Azure Data Lake Store см. здесь:

> [!div class="nextstepaction"]
>[Хранилище озера данных Azure](/azure/data-lake-store/data-lake-store-overview)
