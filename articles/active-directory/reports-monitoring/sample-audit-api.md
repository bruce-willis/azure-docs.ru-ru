---
title: Примеры API аудита инструментов создания отчетов Azure Active Directory | Документация Майкрософт
description: Как начать работу с API отчетов Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 345daaa8ac991cad9f098b317a9b59d52de73ead
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "41924772"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Примеры API аудита инструментов создания отчетов Azure Active Directory
Эта статья входит в серию статей об API отчетов Azure Active Directory.  
Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным аудита, используя код или связанные инструменты.
Цель этой статьи — предоставить пример кода для **API аудита**.

См.:

* Основные сведения см. в разделе [Журналы аудита](overview-reports.md#activity-reports).
* Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](concept-reporting-api.md).

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Предварительные требования
Перед использованием примеров этой статьи выполните [предварительные требования для доступа к API отчетов Azure AD](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="known-issue"></a>Известная проблема
Вы не сможете пройти аутентификацию приложения, если клиент находится в регионе Европы. Используйте аутентификацию пользователя для доступа к API аудита в качестве обходного решения, пока мы не решим эту проблему. 

## <a name="powershell-script"></a>Сценарий PowerShell


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
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

### <a name="executing-the-powershell-script"></a>Выполнение сценария PowerShell
Завершив редактирование сценария, запустите его и убедитесь, что он вернул ожидаемые данные из отчета о журналах аудита.

Сценарий возвращает выходные данные из отчета об аудите в формате JSON. Он также создает файл `Audits.json` с такими же выходными данными. Вы можете поэкспериментировать, изменив сценарий так, чтобы он возвращал данные из других отчетов, и закомментировав ненужные форматы выходных данных.





## <a name="next-steps"></a>Дополнительная информация
* Хотите настроить примеры в этой статье? См. статью [Справочник по API аудита Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Полный обзор использования API отчетов Azure Active Directory см. в статье [Приступая к работе с API отчетов Azure Active Directory](concept-reporting-api.md).
* Дополнительные сведения об отчетах Azure Active Directory см. в статье [Руководство по отчетам Azure Active Directory](overview-reports.md).  

