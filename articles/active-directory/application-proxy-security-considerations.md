---
title: "使用 Azure AD 應用程式 Proxy 遠端存取應用程式時的安全性考量 | Microsoft Docs"
description: "涵蓋使用 Azure AD 應用程式 Proxy 時的安全性考量。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b01d0ddef2271c440cbff1684c85d23dcda9a1de
ms.openlocfilehash: 75d657412a61de38ab53708a8108d40309de742d


---

# <a name="security-considerations-when-accessing-apps-remotely-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 遠端存取應用程式時的安全性考量

>[!NOTE]
>應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
>  

本文說明 Azure AD 應用程式 Proxy 如何提供遠端發佈及存取應用程式的安全服務。 

Azure AD 應用程式 Proxy 提供下列安全性優點︰

**已驗證的存取︰**只有已驗證的連線可以存取您的網路

* Azure AD App Proxy 會仰賴 Azure AD STS 進行所有的驗證。 對於使用預先驗證發佈的應用程式，不允許無有效 STS 權杖的應用程式 Proxy 服務流量通過您的環境。
* 預先驗證 (就其本質) 會封鎖大量匿名攻擊，因為只有已驗證的身分識別可以存取後端應用程式。

**條件式存取︰**在建立您的網路連線之前，先套用更豐富的原則控制

* 使用條件式存取，就可以進一步定義允許哪些流量存取後端應用程式上的限制。 您可以根據位置、驗證強度和使用者風險狀況來定義限制。
* 此功能可使攻擊者受到其他障礙。 您可以深入閱讀條件式存取[開始使用 Azure Active Directory 條件式存取](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps)。

**流量終止︰**終止雲端中所有的流量

* Azure AD App Proxy 是反向 Proxy，因此所有至後端應用程式的流量會在服務終止。 僅可使用後端伺服器重新建立工作階段。 這表示您的後端伺服器不會對直接 HTTP 流量公開。 例如，您可以更輕鬆地降低目標的攻擊。

**所有存取都是輸出︰**不需要開啟任何到公司網路的輸入連線

* Azure AD 連接器會保持對 Azure AD App Proxy 服務的輸出連線。 這表示不需要針對連入連線開啟防火牆連接埠。 
* 傳統方法需要 DMZ 和在網路邊緣開啟存取未經授權的連線。 這會產生很多額外投資在 WAF 產品，需分析流量並對環境提供額外保護。 使用應用程式 Proxy，您可以避免這種情況。 您甚至可以考慮不使用 DMZ 進行，因為所有連接皆為輸出且透過安全通道。

**安全性分析和電腦語言為基礎的智慧︰**最新的安全性保護

* 使用電腦學習導向智慧與來自我們數位犯罪單位和 Microsoft Security Response Center 之摘要的 Azure AD Identity Protection。 我們共同主動識別遭入侵的帳戶，並提供來自高風險登入的即時防護。 我們考慮許多因素，例如來自受感染裝置和透過匿名網路，以及來自非典型與假位置的存取。
* 這些報告和事件中有許多已可透過 API 與 SIEM 系統整合。
* 您可以閱讀 [Azure Active Directory 身分識別保護](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection)，進一步了解 Azure AD Identity Protection。

**遠端存取即服務︰**您不必擔心維護及修補內部部署伺服器

* Azure AD App Proxy 是我們自己的網際網路級別服務，因此您可以確保永遠取得最新的安全性修補程式和升級。 因此，我們會負責處理您的網路安全性。
* 未更新的軟體仍需負責處理大量攻擊。 使用我們的服務模型，您不必再負擔管理邊緣伺服器。 

依據 [Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center)所概述之指導方針和標準，使用 Azure AD 運作所提供的遠端存取服務。 

下圖顯示 Azure AD 如何讓您在內部部署應用程式安全遠端存取。

 ![Azure AD 應用程式 Proxy 連接器](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="components-of-the-azure-ad-app-proxy-solution"></a>Azure AD App Proxy 解決方案的元件

Azure AD 應用程式 Proxy 是由兩個部分組成︰

* 雲端式服務 (AAD AP)。 這是進行外部用戶端/使用者連接之處。
* 內部部署元件呼叫 AAD App Proxy 連接器。  連接器會接聽來自 AAD AP 服務的要求，並處理內部應用程式，包括如 SSO 的 KCD (Kerberos 限制委派) 等項目。

### <a name="traffic-flowsand-how-they-are-secured"></a>流量流程，以及如何保護
本節包含有關如何保護流量的詳細資料。 以下為建立連接器與應用程式 Proxy 服務之間流量的時機︰ 

* 第一次設定連接器。
* 連接器會從應用程式 Proxy 服務提取組態資訊，包括每個連接器為其成員的連接器群組。
* 使用者會存取發佈的應用程式。

>[!NOTE]
>所有的通訊會透過 SSL 發生，且一律源自於至應用程式 Proxy 服務的連接器。 此服務僅輸出。
>

連接器會使用用戶端憑證來驗證所有呼叫的應用程式 Proxy 服務。 唯一的例外狀況是建立用戶端憑證的初始設定步驟。

#### <a name="install-the-connector"></a>安裝連接器

連接器第一次設定時，會發生下列流程。

1. 連接器註冊服務會做為連接器安裝的一部分。 此時，會提示使用者輸入其 Azure AD 系統管理員認證。 接著會向 Azure AD App Proxy 服務顯示必要權杖。
2. App Proxy 會評估權杖，以確保使用者在發行權杖的租用戶內是公司系統管理員角色的成員。 如果沒有，則處理序終止。
3. 連接器會產生用戶端憑證要求，並將此與權杖傳遞至應用程式 Proxy 服務，其會依次驗證權杖並簽署用戶端憑證要求。 
4. 連接器可以使用此用戶端憑證，以便未來與應用程式 Proxy 服務通訊。
4. 連接器會使用其用戶端憑證從服務執行系統設定資料的初始提取，並準備好要接受要求。

#### <a name="periodic-configuration-updates"></a>定期的組態更新

每次應用程式 Proxy 服務更新組態設定時，下列流程會定期發生。

1. 連接器會使用其用戶端憑證連接至應用程式 Proxy 服務內的組態端點。
2. 一旦用戶端憑證驗證後，應用程式 Proxy 服務會將組態資料傳回連接器；例如，連接器應屬之連接器群組。
3. 如果目前的憑證超過 30 天，每隔 30 天有效地循環用戶端憑證，連接器就會產生新的憑證要求。

#### <a name="accesss-published-applications"></a>存取已發佈的應用程式

當使用者存取已發佈的應用程式時，會發生下列流程。

1. 當使用者存取已發佈的應用程式時，應用程式 Proxy 服務會檢查組態設定的應用程式。  如果應用程式設定為以 Azure AD 使用預先驗證時，使用者會重新導向至 Azure AD STS 驗證。  如果您使用傳遞發佈應用程式，則會略過。
 * 在使用 Azure AD 驗證期間，我們會檢查特定應用程式的所有條件式存取原則需求。 這是為了確保使用者已指派至應用程式；(例如，如果需要 MFA，則驗證順序會提示使用者提出第二個因素驗證)。
 * 一旦已通過所有檢查，Azure AD STS 會針對應用程式發出已簽署權杖，並將使用者重新導向回到應用程式 Proxy 服務。
 * 然後應用程式 Proxy 會驗證權杖，以確定它已發行至使用者要求存取的應用程式。 它會與其他檢查共同進行此動作，例如確保權杖是由 Azure AD 所簽署，且仍在有效的視窗內等。
 * 應用程式 Proxy 會設定加密的驗證 cookie (例如非持續性 cookie)，以表示已發生應用程式驗證。  此 cookie 包含根據 Azure AD 的權杖和其他資料之到期時間戳記，例如以驗證為基礎的使用者名稱。  會使用僅應用程式 Proxy 服務所知的私密金鑰來加密此 cookie。
 * 應用程式 Proxy 會將使用者重新導向回到原始要求的 URL。
 >[!NOTE] 
 >如果預先驗證步驟的任何部分失敗，使用者的要求會遭到拒絕，且使用者會顯示訊息，指出問題的來源。
 >

2. 應用程式 Proxy 在收到來自用戶端的要求時，會驗證已符合預先驗證條件，且 cookie 仍然有效 (視需要)。 然後，就會在適當的佇列中提出要求，以供內部部署連接器處理。 

 >[!NOTE]
 >來自連接器的所有要求都會輸出到應用程式 Proxy 服務。 連接器保持對應用程式 Proxy 開啟輸出連線。 當要求傳入時，應用程式 Proxy 會在其中一個開啟連接上佇列要求，以供連接器挑選。
 >

 * 要求包含來自應用程式的項目，例如要求的標頭和來自加密 cookie 的資料、提出要求的使用者，以及要求識別碼。  但是，加密的驗證 cookie 不會傳送給連接器。
3. 連接器會根據長時間執行的輸出連線從佇列接收要求。 應用程式 Proxy 會根據要求執行下列其中一個動作︰
 * 連接器會確認是否可以找出應用程式。  如果不可，則連接器會建立到應用程式 Proxy 服務的連接，以收集關於應用程式的詳細資料，並在本機快取。
 * 如果要求是簡單的作業，例如主體內沒有資料現狀符合 RESTful “GET” 要求，連接器會建立到目標內部資源的連接並等候回應。
 * 如果要求在主體中具有與它相關聯的資料，例如 RESTful “POST” 作業，連接器會使用用戶端憑證建立與應用程式 Proxy 執行個體的輸出連接。 它會進行此動作來要求資料，並開啟與內部部署資源的連接。  在收到來自連接器的要求時，應用程式 Proxy 服務會開始接受來自使用者的內容，並將此轉寄至連接器。  連接器會依次將資料轉送到內部資源。
4. 一旦完成所有內容的要求/傳輸至後端，連接器會等候回應。
5. 一旦收到回應時，連接器會建立對應用程式 Proxy 服務的輸出連接，以傳回標頭詳細資料，並開始串流傳回的資料。
6. 應用程式 Proxy「串流」資料給使用者。  標頭的一些處理可能會視需要發生在這裡，並由應用程式定義。

如果您需要從 Azure web 應用程式以用戶端瀏覽器方式通訊內部部署 Windows 驗證之簡易物件存取 Protocal (SOAP) 端點的協助，請參閱此 [Azure 欄位筆記部落格](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy)。 

##<a name="next-steps"></a>後續步驟
[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)




<!--HONumber=Feb17_HO1-->


