---
title: "Azure AD B2C 使用報告 API 範例和定義 | Microsoft Docs"
description: "關於如何取得有關 B2C 租用戶使用者、驗證、MFA 報告的指南和範例。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>透過報告 API 存取 Azure AD B2C 中的使用報告

Azure Active Directory B2C 提供登入和 MFA 式驗證，可以跨身分識別提供者用於您的應用程式系列的所有使用者。  可以知道租用戶註冊的使用者數目、他們用來註冊的提供者、不同類型驗證的次數，以及這類問題的解答︰
* 過去 10 天內各類型身分識別提供者 (例如，Microsoft 帳戶、LinkedIn) 註冊的使用者人數？
* 上個月成功完成幾次多重要素驗證？
* 這個月已完成幾次登入式驗證？ 依每一天計算？ 依每一應用程式計算？
* 如何估算我的 B2C 租用戶活動的每月預期成本？

本文著重於與計費活動有密切關係的報告，以使用者數目、可計費的登入式驗證次數、多重要素驗證次數為根據。


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>存取 Azure AD 報告 API 的必要條件
開始之前，您必須先完成[存取 Azure AD 報告 API 的必要條件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)。  建立應用程式、取得密碼，並授與對您的 Azure AD B2C 租用戶報告的存取權限。 這裡也提供了 Bash 指令碼和 Python 指令碼的範例。

## <a name="powershell-script"></a>PowerShell 指令碼
此指令碼示範使用 **TimeStamp** 參數和 **-ApplicationId** 篩選器的 4 個使用報告。

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>使用報告定義
**tenantUserCount** - 過去 30 天，每一天各類型身分識別提供者的租用戶使用者數目。 (選擇性的 TimeStamp 篩選器可提供從指定日期至目前日期的使用者計數)。 報告中提供︰
 * TotalUserCount = 所有使用者物件的計數
 * OtherUserCount = AAD 目錄使用者的數目 (非 B2C 使用者)
 * LocalUserCount = B2C 使用者帳戶的數目 (這些帳戶是以 B2C 租用戶本機的認證建立)

**AlternateIdUserCount** = 以外部身分識別提供者 (例如 Facebook Microsoft 帳戶、或其他 AAD 租用戶 - 也稱為 OrgId) 註冊的 B2C 使用者數目

**b2cAuthenticationCountSummary** -過去 30 天每天及各類型驗證流程的每日可計費驗證計數的總和

**b2cAuthenticationCount** - 某段時間週期內的驗證計數。 預設值為 30 天。  (選擇性︰開始和結束時間戳記 (TimeStamp) 會定義特定一段所需的計數)。輸出包括 StartTimeStamp (此租用戶的最早活動日期) 和 EndTimeStamp (最新的更新)。)

**b2cMfaRequestCountSummary** - 每天及各類型 MFA (SMS 或語音) 的每日多重因素驗證計數的總和


## <a name="limitations"></a>限制
* 使用者計數的資料每隔 24 到 48 小時會重新整理。  驗證則是每天更新數次。
* 使用 ApplicationId 篩選器時，回應空白報告可能是因為發生下列情況之一︰
 * 在租用戶中，該應用程式識別碼不存在。 請確定識別碼正確。
 * 應用程式識別碼存在，但在報告期間內沒有任何資料。 檢閱您的日期時間參數。


## <a name="next-steps"></a>後續步驟
### <a name="estimating-your-azure-ad-monthly-bill"></a>評估您的 Azure AD 每月帳單。
搭配[最新 Azure AD B2C 價格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)，您可以評估每日、每週、每月的 Azure 耗用量。  當您規劃的租用戶行為變更可能會影響整體成本時，評估特別實用。  您可在您的[連結的 Azure 訂用帳戶](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)下檢閱實際成本。

### <a name="options-for-other-output-formats"></a>其他輸出格式的選項
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

