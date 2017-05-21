---
title: "Azure Active Directory B2C：針對自訂原則進行疑難排解 | Microsoft Docs"
description: "用來解決自訂原則錯誤的方法指南"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Azure Active Directory B2C 自訂原則和身分識別體驗架構的疑難排解
## <a name="the-basics"></a>基本概念

使用 Azure AD B2C 自訂原則的身分識別開發人員都面臨了挑戰，他們無法輕鬆地使用 XML 格式，以其原則語言設定身分識別體驗架構。  本指南是融合了建議工具和秘訣的清單，可供您快速探索並解決問題。  學習撰寫自訂原則就如同學習新的語言。  
本文著重於 Azure AD B2C 自訂原則設定 (而非信賴憑證者應用程式或其身分識別程式庫) 的疑難排解。



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>XML 編輯，錯誤格式的 XML 是最常見的錯誤。

良好的 XML 編輯器會原生顯示 XML、為內容加上色彩標示、預先填入常用詞彙、讓 XML 元素保有索引，並可對結構描述進行驗證，因此幾乎是不可或缺的。  以下是我們最愛的兩種編輯器。

* [VS 程式碼](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

從入門套件的根資料夾取得 XML 結構描述定義 `TrustFrameworkPolicy_0.3.0.0.xsd`。 在上載之前，XML 結構描述驗證會識別錯誤。  請查看 XML 編輯器的文件以了解 `XML tools` 和 `XML Validation`

Azure AD B2C 會拒絕它所偵測到的任何格式錯誤，因此快速檢閱 XML 規則可能會對您很有幫助。  格式錯誤的 XML 可能會產生令人誤解的錯誤訊息。

## <a name="uploading-policies-and-policy-validation"></a>上傳原則和原則驗證

 **上傳**驗證結果的工作會自動進行，大部分的錯誤都是因為中止上傳所導致。  **請記住，驗證結果中會包含您嘗試上傳的原則檔案，以及其所參考之檔案的鏈結。  `RP policy file > Extensions file > Base File` 常見的驗證錯誤包括︰

錯誤程式碼片段︰`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* claimType 可能拼字錯誤，或不存在於結構描述中。
* ClaimTypes 至少必須定義於原則內的一個檔案。  例如 ` <ClaimType Id="socialIdpUserId">`
* 如果 ClaimType 定義於擴充檔案中，但用於基底檔案的 TechnichalProfile 中，則上傳基底檔案將會導致錯誤。

錯誤程式碼片段︰`...males a reference to a ClaimsTransformation with id...`
* 同上。

錯誤程式碼片段︰`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* 請確認 **<TrustFrameworkPolicy>** 和 **<BasePolicy>** 元素中的 TenantId 符合您的目標 B2C 租用戶。  



## <a name="runtime-troubleshooting"></a>執行階段疑難排解

* 使用 `Run Now` 和 `https://jwt.io`在不受 Web 或行動應用程式的影響下測試您的原則。 此網站的作用就像信賴憑證者應用程式，並且會顯示 Azure AD B2C 原則所產生的 JWT 權杖內容。  若要在身分識別體驗架構中建立測試用應用程式。
    * 名稱︰TestApp
    * Web 應用程式/Web API︰否
    * 原生用戶端︰否

* 使用 [fiddler](http://www.telerik.com/fiddler) 來追蹤用戶端瀏覽器和 Azure AD B2C 之間的訊息交換。  您會獲得指示，以了解使用者旅程是在協調流程步驟的哪一步失敗。

* 在**開發模式**下使用 **Application Insights** 來追蹤身分識別體驗架構 (IEF) 使用者旅程的行為。  在**開發模式**中，您可以觀察 IEF 和各種由 TechnicalProfiles 定義之宣告提供者之間所進行的宣告交換，這些提供者包括識別提供者、API 型服務、Azure AD B2C 使用者目錄 (AAD 目錄) 和其他服務 (如 Multi-Factor-Authentication)。  

## <a name="recommended-practies"></a>建議作法

**為案例保留多個版本，並將這些版本群組在應用程式的專案中。** 基底、擴充和信賴憑證者 (RP) 檔案直接彼此相依，因此請以群組方式儲存這些檔案，並在您的原則有新功能時保存不同的工作版本。  使用與這些檔案互動的應用程式程式碼，在您自己的檔案系統中分階段放入檔案。  應用程式可能會叫用租用戶中的不同 RP 原則，並逐漸依賴預期應該來自 Azure AD B2C 原則的宣告。

**使用已知的使用者旅程來開發和測試技術設定檔。**  使用經過測試的入門套件原則來設定技術設定檔，然後先分開測試這些設定檔，完成後再併入您自己的使用者旅程中

**使用經過測試的技術設定檔來開發和測試使用者旅程** 逐步變更使用者旅程的協調流程步驟，以漸進方式建置您想要的案例







現在就開始吧！

1. 從 GitHub 下載「active-directory-b2c-custom-policy-starterpack」。  [下載 zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 或執行
### <a name="built-in"></a>內建



Azure Active Directory (Azure AD) B2C 的可延伸原則架構是服務的核心力量。 原則可完整描述取用者身分識別體驗，例如註冊、登入或設定檔編輯。 比方說，註冊原則可讓進行下列設定來控制行為：

* 可供取用者用來註冊應用程式的帳戶類型 (例如 Facebook 等社交帳戶，或像是電子郵件地址的本機帳戶)。
* 註冊時向取用者收集的屬性 (例如名字、郵遞區號和鞋子尺寸)。
* 使用 Multi-Factor Authentication。
* 所有註冊頁面的外觀與風格。
* 原則執行完成時應用程式所接收的資訊 (以權杖中的宣告表示)。

您可以在租用戶中建立多個不同類型的原則，並視需要在您的應用程式中使用。 原則可以跨應用程式重複使用。 這可讓開發人員在少量變更或完全不變更程式碼的情況，定義及修改取用者身分識別體驗。

透過簡單的開發人員介面就可使用原則。 您的應用程式使用標準 HTTP 驗證要求來觸發原則 (在要求中傳遞原則參數)，並從回應中收到自訂的權杖。 例如，在叫用註冊原則的要求和叫用登入原則的要求之間，唯一的差別在於 "p" 查詢字串參數中使用的原則名稱：

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

如需原則架構的詳細資訊，請參閱此 [部落格文章](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)。

## <a name="create-a-sign-up-policy"></a>建立註冊原則
若要在您的應用程式中啟用註冊功能，您必須建立註冊原則。 此原則描述取用者在註冊期間將會經歷的體驗，以及成功註冊時，應用程式將收到的權杖內容。

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 按一下 [註冊原則] 。
3. 按一下刀鋒視窗頂端的 [+新增]  。
4. [名稱]  決定您的應用程式所使用的註冊原則名稱。 例如，輸入 "SiUp"。
5. 按一下 [身分識別提供者]  ，選取 [電子郵件註冊]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。 按一下 [確定] 。
6. 按一下 [註冊屬性] 。 您可以在這裡選擇註冊期間要向取用者收集的屬性。 例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。 按一下 [確定] 。
7. 按一下 [應用程式宣告] 。 您在這裡選擇成功註冊之後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[身分識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]。
8. 按一下 [建立] 。 請注意，剛建立的原則會在 [註冊原則] 刀鋒視窗中顯示為 "**B2C_1_SiUp**" (自動新增 **B2C\_1\_** 部分)。
9. 按一下 [B2C_1_SiUp] 以開啟原則。
10. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。
11. 按一下 [立即執行] 。 新的瀏覽器索引標籤隨即開啟，而您可以開始進行取用者體驗來註冊您的應用程式。
    
    > [!NOTE]
    > 建立和更新原則後，需要經過一分鐘才會生效。
    > 
    > 

## <a name="create-a-sign-in-policy"></a>建立登入原則
若要在您的應用程式中啟用登入功能，您必須建立登入原則。 此原則描述取用者在登入期間將經歷的體驗，以及成功登入時，應用程式將收到的權杖內容。

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 按一下 [登入原則] 。
3. 按一下刀鋒視窗頂端的 [+新增]  。
4. [名稱]  決定您的應用程式所使用的登入原則名稱。 例如，輸入 "SiIn"。
5. 按一下 [識別提供者]  ，選取 [本機帳戶登入]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。 按一下 [確定] 。
6. 按一下 [應用程式宣告] 。 您在這裡選擇成功登入後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[識別提供者]、[郵遞區號] 和 [使用者的物件識別碼]。 按一下 [確定] 。
7. 按一下 [建立] 。 請注意，剛建立的原則會在 [登入原則] 刀鋒視窗中顯示為 "**B2C_1_SiIn**" (自動新增 **B2C\_1\_** 部分)。
8. 按一下 [B2C_1_SiIn] 以開啟原則。
9. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。
10. 按一下 [立即執行] 。 新的瀏覽器索引標籤隨即開啟，您可以開始進行取用者體驗來登入您的應用程式。
    
    > [!NOTE]
    > 建立和更新原則後，需要經過一分鐘才會生效。
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>建立註冊或登入原則
此原則可使用單一組態處理取用者註冊和登入經驗。 視內容而定，取用者會被引導到正確的路徑 (註冊或登入)。 此原則也會描述在成功註冊或登入時，應用程式將收到的權杖內容。  您可以 [在這裡找到](active-directory-b2c-devquickstarts-web-dotnet-susi.md)註冊或登入原則的代碼範例。

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 按一下 [註冊或登入原則] 。
3. 按一下刀鋒視窗頂端的 [+新增]  。
4. [名稱]  決定您的應用程式所使用的註冊原則名稱。 例如，輸入 "SiUpIn"。
5. 按一下 [身分識別提供者]  ，選取 [電子郵件註冊]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。 按一下 [確定] 。
6. 按一下 [註冊屬性] 。 您可以在這裡選擇註冊期間要向取用者收集的屬性。 例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。 按一下 [確定] 。
7. 按一下 [應用程式宣告] 。 您在這裡選擇成功註冊或登入後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[身分識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]。
8. 按一下 [建立] 。 請注意，剛建立的原則會在 [註冊或登入原則] 刀鋒視窗中顯示為 "**B2C_1_SiUpIn**" (自動新增 **B2C\_1\_** 部分)。
9. 按一下 [B2C_1_SiUpIn] 以開啟原則。
10. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。
11. 按一下 [立即執行] 。 新的瀏覽器索引標籤隨即開啟，您可以開始依照設定進行註冊或登入取用者體驗。
    
    > [!NOTE]
    > 建立和更新原則後，需要經過一分鐘才會生效。
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>建立設定檔編輯原則
若要在您的應用程式中啟用設定檔編輯功能，您必須建立設定檔編輯原則。 此原則描述取用者在設定檔編輯期間將會經歷的體驗，以及成功完成時，應用程式將收到的權杖內容。

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 按一下 [設定檔編輯原則] 。
3. 按一下刀鋒視窗頂端的 [+新增]  。
4. [名稱]  決定您的應用程式所使用的設定檔編輯原則名稱。 例如，輸入 "SiPe"。
5. 按一下 [識別提供者]，選取 [本機帳戶登入]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。 按一下 [確定] 。
6. 按一下 [設定檔屬性] 。 您在這裡選擇取用者可以檢視及編輯的屬性。 例如，選取 [國家/區域]、[顯示名稱] 和 [郵遞區號]。 按一下 [確定] 。
7. 按一下 [應用程式宣告] 。 您在這裡選擇成功編輯設定檔後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱] 和 [郵遞區號]。
8. 按一下 [建立] 。 請注意，剛建立的原則會在 [設定檔編輯原則] 刀鋒視窗中顯示為 "**B2C_1_SiPe**" (會自動新增 **B2C\_1\_** 部分)。
9. 按一下 [B2C_1_SiPe] 以開啟原則。
10. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。
11. 按一下 [立即執行] 。 新的瀏覽器索引標籤隨即開啟，您可以開始在應用程式中進行設定檔編輯取用者體驗。
    
    > [!NOTE]
    > 建立和更新原則後，需要經過一分鐘才會生效。
    > 
    > 

## <a name="create-a-password-reset-policy"></a>建立密碼重設原則
若要在您的應用程式上啟用更細緻的密碼重設，您必須建立密碼重設原則。 請注意， [這裡](active-directory-b2c-reference-sspr.md) 指定的整個租用戶的密碼重設選項仍適用於登入原則。 此原則描述取用者在密碼重設期間將經歷的體驗，以及成功完成時，應用程式將收到的權杖內容。

1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
2. 按一下 [密碼重設原則] 。
3. 按一下刀鋒視窗頂端的 [+新增]  。
4. [名稱]  決定您的應用程式所使用的密碼重設原則名稱。 例如，輸入 "SSPR"。
5. 按一下 [身分識別提供者]  ，選取 [使用電子郵件地址重設密碼]。 按一下 [確定] 。
6. 按一下 [應用程式宣告] 。 您在這裡選擇在成功進行密碼重設體驗之後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [使用者的物件識別碼]。
7. 按一下 [建立] 。 請注意，剛建立的原則會在 [密碼重設原則] 刀鋒視窗中顯示為 "**B2C_1_SSPR**" (自動新增 **B2C\_1\_** 部分)。
8. 按一下 [B2C_1_SSPR] 以開啟原則。
9. 在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL / 重新導向 URI] 下拉式清單中選取 `https://localhost:44321/`。
10. 按一下 [立即執行] 。 新的瀏覽器索引標籤隨即開啟，您可以開始在應用程式中進行密碼重設取用者體驗。
    
    > [!NOTE]
    > 建立和更新原則後，需要經過一分鐘才會生效。
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>如何將註冊或登入原則與密碼重設原則連結在一起？
當您建立註冊或登入原則 (搭配本機帳戶) 時，取用者會在該體驗的第一個頁面上看到「忘記密碼?」 連結。 按一下此連結並不會自動觸發密碼重設原則。 取而代之的是，會將特定的錯誤碼 `AADB2C90118` 傳回到您的應用程式。 您的應用程式必須處理此錯誤碼，然後叫用特定的密碼重設原則。 如需可示範這個相連結原則的範例，請參閱[這裡](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

## <a name="additional-resources"></a>其他資源
* [權杖、工作階段及單一登入組態](active-directory-b2c-token-session-sso.md)。
* [在取用者註冊期間停用電子郵件驗證](active-directory-b2c-reference-disable-ev.md)


