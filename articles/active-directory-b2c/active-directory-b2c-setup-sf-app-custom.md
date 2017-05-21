---
title: "Azure Active Directory B2C︰使用自訂原則新增 Salesforce SAML 提供者 | Microsoft Docs"
description: "Azure Active Directory B2C 自訂原則的主題"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: dee24deacbe69ada64519802c0eb1b83f565f57e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C︰使用 Salesforce 帳戶透過 SAML 來登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文將說明如何使用[自訂原則](active-directory-b2c-overview-custom.md)讓特定 Salesforce 組織的使用者登入。

## <a name="prerequisites"></a>必要條件

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C 設定
請確定您已完成教您如何[開始使用自訂原則](active-directory-b2c-get-started-custom.md)的所有步驟。

其中包括：

1. 建立 Azure AD B2C 租用戶。
1. 建立 Azure AD B2C 應用程式。
1. 註冊兩個原則引擎應用程式。
1. 設定金鑰。
1. 設定入門套件。

### <a name="salesforce-setup"></a>Salesforce 設定
本教學課程假設您已：
1. 註冊 Salesforce 帳戶。 您可以註冊[免費的開發人員版本](https://developer.salesforce.com/signup)。 
1. 為您的 Salesforce 組織[設定網域](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="get-the-salesforce-saml-metadata"></a>取得 Salesforce SAML 中繼資料
>[!NOTE]
> 本教學課程假設您是使用 [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ)。

1. [登入 Salesforce](https://login.salesforce.com/) 
1. 在左側功能表的 [設定] 下，展開 [身分識別]，然後按一下 [識別提供者]
1. 按一下 [啟用識別提供者]
1. **選取您想要讓 Salesforce 在與 Azure AD B2C 通訊時使用的憑證**，然後按一下 [儲存]。 您可以使用預設憑證。
1. 按一下現已可用的 [下載中繼資料] 按鈕，並儲存中繼資料檔案以供稍後的步驟使用。

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>將 SAML 簽署憑證新增至 Azure AD B2C
您需要將 Salesforce 憑證上傳至 Azure AD B2C 租用戶。 作法：

1. 開啟 PowerShell 並瀏覽至工作目錄 `active-directory-b2c-advanced-policies`。
1. 使用 ExploreAdmin 工具切換到資料夾。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. 將 ExploreAdmin 工具匯入 Powershell。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. 在下列命令中，以您的 Azure AD B2C 租用戶名稱 (例如 fabrikamb2c.onmicrosoft.com) 取代 `tenantName`，以稍後將在原則中用來參考該租用戶的憑證名稱 (例如 ContosoSalesforceCert) 取代 `certificateId`，最後再以憑證的路徑和密碼取代 `pathToCert` 和 `password`。 執行命令。

    ```PowerShell
    Set-CpimCertificate -TenantId {tenantName} -CertificateId {certificateId} -CertificateFileName {pathToCert} - CertificatePassword {password}
    ```

    當您執行命令時，請確定您使用 Azure AD B2C 租用戶本機的 onmicrosoft.com 管理帳戶登入。 

1. 關閉 PowerShell。

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>在基底原則中建立 Salesforce SAML 宣告提供者

若要允許使用者使用 Salesforce 登入，您需要將 Salesforce 定義為宣告提供者。 換句話說，您需要指定將與 Azure AD B2C 通訊的端點。 此端點將「提供」一組「宣告」，由 Azure AD B2C 用來確認特定使用者已驗證。 在作法上，您可以在原則的擴充檔案中為 Salesforce 新增 `<ClaimsProvider>`。

1. 從您的工作目錄中開啟擴充檔案 (TrustFrameworkExtensions.xml)。
1. 找出區段 `<ClaimsProviders>`。 如果不存在，請在根節點下新增。
1. 新增 `<ClaimsProvider>`，如下所示︰

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 在 `<ClaimsProvider>` 節點下，將 `<Domain>` 的值更新為可以與其他識別提供者有所區別的唯一值。
1. 在 `<ClaimsProvider>` 節點下，將 `<DisplayName>` 的值更新為宣告提供者的易記名稱。 目前不使用此值。

### <a name="update-the-technical-profile"></a>更新技術設定檔

若要從 Salesforce 取得 SAML 權杖，您需要定義 Azure AD B2C 應該用來與 Azure AD 進行通訊的通訊協定。 這是在 `<ClaimsProvider>` 的 `<TechnicalProfile>` 元素內定義。

1. 更新 `<TechnicalProfile>` 節點的識別碼。 此識別碼可讓原則的其他部分參考此技術設定檔。
1. 更新 `<DisplayName>` 的值。 這個值會顯示在登入畫面的登入按鈕上。
1. 更新 `<Description>` 的值。
1. Azure AD 使用 OpenID Connect 通訊協定，因此請確定 `<Protocol>` 是 "SAML2"。

您必須更新上述 XML 中的 `<Metadata>` 區段，以反映特定 Azure AD 租用戶的組態設定。 在 XML 中，更新中繼資料值，如下所示︰

1. 使用您從 Salesforce 下載的 Metadata.xml 內容來更新 `<Item Key="PartnerEntity">` 的值。 **請務必使用 <![CDATA[ …metadata… ]]>** 將它封裝起來。

1. 在上述 XML 的 `<CryptographicKeys>` 區段中，將 `StorageReferenceId` 的值更新為您已定義的憑證識別碼 (例如 ContosoSalesforceCert)。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。 若要這樣做：

1. 移至Azure AD B2C 租用戶中的 [所有原則] 刀鋒視窗。
1. 勾選 [覆寫已存在的原則] 方塊。
1. 上傳擴充檔案 (TrustFrameworkExtensions.xml)，並確定它通過驗證。

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>向使用者旅程圖註冊 Salesforce SAML 宣告提供者

您現在需要將 Salesforce SAML 識別提供者新增至其中一個使用者旅程圖。 目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 為了這樣做，我們將建立現有範本使用者旅程圖的複本，然後將它修改成也有 Azure AD 識別提供者。

1. 開啟原則的基本檔案 (例如 TrustFrameworkBase.xml)
1. 尋找 `<UserJourneys>` 元素，並複製含有 Id="SignUpOrSignIn" 的整個 `<UserJourney>`。
1. 開啟擴充檔案 (例如 TrustFrameworkExtensions.xml)，並尋找 `<UserJourneys>` 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 `<UserJourney>` 作為 `<UserJourneys>` 元素的子元素。
1. 重新命名為新的 `<UserJourney>` 的識別碼 (也就是 SignUpOrSignUsingContoso)。

### <a name="display-the-button"></a>顯示「按鈕」

`<ClaimsProviderSelection>` 元素類似於註冊/登入畫面上的識別提供者按鈕。 新增 Salesforce 的 `<ClaimsProviderSelection>` 元素後，當使用者登陸頁面時，將會出現新的按鈕。 作法：

1. 在您剛才建立的 `<UserJourney>` 中，尋找含有 `Order="1"` 的 `<OrchestrationStep>`。
1. 新增下列內容：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. 將 `TargetClaimsExchangeId` 設定為適當的值。 建議您與其他人一樣，遵循相同的慣例 - \[ClaimProviderName\]Exchange。

### <a name="link-the-button-to-an-action"></a>將「按鈕」連結至動作

現在已備妥「按鈕」，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Salesforce 通訊以接收 SAML 權杖。 作法是連結 Salesforce SAML 宣告提供者的技術設定檔。

1. 在 `<UserJourney>` 節點中，尋找含有 `Order="2"` 的 `<OrchestrationStep>`
1. 新增下列內容：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. 將 `Id` 的值更新為與上述 `TargetClaimsExchangeId` 的值相同。
1. 將 `TechnicalProfileReferenceId` 更新為您稍早建立之技術設定檔的 `Id` (例如 ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>上傳更新的擴充檔案

您已修改擴充檔案。 儲存並上傳此檔案，並確保所有驗證都成功。

### <a name="update-the-rp-file"></a>更新 RP 檔案

您現在需要更新 RP 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 將 SignUpOrSignIn.xml 複製到您的工作目錄，並重新命名 (例如 SignUpOrSignInWithAAD.xml)。
1. 開啟新檔案，將 `<TrustFrameworkPolicy>` 的 `PolicyId` 屬性更新成唯一值。 這會成為您的原則名稱 (例如 SignUpOrSignInWithAAD)。
1. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 屬性，以符合您建立之新使用者旅程圖的識別碼 (例如 SignUpOrSignUsingContoso)。
1. 儲存變更並上傳檔案。

## <a name="create-a-connected-app-in-salesforce"></a>在 Salesforce 中建立連線應用程式
您必須在 Salesforce 中將 Azure AD B2C 註冊為連線應用程式。

1. [登入 Salesforce](https://login.salesforce.com/) 
1. 在左側功能表的 [設定] 下，展開 [身分識別]，然後按一下 [識別提供者]
1. 在 [服務提供者] 區段底部，按一下 [現在會透過連線應用程式建立服務提供者]。按一下這裡。
1. 為連線應用程式提供必要的**基本資訊**。
1. 現在，在 [Web 應用程式設定] 區段中︰
    1. 勾選 [啟用 SAML]
    1. 在 [實體識別碼] 欄位中輸入下列 URL，請務必要取代 `tenantName`。 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base
        ```

    1. 在 [ACS URL] 欄位中輸入下列 URL，請務必要取代 `tenantName`。 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base/samlp/sso/assertionconsumer
        ```

    1. 讓其他所有設定保留其預設值
1. 向下捲動到底部，然後按一下 [儲存] 按鈕


## <a name="troubleshooting"></a>疑難排解

測試您剛才上傳的自訂原則，請開啟其刀鋒視窗，然後按一下 [立即執行]。 如果發生錯誤，請參閱如何[疑難排解](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>後續步驟
 
請將意見反應寄到 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)。


