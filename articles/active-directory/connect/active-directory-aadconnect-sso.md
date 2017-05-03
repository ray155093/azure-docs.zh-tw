---
title: "Azure AD Connect：順暢單一登入 | Microsoft Docs"
description: "本主題描述 Azure Active Directory (Azure AD) 的順暢單一登入，以及它如何讓您為公司網路內部的公司桌面使用者，提供真正的單一登入。"
services: active-directory
keywords: "何謂 Azure AD Connect、安裝 Active Directory、Azure AD、SSO、單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 294a7b7de5c0a95f9f0784f315f202ae2c062e57
ms.lasthandoff: 04/22/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory 順暢單一登入

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>何謂 Azure Active Directory 順暢單一登入？

Azure Active Directory 順暢單一登入 (Azure AD 順暢 SSO) 可以為使用者提供真正的單一登入，讓他們登入到已連線到公司網路的公司電腦。 當此功能啟用時，使用者不需要輸入密碼就能登入 Azure AD，而且在大部分情況下，甚至也不用輸入其使用者名稱。 這項功能可讓使用者輕鬆存取雲端架構服務，而不需要任何額外的內部部署元件。

順暢 SSO 可以透過 Azure AD Connect 來啟用，並可與[密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)或[傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)結合。

>[!NOTE]
>這項功能不適用於 Active Directory Federation Services (ADFS)，因為 ADFS 已提供這項功能。

若要為特定使用者啟用這項功能，您必須符合下列條件︰

- 使用者是在公司桌面上登入。
- 桌面先前已加入您的 Active Directory (AD) 網域。
- 桌面能夠直接連線至網域控制站 (DC)，不論是在公司的有線或無線網路上進行，還是透過遠端存取連線 (例如 VPN 連線) 來進行。
- 您已將我們的服務端點納入到瀏覽器的內部網路區域。

如果上述條件有任何一個未能符合，系統就會如往常一樣，提示使用者輸入其使用者名稱和密碼。

![順暢單一登入](./media/active-directory-aadconnect-sso/sso1.png)

## <a name="whats-available-during-preview"></a>預覽期間有什麼可用功能？

>[!NOTE]
>Azure AD 順暢 SSO 目前為預覽狀態。 這是免費功能，您不需要任何付費的 Azure AD 版本即可使用。

順暢 SSO 是透過能夠使用 Kerberos 驗證的桌面 (例如 Windows 架構機器) 上，支援[新式驗證](https://aka.ms/modernauthga)的 Web 瀏覽器型用戶端和 Office 用戶端來支援。 下面的矩陣提供多種作業系統上瀏覽器架構用戶端的詳細資料。

| 作業系統\瀏覽器 |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|是|是|是*|否
|Windows 8.1|是|是|是*|N/A
|Windows 8|是|是|是*|N/A
|Windows 7|是|是|是*|N/A
|Mac|N/A|否|否|N/A

\*需要其他設定。

>[!NOTE]
>對於 Windows 10，建議使用 [Azure AD Join](../active-directory-azureadjoin-overview.md) 以獲得 Azure AD 最佳使用體驗。

## <a name="how-does-azure-ad-seamless-sso-work"></a>Azure AD 順暢 SSO 如何運作？

您可以在 Azure AD Connect 中啟用順暢 SSO，[如下所示](#how-to-enable-azure-ad-seamless-sso?)。 啟用後，系統會在內部部署 Active Directory (AD) 中建立名為 AZUREADSSOACCT 的電腦帳戶，並和 Azure AD 安全地共用其 Kerberos 解密金鑰。 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個服務 URL。

此設定完成之後，Azure AD 登入的運作方式與其他任何使用整合式 Windows 驗證 (IWA) 的登入相同。 順暢 SSO 程序的運作方式如下所示︰

假設您的使用者嘗試存取 Azure AD 所保護的雲端架構資源，例如 SharePoint Online。 SharePoint Online 會將使用者的瀏覽器重新導向至 Azure AD 以進行登入。

- 如果對於 Azure AD 的登入要求包含 `domain_hint` (識別您的 Azure AD 租用戶；例如 contoso.onmicrosoft.com) 或 `login_hint` (識別使用者的使用者名稱；例如 user@contoso.onmicrosoft.com 或 user@contoso.com) 參數，則會發生下列步驟。
- 如果這兩個參數有任何一個未納入 Azure AD 的登入要求中，系統就會要求使用者提供他們的使用者名稱，之後就會發生下列步驟。

1. Azure AD 資源會透過 401 未授權回應挑戰用戶端，以提供 Kerberos 票證。
2. 用戶端從 Active Directory 要求 Azure AD (由先前所設定的電腦帳戶來表示) 的票證。
3. Active Directory 會找出該電腦帳戶，將 Kerberos 票證傳回給用戶端 (使用電腦帳戶的祕密加密)。 票證包括目前已登入桌面之使用者的身分識別。
4. 用戶端將它向 Active Directory 取得的 Kerberos 票證傳遞至 Azure AD。
5. Azure AD 會使用之前共用的金鑰將 Kerberos 票證解密。 如果成功，Azure AD 會傳回權杖，或要求使用者依照資源的要求來執行其他證明，例如 Multi-Factor Authentication。

順暢 SSO 是一個隨機功能，這表示如果它因為某些原因而失敗，使用者只需要像往常一樣在登入頁面上輸入他們的密碼即可。

完整的程序也會顯示在下圖中︰

![順暢單一登入](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>如何啟用 Azure AD 順暢 SSO？

### <a name="pre-requisites"></a>必要條件

如果您要啟用「順暢 SSO」並搭配「傳遞驗證」，則只需要符合「傳遞驗證」功能的必要條件。

如果您要啟用「順暢 SSO」並搭配「密碼同步處理」，而且如果 Azure AD Connect 和 Azure AD 之間有防火牆，請確定︰

- Azure AD Connect 伺服器可以與 `*.msappproxy.net` URL 通訊。
- Azure AD Connect (1.1.484.0 版或更新版本) 可以透過連接埠 443 向 Azure AD發出 HTTPS 要求。 這只能用於啟用此功能，不適用於實際的使用者登入。

>[!NOTE]
> 舊版的 Azure AD Connect (低於 1.1.484.0) 必須能夠透過連接埠 9090 與 Azure AD 進行通訊。

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>啟用 Azure AD 順暢 SSO 功能

您可以透過 Azure AD Connect 啟用 Azure AD 順暢 SSO。

如果您要執行新安裝的 Azure AD Connect，請選擇[自訂的安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，勾選 [啟用單一登入] 選項。

![Azure AD Connect - 使用者登入](./media/active-directory-aadconnect-sso/sso8.png)

如果您已經安裝 Azure AD Connect，可使用[快速安裝](active-directory-aadconnect-get-started-express.md)或[自訂安裝](active-directory-aadconnect-get-started-custom.md)路徑來設定，在 Azure AD Connect 上選擇 [變更使用者登入] 頁面，然後按 [下一步]。 然後勾選 [啟用單一登入] 選項。

![Azure AD Connect - 變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

繼續執行安裝精靈，直到抵達 [啟用單一登入] 頁面。 對於您同步處理至 Azure AD (透過 Azure AD Connect) 的每個 AD 樹系，以及您想要為其使用者啟用順暢 SSO 者，您必須提供網域系統管理員認證。 請注意，網域系統管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中，而只會用來建立電腦帳戶和設定 Kerberos SPN，如先前所述。

此時，您的租用戶已啟用順暢 SSO。 請注意，您仍需先完成下一節的步驟，使用者才能受惠於這項功能。

## <a name="rolling-the-feature-out-to-your-users"></a>對使用者推出此功能

根據預設，瀏覽器不會將 Kerberos 票證傳送給雲端端點，除非您已將其 URL 定義為瀏覽器內部網路區域的一部分。 瀏覽器會自動從 URL 計算正確的區域 (網際網路或內部網路)。 例如，http://contoso/ 會對應到內部網路區域，而 http://intranet.contoso.com/ 會對應到網際網路區域 (因為 URL 包含句點)。

因為用於 Azure AD 順暢 SSO 的服務 URL 包含句點，您必須將它們明確地新增到每個使用者瀏覽器的內部網路區域設定中。 這會讓瀏覽器自動將目前登入之使用者的 Kerberos 票證傳送到 Azure AD。 雖然您可以在每部電腦上手動進行此操作，但將所需 URL 新增到所有使用者之內部網路區域的最簡單方式，是在 Active Directory 中建立群組原則。

1. 開啟群組原則管理工具。
2. 編輯要套用到所有使用者的群組原則 (例如**預設網域原則**)。
3. 瀏覽至 **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security 頁面**，並選取 [指派網站到區域清單]。
![單一登入](./media/active-directory-aadconnect-sso/sso6.png)  
4. 啟用原則，並在對話方塊中輸入下列值/資料。 這些是 Kerberos 票證的傳送目標 Azure AD URL。

        Value: https://autologon.microsoftazuread-sso.com  
        Data: 1  
        Value: https://aadg.windows.net.nsatc.net  
        Data: 1  
5. 按一下 [確定]，然後再按一下 [確定]。

它看起來應該如下所示：![單一登入](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>依照預設，Chrome 會使用和 Internet Explorer 相同的一組信任的網站 URL。 如果您已經為 Chrome 設定不同的設定，您將需要個別更新這些設定。

## <a name="troubleshooting-seamless-sso"></a>針對順暢 SSO 進行疑難排解

使用下列檢查清單來針對順暢 SSO 進行疑難排解︰

1. 在 Azure AD Connect 工具上檢查租用戶是否已啟用順暢 SSO 功能。 如果您無法啟用此功能 (例如，因為連接埠已封鎖)，請確定您已完成所有[必要條件](#pre-requisites)。 如果您仍無法啟用此功能，請連絡 Microsoft 支援服務。
2. 兩個服務 URL (https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net) 已定義到內部網路區域設定中。
3. 確定公司桌面已加入 AD 網域。
4. 確定使用者是使用 AD 網域帳戶來登入桌面。
5. 確定桌面已連線到公司網路。
6. 確定桌面的時間已經與 Active Directory 和網域控制站的時間同步，且彼此的時間差不到 5 分鐘。
7. 從其桌面清除現有的 Kerberos 票證。 這可透過從命令提示字元執行 **klist purge** 命令來完成。
8. 檢閱瀏覽器的主控台記錄 (在 [開發人員工具] 底下)，以協助確定可能的問題。

### <a name="domain-controller-logs"></a>網域控制站記錄

如果網域控制站已啟用成功稽核，則每次使用者使用順暢 SSO 登入時，系統都會在事件記錄中記錄一個安全性項目 (與電腦帳戶 **AzureADSSOAcc$** 相關聯的事件 4769)。 您可以使用下列查詢來尋找這些安全性事件︰

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

