<properties
	pageTitle="使用 OAuth2.0 的 Azure AD 服務對服務驗證 | Microsoft Azure"
	description="本文說明如何使用 HTTP 訊息，以利用 OAuth2.0 用戶端認證授與流程實作服務對服務驗證。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="priyamo"/>

# 使用用戶端認證的服務對服務呼叫

OAuth 2.0 用戶端認證授與流程可允許 Web 服務 (「機密用戶端」) 在呼叫另一個 Web 服務時使用它自己的認證來進行驗證，而不必模擬使用者。在此案例中，用戶端通常是中介層 Web 服務、精靈服務或網站。

## 用戶端認證授與流程圖

下圖說明用戶端認證授與流程在 Azure Active Directory (Azure AD) 中的作用方式。

![OAuth2.0 用戶端認證授與流程](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 用戶端應用程式會向 Azure AD 權杖發行端點進行驗證，並要求存取權杖。
2. Azure AD 權杖發行端點會發行存取權杖。
3. 存取權杖可用來向受保護的資源進行驗證。
4. 來自受保護資源的資料會傳回 Web 應用程式。

## 在 Azure AD 中註冊服務

請在 Azure Active Directory (Azure AD) 中同時註冊呼叫端服務和接收端服務。如需詳細指示，請參閱[新增、更新和移除應用程式](active-directory-integrating-applications.md#BKMK_Native)

## 要求存取權杖

若要要求存取權杖，請對租用戶特定的 Azure AD 端點使用 HTTP POST。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## 服務對服務存取權杖要求

服務對服務存取權杖要求包含下列參數。

| 參數 | | 說明 |
|-----------|------|------------|
| response\_type | 必要 | 指定要求的回應類型。在用戶端認證授與流程中，值必須是 **client\_credentials**。|
| client\_id | 必要 | 指定呼叫端 Web 服務的 Azure AD 用戶端識別碼。若要尋找呼叫端應用程式的用戶端識別碼，請在 Azure 管理入口網站中，依序按一下 [Active Directory]、目錄、應用程式及 [設定]。|
| client\_secret | 必要 | 輸入在 Azure AD 中針對呼叫端 Web 服務所註冊的金鑰。若要建立金鑰，請在 Azure 管理入口網站中，依序按一下 [Active Directory]、目錄、應用程式及 [設定]。 |
| resource | 必要 | 輸入接收端 Web 服務的應用程式識別碼 URI。若要尋找應用程式識別碼 URI，請在 Azure 管理入口網站中，依序按一下 [Active Directory]、目錄、應用程式及 [設定]。 |

## 範例

下列 HTTP POST 會要求 https://service.contoso.com/ Web 服務的存取權杖。`client_id` 會識別要求存取權杖的 Web 服務。

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## 服務對服務存取權杖回應

成功的回應中包含 JSON OAuth 2.0 回應與下列參數。

| 參數 | 說明 |
|-------------|-------------|
|access\_token |所要求的存取權杖。呼叫端 Web 服務可以使用此權杖來向接收端 Web 服務進行驗證。 |
|access\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 [持有人]。如需持有人權杖的詳細資訊，請參閱 [OAuth 2.0 授權架構︰持有人權杖用法 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)。
|expires\_in | 存取權杖的有效期 (以秒為單位)。|
|expires\_on |存取權杖的到期時間。日期會表示為從 1970-01-01T0:0:0Z UTC 至到期時間的秒數。這個值用來判斷快取權杖的存留期。 |
|resource | 接收端 Web 服務的應用程式識別碼 URI。 |

## 範例

下列範例顯示向 Web 服務所提出之存取權杖要求的成功回應。

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## 另請參閱

* [Azure AD 中的 OAuth 2.0](active-directory-protocols-oauth-code.md)

<!---HONumber=AcomDC_0727_2016-->