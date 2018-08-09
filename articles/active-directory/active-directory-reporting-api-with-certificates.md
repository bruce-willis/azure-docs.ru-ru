---
title: Получение данных, используя API отчетов Azure AD с сертификатами | Документация Майкрософт
description: Описание использования API отчетов Azure AD с учетными данными сертификатов для получения данных из каталогов без вмешательства пользователя.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 44ec19721ba59898915f6547231fb586cb44eb30
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389758"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Получение данных с помощью API отчетов Azure Active Directory с сертификатами

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](active-directory-reporting-api-getting-started-azure-portal.md) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования. Чтобы получить доступ к API отчетов Azure AD без вмешательства пользователя, можно настроить доступ с помощью сертификатов.

Для этого необходимо выполнить следующие шаги.

1. [Установка необходимых компонентов](#install-prerequisites)
2. [Регистрация сертификата в приложении](#register-the-certificate-in-your-app)
3. [Получение маркера доступа для API Microsoft Graph](#get-an-access-token-for-ms-graph-api)
4. [Запрос конечных точек API Microsoft Graph](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>установить необходимые компоненты;

1. Сначала убедитесь в том, что вы выполнили [предварительные требования для доступа к API отчетов Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Скачайте и установите Azure AD PowerShell V2, следуя инструкциям, описанным в статье [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

3. Установите MSCloudIDUtils со страницы [PowerShellGallery — MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Этот модуль предоставляет несколько служебных командлетов, позволяющих получить:
    - Библиотеки ADAL, необходимые для проверки подлинности
    - маркеры доступа пользователя, ключи приложений и сертификаты с использованием ADAL;
    - обработку результатов с разбивкой на страницы с помощью API Graph.

4. Если вы используете модуль впервые, выполните командлет **Install-MSCloudIdUtilsModule**, чтобы завершить установку. В противном случае его можно просто импортировать с помощью команды PowerShell **Import-Module**.

Сеанс должен выглядеть так, как показано ниже.

  ![Windows PowerShell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Регистрация сертификата в приложении

1. Сначала перейдите на страницу регистрации приложений. Для этого можно перейти на [портал Azure](https://portal.azure.com), щелкнуть элемент **Azure Active Directory**, затем щелкнуть **Регистрация приложений** и выбрать приложение в списке. 

2. После этого выполните инструкции по [регистрации сертификата в Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) для приложения. 

3. Запишите идентификатор приложения и отпечаток сертификата, который вы только что зарегистрировали для приложения. Чтобы найти отпечаток, на странице приложения на портале щелкните **Параметры** и выберите пункт **Ключи**. Отпечаток будет указан в списке **Открытые ключи**.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Получение маркера доступа для API Microsoft Graph

Чтобы получить маркер доступа для API Microsoft Graph, используйте командлет **Get-MSCloudIdMSGraphAccessTokenFromCert** из модуля PowerShell MSCloudIdUtils. 

>[!NOTE]
>Необходимо использовать идентификатор приложения (также называемый ClientID) и отпечаток сертификата с закрытым ключом, установленного в хранилище сертификатов компьютера (хранилище CurrentUser или LocalMachine).
>

 ![Портал Azure](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>использовать маркер доступа для вызова API Graph.

Теперь маркер доступа можно использовать в скрипте PowerShell для запроса API Graph. Ниже приведен пример использования командлета **Invoke-MSCloudIdMSGraphQuery** из модуля MSCloudIDUtils для перечисления операций входа или запроса конечной точки diectoryAudits. Этот командлет обрабатывает результаты с разбивкой на несколько страниц, а затем отправляет их в конвейер PowerShell.

### <a name="query-the-directoryaudits-endpoint"></a>Запрос конечной точки DirectoryAudits
 ![Портал Azure](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Запрос конечной точки SignIns
 ![Портал Azure](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Теперь можно экспортировать эти данные в CSV-файл и сохранить его в системе SIEM. Также можно перенести скрипт в запланированную задачу, чтобы периодически получать данные Azure AD из клиента без необходимости сохранять ключи приложений в исходном коде. 


## <a name="next-steps"></a>Дополнительная информация

* [Ознакомление с API отчетов](active-directory-reporting-api-getting-started-azure-portal.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



