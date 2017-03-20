---
title: "Azure Active Directory 中可設定的權杖存留期 | Microsoft Docs"
description: "這項功能可供系統管理員和開發人員用來指定 Azure AD 所簽發之權杖的存留期。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: d7e635f7e84ac53399309bf4ec8a7fa9e70e3728
ms.openlocfilehash: aa18efb0c622ae38eea0de28f25c72788e6d0f20
ms.lasthandoff: 03/01/2017


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Azure Active Directory 中可設定的權杖存留期 (公開預覽版)
> [!NOTE]
> 這項功能目前為公開預覽版。  您應做好將任何變更還原或移除的準備。  我們在公開預覽版期間開放這項功能供所有人測試，不過，一旦正式運作之後，可能需要 [Azure AD Premium 訂用帳戶](active-directory-get-started-premium.md)才能使用某些層面。
> 
> 

## <a name="introduction"></a>簡介
這項功能可供系統管理員和開發人員用來指定 Azure AD 所簽發之權杖的存留期。 不論是針對組織中所有的應用程式、針對多租用戶 (多組織) 應用程式，還是針對組織中特定的服務主體，都可以設定權杖存留期。

在 Azure AD 中，原則專案代表在組織中個別應用程式或所有應用程式上強制執行的一組規則。  每個類型的原則都具有包含一組屬性的獨特結構，這些屬性會接著套用至它們已被指派的物件。

您可以將原則指定為組織的預設原則。 接著，只要此原則不被優先順序更高的原則覆寫，就會對該組織內的所有應用程式生效。 您也可以將原則指派給特定的應用程式。 優先順序會因原則類型而異。

您可以針對重新整理權杖、存取權杖及識別碼權杖設定權杖存留期。

### <a name="access-tokens"></a>存取權杖
存取權杖可供用戶端用來存取受保護的資源。 存取權杖僅可用於特定的使用者、用戶端及資源的組合。 存取權杖是不可撤銷的，在到期之前都會一直有效。 惡意執行者若已取得存取權杖，便可在權杖的存留期範圍內使用該權杖。  調整存取權杖存留期是在改進系統效能與增加用戶端在使用者帳戶停用後保留存取權的時間，這兩者之間所做的一項權衡取捨。  系統效能的改進是藉由減少用戶端需要取得新存取權杖的次數來達成。 

### <a name="refresh-tokens"></a>重新整理權杖
當用戶端取得存取權杖來存取受保護的資源時，會同時收到重新整理權杖和存取權杖。 當存取權杖到期時，可以使用重新整理權杖來取得一組新的存取/重新整理權杖。 重新整理權杖會繫結至使用者與用戶端的組合。 這些權杖是可撤銷的，而每次使用這些權杖時，系統都會檢查其有效性。

區別機密用戶端與公開用戶端相當重要。 如需不同類型用戶端的詳細資訊，請參閱 [RFC 6749 (英文)](https://tools.ietf.org/html/rfc6749#section-2.1)。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>具有機密用戶端重新整理權杖的權杖存留期
機密用戶端是能夠安全地儲存用戶端密碼的應用程式，它們能夠證明要求來自用戶端應用程式而不是惡意執行者。 例如，Web 應用程式是機密用戶端，因為它可在 Web 伺服器上儲存用戶端密碼，因此不是公開的。 因為這些流程較安全，所以簽發給這些流程的重新整理權杖預設存留期會較長，並且無法使用原則來變更。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>具有公開用戶端重新整理權杖的權杖存留期 

公開用戶端無法安全地儲存用戶端密碼。 例如，iOS/Android 應用程式無法模糊來自資源擁有者的密碼，因此被視為公開用戶端。  您可以在資源上設定原則，讓來自公開用戶端的重新整理權杖只要超過指定的期間 (重新整理權杖最大閒置時間)，便無法取得一組新的存取/重新整理權杖。  此外，原則也可用來設定可接受重新整理權杖的期間 (重新整理權杖最大壽命)。  調整重新整理權杖存留期將可讓您控制當使用者使用公開用戶端應用程式時，必須在何時及隔多久重新輸入一次認證，而不是以無訊息方式重新驗證。

### <a name="id-tokens"></a>ID 權杖
識別碼權杖會傳遞給網站和原生用戶端，且權杖中包含了使用者的相關設定檔資訊。 識別碼權杖會繫結至特定的使用者與用戶端組合。 識別碼權杖在到期前都會被視為有效。  通常，Web 應用程式會將應用程式中的使用者工作階段存留期，與針對該使用者簽發之識別碼權杖的存留期做比對。  調整識別碼權杖存留期可讓您控制 Web 應用程式讓應用程式工作階段到期並要求使用者重新向 Azure AD 進行驗證 (以無訊息方式或以互動方式) 的頻率。

### <a name="single-sign-on-session-token"></a>單一登入工作階段權杖
當使用者向 Azure AD 進行驗證並勾選 [讓我保持登入] 方塊時，系統會在使用者的瀏覽器和 Azure AD 建立單一登入工作階段。  「單一登入工作階段權杖」(採用 Cookie 的形式) 即代表此工作階段。 請特別注意，SSO 工作階段權杖不會繫結至特定的資源/用戶端應用程式。 SSO 工作階段權杖是可撤銷的，而每次使用這些權杖時，系統都會檢查其有效性。

SSO 工作階段權杖有兩種。 持續性工作階段權杖會由瀏覽器儲存為持續性 Cookie，非持續性工作階段權杖則會儲存為工作階段 Cookie (這些 Cookie 會在瀏覽器關閉時銷毀)。

非持續性工作階段權杖的存留期為 24 小時，持續性權杖的存留期則為 180 天。 只要在 SSO 工作階段權杖的有效期內使用此權杖，有效期就會再延長 24 小時或 180 天。 如果未在 SSO 工作階段權杖的有效期內使用此權杖，系統就會將其視為過期而不再接受它。 

您可以使用原則來設定簽發第一個工作階段權杖之後可接受工作階段權杖的期間 (工作階段權杖最大壽命)。  調整工作階段權杖存留期將可讓您控制當使用者使用 Web 應用程式時，必須在何時及隔多久重新輸入一次認證，而不是以無訊息方式重新驗證。

### <a name="token-lifetime-policy-properties"></a>權杖存留期原則屬性
權杖存留期原則是一種包含權杖存留期規則的原則物件。  原則的屬性可用來控制指定的權杖存留期。  如果未設定任何原則，系統就會強制執行預設存留期值。

### <a name="configurable-token-lifetime-properties"></a>可設定的權杖存留期屬性
| 屬性 | 原則屬性字串 | 影響 | 預設值 | 最小值 | 最大值 |
| --- | --- | --- | --- | --- | --- |
| 存取權杖存留期 |AccessTokenLifetime |存取權杖、識別碼權杖、SAML2 權杖 |1 小時 |10 分鐘 |1 天 |
| 重新整理權杖最大閒置時間 |MaxInactiveTime |重新整理權杖 |14 天 |10 分鐘 |90 天 |
| 單一要素重新整理權杖最大壽命 |MaxAgeSingleFactor |重新整理權杖 (適用於任何使用者) |90 天 |10 分鐘 |直到撤銷為止* |
| 多重要素重新整理權杖最大壽命 |MaxAgeMultiFactor |重新整理權杖 (適用於任何使用者) |90 天 |10 分鐘 |直到撤銷為止* |
| 單一要素工作階段權杖最大壽命 |MaxAgeSessionSingleFactor** |工作階段權杖 (持續性和非持續性) |直到撤銷為止 |10 分鐘 |直到撤銷為止* |
| 多重要素工作階段權杖最大壽命 |MaxAgeSessionMultiFactor*** |工作階段權杖 (持續性和非持續性) |直到撤銷為止 |10 分鐘 |直到撤銷為止* |

* *針對這些屬性，可設定的明確時間長度上限為&365; 天。
* **如果未設定 MaxAgeSessionSingleFactor，則此值會採用 MaxAgeSingleFactor 值。 如果兩個參數都未設定，此屬性就會接受預設值 (即直到撤銷為止)。
* **如果未設定 MaxAgeSessionMultiFactor，則此值會採用 MaxAgeMultiFactor 值。 如果兩個參數都未設定，此屬性就會接受預設值 (即直到撤銷為止)。

### <a name="exceptions"></a>例外狀況
| 屬性 | 影響 | 預設值 |
| --- | --- | --- |
| 重新整理權杖最大閒置時間 (沒有足夠撤銷資訊的同盟使用者) |重新整理權杖 (針對沒有足夠撤銷資訊的同盟使用者簽發) |12 小時 |
| 重新整理權杖最大閒置時間 (機密用戶端) |重新整理權杖 (針對機密用戶端簽發) |90 天 |
| 重新整理權杖最大壽命 (針對機密用戶端簽發) |重新整理權杖 (針對機密用戶端簽發) |直到撤銷為止 |

### <a name="priority-and-evaluation-of-policies"></a>原則的優先順序和評估
您可以建立「權杖存留期」原則，並將其指派給特定的應用程式、組織及服務主體。 這意謂著可以將多個原則套用至某個特定的應用程式。 生效的「權杖存留期」原則會遵循下列規則：

* 如果已將原則明確指派給服務主體，就會強制執行該原則。 
* 如果未將任何原則明確指派給服務主體，則會強制執行指派給該服務主體之父組織的原則。 
* 如果未將任何原則明確指派給服務主體或組織，則會強制執行指派給應用程式的原則。 
* 如果未將任何原則明確指派給服務主體、組織或應用程式物件，將會強制執行預設值 (請參閱上表)。

如需有關 Azure AD 中應用程式物件與服務主體物件之間關係的詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](active-directory-application-objects.md)。

使用權杖時，系統便會評估權杖的有效性。 在所要存取之應用程式上優先順序最高的原則會生效。

> [!NOTE]
> 範例
> 
> 使用者想要存取 A 和 B 這兩個 Web 應用程式。 
> 
> * 兩個應用程式都在相同的父組織中。 
> * 「工作階段權杖最大壽命」為 8 小時的權杖存留期原則 1 已設定為父組織的預設值。
> * Web 應用程式 A 是一個一般用途 Web 應用程式且未與任何原則連結。 
> * Web 應用程式 B 用於高機密性程序，且其服務主體與「工作階段權杖最大壽命」為 30 分鐘的權杖存留期原則 2 連結。
> 
> 在下午 12:00，使用者開啟新的瀏覽器工作階段並嘗試存取 Web 應用程式 A。系統會將使用者重新導向到 Azure AD 並要求使用者登入。 這會在瀏覽器中留下一個帶有工作階段權杖的 Cookie。 系統會將使用者重新導向回 Web 應用程式 A，其中會提供一個可讓使用者存取該應用程式的識別碼權杖。
> 
> 接著，在下午 12:15，使用者嘗試存取 Web 應用程式 B。瀏覽器會重新導向到 Azure AD 來偵測工作階段 Cookie。 Web 應用程式 B 的服務主體會與原則 2 連結，但同時也是帶有預設原則 1 之父組織的一部分。 原則 2 會生效，因為與服務主體連結之原則的優先順序高於組織預設原則。 工作階段權杖原先是在過去 30 分鐘內簽發的，因此被視為有效。 系統會將使用者重新導向回 Web 應用程式 B，其中會提供一個授與使用者存取權的識別碼權杖。
> 
> 在下午 1:00，使用者嘗試瀏覽至 Web 應用程式 A。系統會將使用者重新導向到 Azure AD。 Web 應用程式 A 並未與任何原則連結，但由於它位於帶有預設原則 1 的組織中，因此這個原則會生效。 系統偵測到工作階段 Cookie 原先是在過去 8 小時內簽發的，因此會以無訊息方式將使用者重新導向回 Web 應用程式 A 並提供一個新的識別碼權杖，而不需重新進行驗證。
> 
> 使用者立即嘗試存取 Web 應用程式 B。系統會將使用者重新導向到 Azure AD。 如同之前，原則 2 會生效。 由於權杖是在超過 30 分鐘之前簽發的，因此系統會接著提示使用者重新輸入其認證，然後簽發全新的工作階段權杖和識別碼權杖。 接著，使用者便可存取 Web 應用程式 B。
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>可設定的原則屬性：深入探討
### <a name="access-token-lifetime"></a>存取權杖存留期
**字串：**AccessTokenLifetime

**影像：**存取權杖、識別碼權杖

**摘要：**此原則可控制將此資源的存取權杖和識別碼權杖視為有效的期限。 縮短存取權杖存留期可降低惡意執行者持續一段長時間使用存取權杖或識別碼權杖的風險 (因為這些權杖無法被撤銷)，但同時也會為效能帶來負面影響，因為使用者將必須更頻繁地更換權杖。

### <a name="refresh-token-max-inactive-time"></a>重新整理權杖最大閒置時間
**字串︰**MaxInactiveTime

**影響：**重新整理權杖

**摘要：**此原則可控制在簽發重新整理權杖多久之後，用戶端才不能在嘗試存取此資源時，再使用該權杖來擷取一組新的存取權杖/重新整理權杖。 由於使用重新整理權杖時通常會傳回一個新的重新整理權杖，因此用戶端不得在指定的期間內，於此原則會防止存取之前，就使用目前的重新整理權杖來存取任何資源。 

此原則會強制尚未在其用戶端上變成作用中的使用者必須重新驗證，才能擷取新的重新整理權杖。 

請務必注意，「重新整理權杖最大閒置時間」必須設定成低於「單一要素權杖最大壽命」和「多重要素重新整理權杖最大壽命」的值。

### <a name="single-factor-refresh-token-max-age"></a>單一要素重新整理權杖最大壽命
**字串︰**MaxAgeSingleFactor

**影響：**重新整理權杖

**摘要︰**此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久繼續使用重新整理權杖來取得一組新的存取/重新整理權杖。 在使用者驗證並收到新的重新整理權杖之後，他們將能夠在指定的期間內使用重新整理權杖流程 (只要目前的重新整理權杖沒有被撤銷，且保持未使用狀態的時間未超過閒置時間即可)。 到那時，系統將會強制使用者必須重新驗證，才能收到新的重新整理權杖。 

縮短最大壽命將會強制使用者更頻繁地進行驗證。 由於單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此原則設定為等於或小於「多重要素重新整理權杖最大壽命」原則的值。

### <a name="multi-factor-refresh-token-max-age"></a>多重要素重新整理權杖最大壽命
**字串︰**MaxAgeMultiFactor

**影響：**重新整理權杖

**摘要︰**此原則可控制使用者在上次以多重要素成功驗證之後，可以持續多久繼續使用重新整理權杖來取得一組新的存取/重新整理權杖。 在使用者驗證並收到新的重新整理權杖之後，他們將能夠在指定的期間內使用重新整理權杖流程 (只要目前的重新整理權杖沒有被撤銷，且保持未使用狀態的時間未超過閒置時間即可)。 到那時，系統將會強制使用者必須重新驗證，才能收到新的重新整理權杖。 

縮短最大壽命將會強制使用者更頻繁地進行驗證。 由於單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此原則設定為等於或大於「單一要素重新整理權杖最大壽命」原則的值。

### <a name="single-factor-session-token-max-age"></a>單一要素工作階段權杖最大壽命
**字串︰**MaxAgeSessionSingleFactor

**影響：**工作階段權杖 (持續性和非持續性)

**摘要︰**此原則可控制使用者在上次僅以單一要素成功驗證之後，可以持續多久繼續使用工作階段權杖來取得新的識別碼權杖和工作階段權杖。 在使用者驗證並收到新的工作階段權杖之後，他們將能夠在指定的期間內使用工作階段權杖流程 (只要目前的工作階段權杖沒有被撤銷或過期即可)。 到那時，系統將會強制使用者必須重新驗證，才能收到新的工作階段權杖。 

縮短最大壽命將會強制使用者更頻繁地進行驗證。 由於單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此原則設定為等於或小於「多重要素工作階段權杖最大壽命」原則的值。

### <a name="multi-factor-session-token-max-age"></a>多重要素工作階段權杖最大壽命
**字串︰**MaxAgeSessionMultiFactor

**影響：**工作階段權杖 (持續性和非持續性)

**摘要︰**此原則可控制使用者在上次以多重要素成功驗證之後，可以持續多久繼續使用工作階段權杖來取得新的識別碼權杖和工作階段權杖。 在使用者驗證並收到新的工作階段權杖之後，他們將能夠在指定的期間內使用工作階段權杖流程 (只要目前的工作階段權杖沒有被撤銷或過期即可)。 到那時，系統將會強制使用者必須重新驗證，才能收到新的工作階段權杖。 

縮短最大壽命將會強制使用者更頻繁地進行驗證。 由於單一要素驗證的安全性被視為比多重要素驗證低，因此建議將此原則設定為等於或大於「單一要素工作階段權杖最大壽命」原則的值。

## <a name="sample-token-lifetime-policies"></a>範例權杖存留期原則
能夠為應用程式、服務主體及您整體組織建立及管理權杖存留期，揭露了各種在 Azure AD 中可能的新案例。  我們將逐步解說一些常見的原則案例，這些案例將協助您強制實行下列各項的新規則：

* 權杖存留期
* 權杖最大閒置時間
* 權杖最大壽命

我們將逐步解說一些案例，包括：

* 管理組織的預設原則
* 為 Web 登入建立原則
* 為呼叫 Web API 的原生應用程式建立原則
* 管理進階原則 

### <a name="prerequisites"></a>必要條件
在範例案例中，我們將在應用程式、服務主體及您的整體組織上建立、更新、連結及刪除原則。  如果您不熟悉 Azure AD，請先參閱[這篇文章](active-directory-howto-tenant.md)來協助您開始使用，然後再繼續進行這些範例。  

1. 若要開始，請下載最新的 [Azure AD PowerShell Cmdlet 預覽版](https://www.powershellgallery.com/packages/AzureADPreview)。 
2. 有了 Azure AD PowerShell Cmdlet 之後，請執行 Connect 命令來登入您的 Azure AD 系統管理員帳戶。 每次您啟動新工作階段時，都必須執行此操作。

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. 執行下列命令以查看已在您組織中建立的所有原則。  在下列案例中的大多數操作之後，都應該使用此命令。  它也會協助您取得原則的 **ObjectId**。 
   
    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="sample-managing-a-organizations-default-policy"></a>範例：管理組織的預設原則
在此範例中，我們將建立可讓使用者在您整個組織中降低登入頻率的原則。 

為了這樣做，我們將為「單一要素重新整理權杖」建立一個在整個組織套用的權杖存留期原則。 此原則將套用至您組織中的每個應用程式，以及每個尚未設定原則的服務主體。 

#### <a name="1-create-a-token-lifetime-policy"></a>1.建立權杖存留期原則

將「單一要素重新整理權杖」設定為「直到撤銷為止」，這意謂著必須等到存取權被撤銷之後，權杖才會失效。  以下的原則定義就是我們將建立的原則定義：

```PowerShell
@('{
    "TokenLifetimePolicy":
    {
        "Version":1, 
        "MaxAgeSingleFactor":"until-revoked"
    }
}')
```

接著，執行下列命令來建立此原則。 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

若要查看您的新原則並取得其 ObjectId，請執行下列命令。

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-update-the-policy"></a>2.更新原則

您已決定讓第一個原則不要像您服務所需的那樣嚴格，並已決定讓「單一要素重新整理權杖」在 2 天後到期。 執行下列命令。 

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
```

#### <a name="3-youre-done"></a>3.大功告成！ 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>範例：為 Web 登入建立原則

在此範例中，我們將建立會要求使用者提高驗證頻率來登入 Web 應用程式的原則。 此原則會為 Web 應用程式的服務主體設定「存取權杖」/「識別碼權杖」的存留期及「多重要素工作階段權杖」的「最大壽命」。

#### <a name="1-create-a-token-lifetime-policy"></a>1.建立權杖存留期原則。

這個 Web 登入原則會將「存取權杖」/「識別碼權杖」的存留期及「單一要素工作階段權杖最大壽命」設定為 2 小時。

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

若要查看您的新原則並取得其 ObjectId，請執行下列命令。

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-service-principal"></a>2.將原則指派給服務主體。

我們將把這個新原則與服務主體連結。  您也將需要一個可存取服務主體之 **ObjectId** 的方式。 您可以查詢 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) 或移至我們的 [Graph Explorer 工具](https://graphexplorer.cloudapp.net/)並登入您的 Azure AD 帳戶，來查看您所有組織的服務主體。 

有了 **ObjectId** 之後，請執行下列命令。

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3.大功告成！ 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>範例：為呼叫 Web API 的原生應用程式建立原則
在此範例中，我們將建立要求使用者降低驗證頻率的原則，而此原則將延長使用者可處於閒置狀態而不需再次驗證的時間。 此原則將套用至 Web API，如此一來，當原生應用程式要求它作為資源時，就會套用此原則。

#### <a name="1-create-a-token-lifetime-policy"></a>1.建立權杖存留期原則。 

此命令會為 Web API 建立一個嚴格的原則。 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
```

若要查看您的新原則並取得其 ObjectId，請執行下列命令。

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-your-web-api"></a>2.將原則指派給 Web API。

我們將把這個新原則與應用程式連結。  您也將需要一個可存取應用程式之 **ObjectId** 的方式。 尋找您應用程式之 **ObjectId** 的最佳方式就是使用 [Azure 入口網站](https://portal.azure.com/)。 

有了 **ObjectId** 之後，請執行下列命令。

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-youre-done"></a>3.大功告成！ 

### <a name="sample-managing-an-advanced-policy"></a>範例：管理進階原則
在此範例中，我們將建立一些原則來示範優先順序系統如何運作，以及您如何管理套用至數個物件的多個原則。 這將針對上述原則的優先順序提供一些深入見解，並且還將協助您管理更複雜的案例。 

#### <a name="1-create-a-token-lifetime-policy"></a>1.建立權杖存留期原則。

到目前為止，很簡單。 我們已經建立一個將「單一要素重新整理權杖」存留期設定為 30 天的組織預設原則。 

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

若要查看您的新原則並取得其 ObjectId，請執行下列命令。

```PowerShell
Get-AzureADPolicy
```

#### <a name="2-assign-the-policy-to-a-service-principal"></a>2.將原則指派給服務主體。

現在，我們在整個組織上有一個原則。  假設我們想要針對特定的服務主體保留這個 30 天原則，但是將組織預設原則變更為上限「直到撤銷為止」。 

首先，我們將把這個新原則與服務主體連結。  您也將需要一個可存取服務主體之 **ObjectId** 的方式。 您可以查詢 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) 或移至我們的 [Graph Explorer 工具](https://graphexplorer.cloudapp.net/)並登入您的 Azure AD 帳戶，來查看您所有組織的服務主體。 

有了 **ObjectId** 之後，請執行下列命令。

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
```

#### <a name="3-set-the-isorganizationdefault-flag-to-false"></a>3.將 IsOrganizationDefault 旗標設定為 flase。

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
```

#### <a name="4-create-a-new-organization-default-policy"></a>4.建立新的組織預設原則。

```PowerShell
New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
```

#### <a name="5-youre-done"></a>5.大功告成！ 

現在，原始原則已連結至您的服務主體，且新原則已設定為您的組織預設原則。  請務必記住，套用至服務主體的原則優先順序會高於組織預設原則。 

## <a name="cmdlet-reference"></a>Cmdlet 參考

### <a name="manage-policies"></a>管理原則

下列 Cmdlet 可用來管理原則。

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

建立新的原則。

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;Definition</code> |包含原則之所有規則的字串化 JSON 陣列。 | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |原則名稱的字串 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |如果為 true，就會將原則設定為組織的預設原則，如果為 false，則不會執行任何動作 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |原則的類型，針對權杖存留期，請一律使用 "TokenLifetimePolicy" | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [選用] |設定原則的替代識別碼。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
取得所有 AzureAD 原則或指定的原則 

```PowerShell
Get-AzureADPolicy 
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> [選用] |您想要取得之原則的 ObjectId。 |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
取得與原則連結的所有應用程式和服務主體

```PowerShell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of Policy> 
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |您想要取得之原則的 ObjectId。 |`-ObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
更新現有的原則

```PowerShell
Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName <string> 
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |您想要取得之原則的 ObjectId。 |`-ObjectId <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |原則名稱的字串 |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [選用] |包含原則之所有規則的字串化 JSON 陣列。 |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [選用] |如果為 true，就會將原則設定為組織的預設原則，如果為 false，則不會執行任何動作 |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [選用] |原則的類型，針對權杖存留期，請一律使用 "TokenLifetimePolicy" |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [選用] |設定原則的替代識別碼。 |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
刪除指定的原則

```PowerShell
 Remove-AzureADPolicy -ObjectId <ObjectId of Policy>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |您想要取得之原則的 ObjectId。 | `-ObjectId <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>應用程式原則
下列 Cmdlet 可用於應用程式原則。</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
將指定的原則連結至應用程式

```PowerShell
Add-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |原則的 ObjectId。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
取得指派給應用程式的原則

```PowerShell
Get-AzureADApplicationPolicy -ObjectId <ObjectId of Application>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
從應用程式移除原則

```PowerShell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |原則的 ObjectId。 | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>服務主體原則
下列 Cmdlet 可用於服務主體原則。</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
將指定的原則連結至服務主體

```PowerShell
Add-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |原則的 ObjectId。 | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
取得與指定的服務主體連結的任何原則

```PowerShell
Get-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
從指定的服務主體移除原則

```PowerShell
Remove-AzureADServicePrincipalPolicy -ObjectId <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| 參數 | 說明 | 範例 |
| --- | --- | --- |
| <code>&#8209;ObjectId</code> |應用程式的 ObjectId。 | `-ObjectId <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |原則的 ObjectId。 | `-PolicyId <ObjectId of Policy>` |


