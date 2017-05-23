---
title: "Azure AD B2C 整合 REST API 宣告交換以作為自訂原則中的驗證 | Microsoft Docs"
description: "Azure Active Directory B2C 自訂原則的主題"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程中以作為使用者輸入的驗證

構成 Azure AD B2C 基礎的**身分識別體驗架構** (IEF) 可讓身分識別開發人員於使用者旅程中整合與 RESTful API 的互動。  

在本逐步解說結束時，您將可以建立與 RESTful 服務互動的 Azure AD B2C 使用者旅程。

IEF 會在宣告中傳送資料，並在宣告中收到傳回的資料。  您可以將與 API 的互動設計為 REST API 宣告交換，以作為協調流程步驟內發生的驗證設定檔。

- 這通常會驗證使用者的輸入
- 如果系統拒絕使用者輸入的值，則使用者可以再試著輸入有效值一次，但系統可能會對使用者傳回錯誤訊息。

互動也可以設計成協調流程步驟。 如需詳細資訊，請參閱[逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程中以作為協調流程步驟](active-directory-b2c-rest-api-step-custom.md)。

至於驗證設定檔範例，我們會使用入門套件檔案 ProfileEdit.xml 中的設定檔編輯使用者旅程。

我們可以驗證使用者在設定檔編輯中所提供的名字不是排除清單的一部分。

## <a name="prerequisites"></a>必要條件

- 如[開始使用](active-directory-b2c-get-started-custom.md)所述，設定為完成本機帳戶註冊/登入的 Azure AD B2C 租用戶。
- 要互動的 REST API 端點。 示範網站 [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) 已設定了 REST API 服務，以便用於此逐步解說。

## <a name="step-1---prepare-the-rest-api-function"></a>步驟 1 - 準備 REST API 函式

> [!NOTE]
> REST API 函式的設定不在本文討論範圍內。 [Azure 函式應用程式](https://docs.microsoft.com/azure/azure-functions/functions-reference)提供了絕佳的工具組，供您在雲端建立 RESTful 服務。

我們已建立了 Azure 函式來接收宣告它預期會是「playerTag」的宣告，並驗證此宣告是否存在。 您可以在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) 中取得完整的 Azure 函式程式碼。

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

身分識別體驗架構預期 Azure 函式會傳回 `userMessage` 宣告，如果驗證失敗 (例如，當上述範例傳回「409 衝突」狀態時)，系統會以字串形式向使用者提供此宣告。

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>步驟 2 - 在 TrustFrameworkExtensions.xml 檔案中將 RESTful API 宣告交換設定為技術設定檔

技術設定檔是 RESTful 服務所需之交換的完整設定。 開啟 `TrustFrameworkExtensions.xml` 檔案，並將下列 XML 程式碼片段新增到 `<ClaimsProviders>` 元素內。

> [!NOTE]
> 請考慮使用以下所述的「Restful 提供者 1.0.0.0 版」來作為會與外部服務互動之函式的通訊協定。  您可以找到完整的結構描述定義 <!-- TODO: Link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` 元素會定義將從 IEE 傳送至 REST 服務的宣告。 在上述範例中，`givenName` 宣告的內容將會傳送至 REST 服務來作為 `playerTag`。 在此範例中，IEE 不會預期有宣告傳回，而會改為等待來自 REST 服務的回應，並根據所收到的狀態碼來採取行動。

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>步驟 3 - 在您想要用來驗證使用者輸入的自我判斷提示技術設定檔中加入 RESTful 服務宣告交換

驗證步驟最常用於與使用者互動。  使用者應該在其中提供輸入的所有互動就是**自我判斷提示技術設定檔**。 在此範例中，我們會將此驗證新增到 **Self-Asserted-ProfileUpdate** 技術設定檔 (TP)。  這是 RP 原則檔 `Profile Edit` 所使用的 TP。

若要在自我判斷提示技術設定檔中新增宣告交換︰

1. 開啟 TrustFrameworkBase 檔案，並搜尋 `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`。
2. 檢閱此 TP 的設定，並觀察與使用者所進行的交換如何定義為向使用者要求的宣告 (輸入宣告)，以及如何定義為應該從自我判斷提示提供者傳回的宣告 (輸出宣告)
3. 搜尋 `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`，並注意系統會叫用此設定檔來作為 `<UserJourney Id="ProfileEdit">` 的協調流程步驟 #6

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>步驟 4 - 上傳和測試設定檔編輯 RP 原則檔案

1. 上傳新版本的 `TrustframeworkExtensions` 檔案。
2. 使用**立即執行**來測試設定檔編輯 RP 原則檔案。
3. 在 [名字] 欄位中提供其中一個現有名稱 (例如︰mcvinny)，以測試驗證。 如果一切設定皆正確，您應該會收到訊息，通知使用者 `player tag` 已在使用中。

## <a name="next-steps"></a>後續步驟

[如何修改設定檔編輯和使用者註冊以從使用者收集其他資訊](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程中以作為協調流程步驟](active-directory-b2c-rest-api-step-custom.md)

