---
title: "Azure Active Directory B2C︰將自有屬性新增到自訂原則並用於設定檔編輯 | Microsoft Docs"
description: "逐步解說如何使用擴充屬性、自訂屬性，並將其包含在使用者介面中"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: c2bbb8058ce335c7568d5260ddd0274ca36c9c52
ms.contentlocale: zh-tw
ms.lasthandoff: 06/02/2017

---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C︰在自訂設定檔編輯原則中建立和使用自訂屬性。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在本文中，您將會在 Azure AD B2C 目錄中建立自訂屬性，並使用這個新屬性來作為設定檔編輯使用者旅程中的自訂宣告。

## <a name="prerequisites"></a>必要條件

完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文中的步驟。

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>使用自訂屬性，以透過自訂原則來收集您的客戶在 Azure Active Directory B2C 中的相關資訊
您的 Azure Active Directory (Azure AD) B2C 目錄隨附一組內建屬性：名字、姓氏、城市、郵遞區號、userPrincipalName 等等。您通常必須建立自有屬性。  例如：
* 面對客戶的應用程式需要保有「LoyaltyNumber」之類的屬性。
* 識別提供者擁有必須儲存的唯一使用者識別碼，例如「uniqueUserGUID」。
* 自訂使用者旅程需要保有使用者的狀態，例如「migrationStatus」。

Azure AD B2C 可讓您擴充每個使用者帳戶所儲存的屬性組合。 您也可以使用 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)讀取和寫入這些屬性。

>[!NOTE]
>我們將自訂屬性或擴充屬性稱為 Azure AD B2C 目錄的功能。  擴充屬性會擴充目錄中的使用者物件結構描述。  若要在原則中使用自訂屬性來作為自訂宣告，請將其定義在原則的 `ClaimsSchema` 中。

>[!NOTE]
>擴充屬性只能註冊在應用程式物件上，即使這些屬性可能包含使用者的資料也是如此。 屬性會附加至應用程式。 應用程式物件必須取得寫入權限才能註冊擴充屬性。 任何單一物件均可寫入 100 個擴充屬性 (所有類型和所有應用程式皆可)。 擴充屬性會新增到目標目錄類型，而且在 Azure AD B2C 目錄租用戶中會立即變成可供存取。
如果您刪除應用程式，則這些擴充屬性以及其中包含之所有使用者的資料也會全部移除。 如果應用程式刪除擴充屬性，則擴充屬性會從目標目錄物件上移除，其中所包含的任何資料也都會移除。

>[!NOTE]
>擴充屬性只存在於租用戶中已註冊之應用程式的內容裡。 該應用程式的物件識別碼必須包含在使用該識別碼的 TechnicalProfile 中

>[!NOTE]
>Azure AD B2C 目錄通常會包含名為 `b2c-extensions-app` 的 Web API 應用程式。  此應用程式主要是供 b2c 內建原則用在透過 Azure 入口網站所建立的自訂宣告中。  使用此應用程式來為 b2c 自訂原則註冊擴充屬性的建議，僅適用於進階使用者。


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>建立新的應用程式來儲存擴充屬性

1. 開啟瀏覽工作階段並瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後使用您想要設定之 B2C 目錄的系統管理認證來登入。
1. 在左方的導覽功能表中，按一下 [Azure Active Directory]。 您可能需要選取 [更多服務 >] 才能找到它。
1. 選取 [應用程式註冊]，然後按一下 [新增應用程式註冊]
1. 提供下列建議項目︰
  * 指定 Web 應用程式的名稱：**WebApp-GraphAPI-DirectoryExtensions**
  * 應用程式類型︰Web 應用程式/API
  * 登入 URL：https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. 選取 [建立]。 [通知] 中會出現成功完成
1. 選取新建立的 Web 應用程式：**WebApp-GraphAPI-DirectoryExtensions**
1. 選取 [設定]：**必要權限**
1. 選取 API [Windows Active Directory]
1. 在 [應用程式權限] 中標上核取記號：**讀取及寫入目錄資料**，然後**儲存**
1. 選擇 [授與權限]，然後確認 [是]。
1. 將來自 WebApp-GraphAPI-DirectoryExtensions>Settings>Properties> 的下列識別碼複製到剪貼簿並儲存
*  **應用程式識別碼**。 範例： `103ee0e6-f92d-4183-b576-8c3739027780`
* **物件識別碼**。 範例： `80d8296a-da0a-49ee-b6ab-fd232aa45201`

## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>修改您的自訂原則以新增 `ApplicationObjectId`

針對每個會讀取或寫入擴充屬性的 TechnicalProfile，您必須新增 `<Metadata>` 元素與兩個項目︰先前步驟中所取得的 ApplicationObjectId 和 ClientId。

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
><TechnicalProfile Id="AAD-Common"> 很「通用」，因為其元素會透過下列元素而重複使用在所有的 Azure Active Directory TechnicalProfiles 中︰

```
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
```

>[!NOTE]
>當 TechnicalProfile 第一次寫入到新建立的擴充屬性時，您可能會遇到一次性錯誤，因為系統會在找不到此屬性時加以建立。  .*  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>在使用者旅程中使用新的擴充屬性/自訂屬性


1. 開啟信賴憑證者 (RP) 檔案，此檔案會描述您的原則編輯使用者旅程。  如果您要開始，我們的建議可能是直接從 Azure 入口網站中的 [Azure B2C 自訂原則] 區段，下載您已設定的 RP-PolicyEdit 檔案版本。  或者，您也可以從儲存體資料夾開啟 XML 檔案。
2. 新增自訂宣告 `loyaltyId`。  藉由在 `<RelyingParty>` 元素中加入自訂宣告，它就會以參數形式傳遞至 UserJourney TechnicalProfiles，並加入應用程式的權杖中。
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. 在擴充原則檔 `TrustFrameworkExtensions.xml` 的 ``<ClaimsSchema>`` 元素內新增宣告定義，如下所示。
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. 在基底原則檔 `TrustFrameworkBase.xml` 中新增相同的宣告定義。  

>[!NOTE]
>通常不需同時在基底原則檔和擴充原則檔中新增 `ClaimType` 定義，不過，由於接下來的步驟會將 extension_loyaltyId 新增到基底原則檔的 TechnicalProfiles 中，因此原則驗證器將拒絕上傳沒有此項目的基底原則檔。

>[!NOTE]
>追蹤 TrustFrameworkBase.xml 檔案中名為「ProfileEdit」之使用者旅程圖的執行情形可能有用。  請在編輯器中搜尋相同名稱的使用者旅程，並注意到協調流程步驟 5 會叫用 TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate"。  搜尋並檢查此 TechnicalProfile 以熟悉流程。

5. 在 TechnicalProfile "SelfAsserted-ProfileUpdate" 中新增 loyaltyId 來作為輸入和輸出宣告

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. 在 TechnicalProfile "AAD-UserWriteProfileUsingObjectId" 中新增宣告，針對目錄中的目前使用者將宣告值保存在擴充屬性中。

```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. 在 TechnicalProfile "AAD-UserReadUsingObjectId" 中新增宣告，以在每次使用者登入時讀取擴充屬性的值。
注意︰到目前為止，只有本機帳戶的流程有變更 TechnicalProfiles。  如果您想要將新屬性用在社交/同盟帳戶的流程中，您就必須變更一組不同的 TechnicalProfiles。 請參閱後續步驟。

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>上述的 IncludeTechnicalProfile 元素會將 AAD-Common 的所有元素新增到這個 TechnicalProfile。

## <a name="test-the-custom-policy-using-run-now"></a>使用 [立即執行] 測試自訂原則

     1. Open the **Azure AD B2C Blade** and navigate to **Identity Experience Framework > Custom policies**.
     2. Select the custom policy that you uploaded, and click the **Run now** button.
     3. You should be able to sign up using an email address.

傳送回應用程式的識別碼權杖，會以自訂宣告的形式來包含新的擴充屬性，且此宣告前面會加上 extension_loyaltyId。 請參閱範例。

```
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>後續步驟

藉由變更下列 TechnicalProfiles，將新的宣告新增至社交帳戶登入的的流程。 社交/同盟帳戶登入會使用這兩個 TechnicalProfiles，以 alternativeSecurityId 作為使用者物件的定位器來寫入和讀取使用者資料。
```
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```


## <a name="reference"></a>參考

* **技術設定檔 (TP)** 是一個元素類型，您可以將它想作是「函式」，而它可定義端點的名稱、其中繼資料、其通訊協定，並詳細說明身分識別體驗架構所應該執行的宣告交換。  當這個「函式」在協調流程步驟中或從另一個 TechnicalProfile 呼叫時，呼叫端會提供 InputClaims 和 OutputClaims 來作為參數。


* 如需擴充屬性的完整處理方式，請參閱[目錄結構描述擴充 | 圖形 API 概念](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) \(英文\) 一文

>[!NOTE]
>圖形 API 中的擴充屬性會使用 `extension_ApplicationObjectID_attributename` 慣例來命名。自訂原則會將擴充屬性稱為 extension_attributename，因此會省略 XML 中的 ApplicationObjectId

