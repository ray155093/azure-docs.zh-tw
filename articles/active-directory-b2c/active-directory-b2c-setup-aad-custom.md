---
title: "Azure Active Directory B2C︰使用自訂原則新增 Azure AD 提供者 | Microsoft Docs"
description: "Azure Active Directory B2C 自訂原則的主題"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 29d30cacb29fee1b2c5b8ef523051fa543bee829
ms.contentlocale: zh-tw
ms.lasthandoff: 04/28/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C︰使用 Azure AD 帳戶登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文將說明如何使用[自訂原則](active-directory-b2c-overview-custom.md)讓特定 Azure AD 組織的使用者登入。

## <a name="prerequisites"></a>必要條件

請確定您已完成教您如何[開始使用自訂原則](active-directory-b2c-get-started-custom.md)的所有步驟。

其中包括：

1. 建立 Azure AD B2C 租用戶。
1. 建立 Azure AD B2C 應用程式。
1. 註冊兩個原則引擎應用程式。
1. 設定金鑰。
1. 設定入門套件。

## <a name="create-an-azure-ad-app"></a>建立 Azure AD 應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

>[!NOTE]
> 我們將以 `contoso.com` 當作組織 Azure AD 租用戶，並以 `fabrikamb2c.onmicrosoft.com` 當作 Azure AD B2C 租用戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端列上按一下您的帳戶，然後在 [目錄] 清單底下，選擇您要註冊應用程式的組織 Azure AD 租用戶 (例如 contoso.com)。
1. 按一下左側瀏覽區中的 [更多服務]，然後搜尋 [應用程式註冊]。
1. 選擇 [新增應用程式註冊]。
1. 輸入應用程式的 [名稱] (例如 Azure AD B2C 應用程式)
1. 在 [應用程式類型] 中，選取 [Web 應用程式/API]。
1. 在 [登入 URL] 中，輸入下列 URL，其中 `{tenantName}` 應該換成您的 Azure AD B2C 租用戶名稱 (也就是 fabrikamb2c.onmicrosoft.com)。

    ```
    https://login.microsoftonline.com/te/{tenantName}.onmicrosoft.com/oauth2/authresp
    ```

1. 儲存 [應用程式識別碼]。
1. 按一下新建立的應用程式。
1. 在 [設定] 刀鋒視窗下，按一下 [金鑰]。
1. 建立新的金鑰並儲存，下一節會用到。

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>將 Azure AD 金鑰新增至 Azure AD B2C

您需要將 `contoso.com` 應用程式金鑰儲存在 Azure AD B2C 租用戶中。 作法：

1. 開啟 PowerShell 並瀏覽至工作目錄 `active-directory-b2c-advanced-policies`。
1. 切換至含有 ExploreAdmin 工具的資料夾。

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. 將 ExploreAdmin 工具匯入 Powershell。

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. 在下列命令中，以您的 Azure AD B2C 租用戶名稱 (例如 fabrikamb2c.onmicrosoft.com) 取代 `tenantName`、以您將用來參考祕密的名稱 (例如 ContosoAppSecret) 取代 `SecretReferenceId`，並以 `contoso.com` 應用程式金鑰取代 `ClientSecret`。 執行命令。

    ```PowerShell
    Set-CpimKeyContainer -Tenant {tenantName} -StorageReferenceId {SecretReferenceId} -UnencodedAsciiKey {ClientSecret}
    ```

    當您執行命令時，請確定您使用 Azure AD B2C 租用戶本機的 onmicrosoft.com 管理帳戶登入。 如果發生找不到 'TokenSigningKeyContainer' 的錯誤，請參閱[快速入門](active-directory-b2c-get-started-custom.md)指南。

1. 關閉 PowerShell。

## <a name="add-a-claims-provider-in-your-base-policy"></a>在基本原則中新增宣告提供者

若要允許使用者使用 Azure AD 登入，您需要將 Azure AD 定義為宣告提供者。 換句話說，您需要指定將與 Azure AD B2C 通訊的端點。 此端點將「提供」一組「宣告」，由 Azure AD B2C 用來確認特定使用者已驗證。 在作法上，您可以在原則的擴充檔案中新增 Azure AD 作為 `<ClaimsProvider>`。

1. 從您的工作目錄中開啟擴充檔案 (TrustFrameworkExtensions.xml)。
1. 找出區段 `<ClaimsProviders>`。 如果不存在，請在根節點下新增。
1. 新增 `<ClaimsProvider>`，如下所示︰

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
            <DisplayName>Contoso Employee</DisplayName>
            <Description>Login with your Contoso account</Description>
            <Protocol Name="OpenIdConnect"/>
            <OutputTokenFormat>JWT</OutputTokenFormat>
            <Metadata>
                <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="response_types">id_token</Item>
                <Item Key="UsePolicyInRedirectUri">false</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="ContosoAppSecret"/>
            </CryptographicKeys>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="userId" PartnerClaimType="oid"/>
                <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
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

若要從 Azure AD 端點取得權杖，您需要定義 Azure AD B2C 應該用來與 Azure AD 進行通訊的通訊協定。 這是在 `<ClaimsProvider>` 的 `<TechnicalProfile>` 元素內定義。

1. 更新 `<TechnicalProfile>` 節點的識別碼。 此識別碼可讓原則的其他部分參考此技術設定檔。
1. 更新 `<DisplayName>` 的值。 這個值會顯示在登入畫面的登入按鈕上。
1. 更新 `<Description>` 的值。
1. Azure AD 使用 OpenID Connect 通訊協定，因此請確定 `<Protocol>` 是 "OpenIDConnect"。

您必須更新上述 XML 中的 `<Metdata>` 區段，以反映特定 Azure AD 租用戶的組態設定。 在 XML 中，更新中繼資料值，如下所示︰

1. 將 `<Item Key="METADATA">` 設定為 `https://login.windows.net/{tenantName}/.well-known/openid-configuration`，其中 `tenantName` 是您的 Azure AD 租用戶名稱 (例如 contoso.com)。
1. 開啟瀏覽器並瀏覽至剛更新的 `Metadata` URL。
1. 在瀏覽器中，尋找 'issuer' 物件並複製其值。 如下所示：`https://sts.windows.net/{tenantId}/`。
1. 將 `<Item Key="ProviderName">` 的值貼到 XML 中。
1. 將 `<Item Key="client_id">` 設定為應用程式註冊時的 `Application ID`。
1. 將 `<Item Key="IdTokenAudience">` 設定為應用程式註冊時的 `Application ID`。
1. 確定 `<Item Key="response_types">` 設定為 `id_token`。
1. 確定 `<Item Key="UsePolicyInRedirectUri">` 設定為 `false`。

您也需要將[您在 Azure AD B2C 租用戶中註冊的 Azure AD 祕密](#add-the-azure-ad-key-to-azure-ad-b2c)連結至 Azure AD `<ClaimsProvider>`。

1. 在上述 XML 區段的 `<CryptographicKeys>` 中，將 `StorageReferenceId` 的值更新為您定義之祕密的參考識別碼 (例如 ContosoAppSecret)。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。 若要這樣做：

1. 移至Azure AD B2C 租用戶中的 [所有原則] 刀鋒視窗。
1. 勾選 [覆寫已存在的原則] 方塊。
1. 上傳擴充檔案 (TrustFrameworkExtensions.xml)，並確定它通過驗證。

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>向使用者旅程圖註冊 Azure AD 宣告提供者

您現在需要將 Azure AD 識別提供者新增至其中一個使用者旅程圖。 目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 為了這樣做，我們將建立現有範本使用者旅程圖的複本，然後將它修改成也有 Azure AD 識別提供者。

1. 開啟原則的基本檔案 (例如 TrustFrameworkBase.xml)
1. 尋找 `<UserJourneys>` 元素，並複製含有 Id="SignUpOrSignIn" 的整個 `<UserJourney>`。
1. 開啟擴充檔案 (例如 TrustFrameworkExtensions.xml)，並尋找 `<UserJourneys>` 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 `<UserJourney>` 作為 `<UserJourneys>` 元素的子元素。
1. 重新命名為新的 `<UserJourney>` 的識別碼 (也就是 SignUpOrSignUsingContoso)。

### <a name="display-the-button"></a>顯示「按鈕」

`<ClaimsProviderSelection>` 元素類似於註冊/登入畫面上的識別提供者按鈕。 新增 Azure AD 的 `<ClaimsProviderSelection>` 元素後，當使用者登陸頁面時，將會出現新的按鈕。 作法：

1. 在您剛才建立的 `<UserJourney>` 中，尋找含有 `Order="1"` 的 `<OrchestrationStep>`。
1. 新增下列內容：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. 將 `TargetClaimsExchangeId` 設定為適當的值。 建議您與其他人一樣，遵循相同的慣例 - *\[ClaimProviderName\]Exchange*。

### <a name="link-the-button-to-an-action"></a>將「按鈕」連結至動作

現在已備妥「按鈕」，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Azure AD 通訊以接收權杖。 作法是連結 Azure AD 宣告提供者的技術設定檔。

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

## <a name="troubleshooting"></a>疑難排解

測試您剛才上傳的自訂原則，請開啟其刀鋒視窗，然後按一下 [立即執行]。 如果發生錯誤，請參閱如何[疑難排解](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>後續步驟
 
請將意見反應寄到 AADB2CPreview@microsoft.com。


