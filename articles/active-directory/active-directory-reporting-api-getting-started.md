<properties
   pageTitle="開始使用 Azure AD 報告 API | Microsoft Azure"
   description="如何開始使用 Azure Active Directory 報告 API"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/28/2016"
   ms.author="dhanyahk"/>

# 開始使用 Azure Active Directory 報告 API

*這份文件是 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*

Azure Active Directory (AD) 提供各種活動、安全性與稽核報告。這份資料可以透過 Azure 傳統入口網站取用，但是在其他許多應用程式 (例如 SIEM 系統、稽核和商業智慧工具) 中也可能非常有用。

[Azure AD 報告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 透過一組以 REST 為基礎的 API (可從各種程式設計語言和工具呼叫)，提供這些資料的程式設計方式存取。

本文將逐步引導您使用 PowerShell 完成呼叫 Azure AD 報告 API 的程序。您可以依照案例需求修改範例 PowerShell 指令碼，從任何可用的報告 (JSON、XML 或文字格式) 存取資料。

若要使用這個範例，您需要 [Azure Active Directory](active-directory-whatis.md) 租用戶。

## 建立 Azure AD 應用程式以存取 API

報告 API 會使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授權存取 Web API。若要存取您的目錄資訊，您必須在 Azure AD 租用戶中建立應用程式，並授與其存取 Azure AD 資料的適當權限。


### 註冊 Azure AD 應用程式
若要完成 Azure AD 應用程式註冊工作，您必須以 Azure 訂用帳戶管理員帳戶登入 Azure 傳統入口網站，而且該帳戶同時也是 Azure AD 租用戶全域管理員目錄角色的成員。這是因為您註冊 Azure AD 應用程式時需要有註冊/同意的權限，而將會使用具有全域管理員權限的帳戶。

> [AZURE.IMPORTANT] 像這樣使用具有「管理員」權限的認證所執行的應用程式會非常強大，請務必確保應用程式的識別碼/密碼認證的安全性。


1. 巡覽至 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 沿著左窗格上的 **Active Directory** 擴充，瀏覽至 Azure AD 租用戶。
3. 瀏覽至 [應用程式] 索引標籤。
4. 按一下底列上的 [新增]。
	- 按一下 [加入我的組織正在開發的應用程式]。
	- **名稱**：任何名稱均可。建議類似「報告 API 應用程式」。
	- **類型**：選取 [Web 應用程式和/或 Web API]。
	- 按一下箭號以移至下一頁。
	- **登入 URL**：```https://localhost```。
	- **應用程式識別碼 URI**：```https://localhost/ReportingApiApp```。
	- 按一下核取記號以加入應用程式。

### 授與您的應用程式使用 API 的權限
1. 瀏覽至 [應用程式] 索引標籤。
2. 瀏覽至新建立的應用程式。
3. 按一下 [設定] 索引標籤。
4. 在 [其他應用程式的權限] 區段中：
	- 若是 "Windows Azure Active Directory" 資源 (Azure AD Graph API 的權限)，請按一下 [應用程式權限] 下拉式清單，然後選取 [讀取目錄資料]。

        > [AZURE.IMPORTANT] 請務必在這裡指定正確的權限。套用「應用程式權限」而非「委派的權限」，否則應用程式不會取得存取「報告 API」所需的權限等級，您的指令碼將會收到「無法檢查 appId 的目錄讀取權限」錯誤。


5. 按一下底列上的 [**儲存**]。

### 取得目錄識別碼、用戶端識別碼和用戶端密鑰

下列步驟將引導您取得應用程式用戶端識別碼和用戶端密鑰。您也需要知道您的租用戶名稱，它可以是您的 *.onmicrosoft.com 或自訂網域名稱。將這些值複製到不同的位置；稍後您將使用它們來修改指令碼。

#### 取得 Azure AD 租用戶的網域名稱
1. 沿著左窗格上的 **Active Directory** 擴充，瀏覽至 Azure AD 租用戶。
2. 瀏覽至 [網域] 索引標籤。
4. 將會顯示租用戶的 "<tenant-name>.onmicrosoft.com" 網域名稱，以及任何自訂網域名稱 (如果已設定的話)。

#### 取得應用程式的用戶端識別碼
1. 瀏覽至 [應用程式] 索引標籤。
2. 瀏覽至新建立的應用程式。
3. 瀏覽至 [**設定**] 索引標籤。
4. 您的應用程式用戶端識別碼會列在 [**用戶端識別碼**] 欄位中。

#### 取得應用程式的用戶端密碼
1. 瀏覽至 [應用程式] 索引標籤。
2. 瀏覽至新建立的應用程式。
3. 瀏覽至 [**設定**] 索引標籤。
4. 在 [金鑰] 區段中選取持續時間，為您的應用程式產生新的秘鑰。
5. 此金鑰將會在儲存時顯示。務必將它複製並貼到安全的位置，因為之後便無法予以擷取。

## 修改指令碼
編輯下列其中一個指令碼以搭配您的目錄使用：使用上方區段中的正確值來取代 $ClientID、$ClientSecret 和 $tenantdomain。

### PowerShell 指令碼
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Bash 指令碼

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## 執行指令碼
完成指令碼編輯後，加以執行並確認從 AuditEvents 報告傳回預期的資料。

指令碼會以 JSON 格式傳回 auditEvents 報告的輸出。它也會建立具有相同輸出的 `auditEvents.json` 檔案。您可透過修改指令碼以從其他報告傳回資料來進行實驗，以及取消註解您不需要的輸出格式。

## 注意事項

- Azure AD Reporting API (使用 OData 分頁) 傳回的事件數目沒有任何限制。
- 如需報告資料的保留限制，請參閱[報告保留原則](active-directory-reporting-retention.md)。


## 後續步驟
- 想知道有哪些安全性、稽核及活動報告可用嗎？ 請查看 [Azure AD 安全性、稽核及活動報告](active-directory-view-access-usage-reports.md)。您也可以瀏覽至 [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta) (收錄於 [Azure AD 報告和事件 (預覽)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) 文件中)，以查看所有可用的 Azure AD Graph API 端點。
- 如需稽核報告的詳細資訊，請參閱 [Azure AD 稽核報告事件](active-directory-reporting-audit-events.md)
- 如需 Azure AD Graph API REST 服務的詳細資訊，請參閱 [Azure AD 報告和事件 (預覽)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)。

<!---HONumber=AcomDC_0629_2016-->