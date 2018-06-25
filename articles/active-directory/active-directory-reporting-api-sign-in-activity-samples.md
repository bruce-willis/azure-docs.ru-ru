---
title: Примеры для API отчета о событии входа в Azure Active Directory | Документация Майкрософт
description: Как начать работу с API отчетов Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 52d49770014a5fb6a5eec644868e702c8a8d9ef3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224904"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Примеры для API отчета о действиях при входе Azure Active Directory
Эта статья входит в серию статей об API отчетов Azure Active Directory.  
Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным о действиях при входе, используя код или связанные инструменты.  
Цель этой статьи — предоставить пример кода для **API действий при входе**.

См.:

* Основные сведения см. в разделе [Журналы аудита](active-directory-reporting-azure-portal.md#activity-reports).
* Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).


## <a name="prerequisites"></a>предварительным требованиям
Перед использованием примеров этой статьи выполните [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Сценарий PowerShell

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>Выполнение сценария
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета о журналах входа.

Сценарий возвращает выходные данные из отчета о входе в формате JSON. Он также создает файл `SignIns.json` с такими же выходными данными. Вы можете поэкспериментировать, изменив сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав ненужные форматы выходных данных.

## <a name="next-steps"></a>Дальнейшие действия
* Хотите настроить примеры в этой статье? Просмотрите [API отчета о действиях при входе Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Полный обзор использования API отчетов Azure Active Directory см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Дополнительные сведения об отчетах Azure Active Directory см. в статье [Руководство по отчетам Azure Active Directory](active-directory-reporting-guide.md).  

