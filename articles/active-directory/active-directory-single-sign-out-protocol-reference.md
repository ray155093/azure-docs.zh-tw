<properties
	pageTitle="Azure 單一登出 SAML 通訊協定 | Microsoft Azure"
	description="本文說明 Azure Active Directory 中的單一登出 SAML 通訊協定"
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
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# 單一登出 SAML 通訊協定

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) 支援 SAML 2.0 Web 瀏覽器單一登出設定檔。若要讓單一登出正常運作，Azure AD 必須在應用程式註冊期間註冊其中繼資料 URL。Azure AD 會從中繼資料取得雲端服務的登出 URL 和簽署金鑰。Azure AD 會使用簽署金鑰來驗證連入 LogoutRequest 上的簽章，並且將在使用者登出之後使用 LogoutURL 來重新導向使用者。

如果雲端服務不支援中繼資料端點，在應用程式註冊之後，開發人員必須連絡 Microsoft 支援服務以提供登出 URL 和簽署金鑰。

下圖顯示 Azure AD 單一登出程序的工作流程。

![單一登出工作流程](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## LogoutRequest

雲端服務會傳送 `LogoutRequest` 訊息至 Azure AD，指出工作階段已終止。下列摘錄顯示範例 `LogoutRequest` 元素。

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### LogoutRequest

傳送至 Azure AD 的 `LogoutRequest` 元素需要下列屬性：

- `ID`：這會識別登出要求。`ID` 的值不應該以數字開頭。一般的做法是附加 **id** 至 GUID 的字串表示法。

- `Version`：將此元素的值設定為 **2.0**。需要此值。

- `IssueInstant`：這是具有國際標準時間 (UTC) 值和 [來回行程格式 ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx) 的 `DateTime` 字串。Azure AD 會預期此類型的值，但不會強制。

- 如果 `Consent`、`Destination`、`NotOnOrAfter` 和 `Reason` 屬包含在 `LogoutRequest` 元素中，則會加以忽略。

### Issuer

`LogoutRequest` 中的 `Issuer` 元素必須完全符合 Azure AD 中雲端服務的其中一個 **ServicePrincipalNames**。一般而言，這會設定為應用程式註冊期間指定的**應用程式識別碼 URI**。

### NameID

`NameID` 元素的值必須完全符合正在登出的使用者的 `NameID`。
## LogoutResponse

Azure AD 會傳送 `LogoutResponse` 以回應 `LogoutRequest` 元素。下列摘錄顯示範例 `LogoutResponse`。

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### LogoutResponse

Azure AD 會設定 `LogoutResponse` 元素中的 `ID`、`Version` 和 `IssueInstant` 值。它也會將 `InResponseTo` 元素設定為導出回應的 `LogoutRequest` 的 `ID` 屬性值。

### Issuer

Azure AD 會將此值設為 `https://login.microsoftonline.com/<TenantIdGUID>/`，其中，<TenantIdGUID> 是 Azure AD 租用戶的租用戶識別碼。

若要評估 `Issuer` 元素的值，請使用應用程式註冊期間提供的**應用程式識別碼 URI** 的值。

### Status

Azure AD 使用 `Status` 元素中的 `StatusCode` 元素，來指出登出成功或失敗。登出嘗試失敗時，`StatusCode` 元素也可包含自訂錯誤訊息。

<!---HONumber=AcomDC_0601_2016-->