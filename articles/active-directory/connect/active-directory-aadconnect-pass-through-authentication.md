---
title: "Azure AD Connect：傳遞驗證 | Microsoft Docs"
description: "這篇文章說明 Azure Active Directory (Azure AD) 傳遞驗證，以及它如何向您的內部部署 Active Directory 驗證使用者的密碼以允許 Azure AD 登入。"
services: active-directory
keywords: "何謂 Azure AD Connect 傳遞驗證, 安裝 Active Directory, Azure AD, SSO, 單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: d3c3f6ba0da73a8297f437a56f190f90274957ab
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>使用 Azure Active Directory 傳遞驗證設定使用者登入

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>什麼是 Azure Active Directory (Azure AD) 傳遞驗證？

讓使用者使用相同的認證 (密碼) 登入內部部署資源和雲端服務，對您的使用者和組織有益。 使用者會少一個要記住的密碼。 這提供較佳使用者體驗，並減少發生使用者忘記如何登入的機會。 進而降低支援人員成本，因為密碼相關問題通常會耗用最多支援資源。

許多組織使用 [Azure AD 密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)，這種 [Azure AD Connect](active-directory-aadconnect.md) 功能可將使用者的密碼從內部部署 Active Directory 同步處理至 Azure AD，以這種方式跨內部部署資源和雲端服務提供使用者相同的認證。 然而，也有許多組織不希望讓密碼 (即使是雜湊形式) 傳輸到內部組織的界限之外。

Azure AD 傳遞驗證能為這些組織提供簡易的解決方案。 當使用者登入 Azure AD 時，可確保會向您的內部部署 Active Directory 直接驗證使用者的密碼。 此功能也提供下列優點：

- 容易使用
  - 執行密碼驗證，而不需要複雜的內部部署或網路組態。
  - 它只會利用輕量型內部部署連接器接聽並回應密碼驗證要求。
  - 內部部署連接器具有自動更新功能，能夠自動接收功能改良和錯誤修正。
  - 它可以和 [Azure AD Connect](active-directory-aadconnect.md) 一起設定。 輕量型內部部署連接器和 Azure AD Connect 都安裝在相同的伺服器上。
- 安全
  - 內部部署密碼絕對不會以任何形式儲存在雲端。
  - 輕量型內部部署連接器只會從您的網路內進行輸出連線。 因此，不需要在 DMZ 中安裝連接器。
  - 傳遞驗證可與 Azure Multi-Factor Authentication 完美配合運作。
- 可靠且可調整
  - 可以在多部伺服器安裝其他輕量型內部部署連接器，以達到登入要求的高可用性。

![Azure AD 傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

和[順暢的單一登入](active-directory-aadconnect-sso.md)功能結合時，當使用者在公司網路內的公司電腦上，甚至不需要輸入密碼就能登入 Azure AD，可說是真正的整合體驗。

## <a name="whats-available-during-preview"></a>預覽期間有什麼可用功能？

>[!NOTE]
>Azure AD 傳遞驗證目前為預覽狀態。 這是免費功能，您不需要任何付費的 Azure AD 版本即可使用。 

預覽期間完全支援的案例如下︰

- 所有 Web 瀏覽器型應用程式。
- 支援[新式驗證 (英文)](https://aka.ms/modernauthga) 的 Office 365 用戶端應用程式。

預覽期間不支援的案例如下︰

- 舊版 Office 用戶端應用程式和 Exchange ActiveSync (亦即，行動裝置上的原生電子郵件應用程式)。
  - 組織應該盡可能切換到新式驗證。 這樣可允許支援傳遞驗證，但也可使用 Multi-Factor Authentication 等[條件式存取](../active-directory-conditional-access.md)功能，協助您保護身分識別。
- 適用於 Windows 10 裝置的 Azure AD Join。

>[!IMPORTANT]
>為了因應傳遞驗證功能目前不支援的案例 (舊版 Office 用戶端應用程式、Exchange ActiveSync 和適用於 Window 10 裝置的 Azure AD Join)，當您啟用傳遞驗證時，預設也會啟用密碼同步處理。 只有在這些特定情況下可將密碼同步處理做為後援。 若無此需要，您可以在 Azure AD Connect 精靈中的[選用功能](active-directory-aadconnect-get-started-custom.md#optional-features)頁面上關閉密碼同步處理。

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>如何啟用 Azure AD 傳遞驗證？

### <a name="pre-requisites"></a>必要條件

在啟用 Azure AD 傳遞驗證之前，您需要已備妥下列必要條件：

- 一個您必須是全域管理員的 Azure AD 租用戶。

>[!NOTE]
>強烈建議全域系統管理員帳戶使用僅限雲端使用的帳戶，這樣一來您就能夠在您的內部部署服務失敗或無法使用時，管理您租用戶的組態。 您可以新增僅限雲端使用的全域系統管理員帳戶，如[這裡](../active-directory-users-create-azure-portal.md)所示。

- Azure AD Connect 1.1.486.0 版或更新版本。 建議您使用[最新版的 Azure AD Connect (英文)](https://www.microsoft.com/download/details.aspx?id=47594)。
- 執行 Windows Server 2012 R2 或更新版本，以在其上執行 Azure AD Connect 的伺服器。
  - 此伺服器和必須驗證其密碼的使用者必須都是相同 AD 樹系的成員。
  - 請注意，傳遞驗證連接器和 Azure AD Connect 安裝在相同的伺服器上。 確認連接器版本是 1.5.58.0 或更新版本。

>[!NOTE]
>如果 AD 樹系之間有樹系信任且名稱尾碼路由已正確設定，就支援多樹系環境。

- 如果您想要高可用性，您需要有執行 Windows Server 2012 R2 或更新版本的其他伺服器來安裝獨立連接器 (版本必須是 1.5.58.0 或更新版本)。
- 如果任何連接器和 Azure AD 之間有防火牆，請確定：
    - 如果已經啟用 URL 篩選，請確定連接器可以和下列 URL 通訊：
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - 連接器也會對 [Azure 資料中心 IP 範圍 (英文)](https://www.microsoft.com/en-us/download/details.aspx?id=41653) 直接建立 IP 連線。
    - 請確定防火牆不會執行 SSL 檢查，因為連接器會使用用戶端憑證來與 Azure AD 通訊。
    - 請確定連接器可以透過連接埠 80 和 443 對 Azure AD 發出輸出要求。
      - 如果您的防火牆根據原始使用者強制執行規則，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。
      - 連接器會透過連接埠 80 發出 HTTP 要求，以下載 SSL 憑證撤銷清單。 為了讓自動更新功能正確運作，也需要如此。
      - 連接器會透過連接埠 443 發出 HTTPS 要求，以支援其他所有作業，例如啟用和停用功能、註冊連接器、下載連接器更新和處理所有使用者登入要求。

>[!NOTE]
>我們最近已經改善，降低連接器與我們的服務通訊時所需的連接埠數目。 如果您正在執行舊版 Azure AD Connect 和/或獨立連接器，您應該繼續將這些其他的連接埠 (5671、8080、9090、9091、9350、9352、10100-10120) 保持開啟。

### <a name="enabling-azure-ad-pass-through-authentication"></a>啟用 Azure AD 傳遞驗證

Azure AD 傳遞驗證能透過 Azure AD Connect 啟用。

如果您要執行新安裝的 Azure AD Connect，請選擇[自訂的安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，選取 [傳遞驗證]。 成功完成時，這會在和 Azure AD Connect 相同的伺服器上安裝傳遞驗證。 此外，它還會對您的租用戶啟用傳遞驗證功能。

![Azure AD Connect - 使用者登入](./media/active-directory-aadconnect-sso/sso3.png)

如果您已經安裝 Azure AD Connect，可使用[快速安裝](active-directory-aadconnect-get-started-express.md)或[自訂安裝](active-directory-aadconnect-get-started-custom.md)路徑來設定，在 Azure AD Connect 上選擇 [變更使用者登入] 頁面，然後按 [下一步]。 然後選取 [傳遞驗證]，在和 Azure AD Connect 相同的伺服器上安裝傳遞驗證連接器，然後對您的租用戶啟用該功能。

![Azure AD Connect - 變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD 傳遞驗證為租用戶層級功能。 它會影響租用戶中所有受管理網域的使用者登入。 不過，同盟網域的使用者仍可繼續使用 Active Directory Federation Services (ADFS) 或您先前設定的任何其他同盟提供者來登入。 如果您將同盟網域轉換成受管理網域，該網域中的所有使用者都會自動開始使用傳遞驗證登入。 純雲端的使用者不會受傳遞驗證影響。

### <a name="ensuring-high-availability"></a>確保高可用性

如果您打算在生產部署中使用傳遞驗證，強烈建議您在 (和執行 Azure AD Connect 與第一個連接器) 不同的伺服器上安裝第二個連接器，以確保您擁有登入要求的高可用性。 您可以視需要安裝其他連接器。這個數目是根據租用戶處理的尖峰和平均登入要求數而定。

請依照下列指示來部署獨立連接器：

#### <a name="step-1-download-and-install-the-connector"></a>步驟 1：下載及安裝連接器

在此步驟中，您可以在伺服器上下載並安裝連接器軟體。

1.    [下載 (英文)](https://go.microsoft.com/fwlink/?linkid=837580) 最新的連接器。 確認連接器版本是 1.5.58.0 或更新版本。
2.    以系統管理員身分開啟命令提示字元。
3.    執行下列命令 (/q 表示無訊息安裝，即安裝不會提示您接受《使用者授權合約》)：

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>每部伺服器都只能安裝單一連接器。

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>步驟 2：在 Azure AD 註冊連接器以用於傳遞驗證

在此步驟中，您可以向我們的服務註冊伺服器上安裝的連接器，並讓它可以接收登入要求。

1.    以系統管理員身分開啟 PowerShell 視窗。
2.    瀏覽至 **C:\Program Files\Microsoft AAD App Proxy Connector**，然後執行指令碼，如下所示：`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    出現提示時，輸入您 Azure AD 租用戶上全域系統管理員帳戶的認證。

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Azure AD 傳遞驗證的運作方式

當使用者嘗試登入 Azure AD (且若租用戶上已啟用傳遞驗證)，會發生下列情況：

1. 使用者將其使用者名稱和密碼輸入 Azure AD 登入頁面。 我們的服務會將用於驗證的使用者名稱和密碼 (使用公開金鑰加密) 置於佇列。
2. 其中一個可用的內部部署連接器會對佇列進行輸出呼叫，並擷取使用者名稱和密碼。
3. 該連接器接著會使用標準 Windows API (和 ADFS 所使用類似的機制)，向您的 Active Directory 使用驗證使用者名稱和密碼。 請注意，使用者名稱可以是內部部署的預設使用者名稱 (通常是 "userPrincipalName")，或可在 Azure AD Connect 中設定的另一個屬性 (又稱為「替代識別碼 」)。
4. 然後內部部署網域控制站會評估要求並將回應 (成功或失敗) 傳回給連接器。
5. 連接器再將此回應傳回給 Azure AD。
6. Azure AD 接著評估回應，並視情況回應使用者。 例如，它會發行權杖傳回給應用程式，或要求多重要素驗證。

下圖也說明各種步驟。 請注意，所有要求和回應都是透過 HTTPS 通道進行。

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>密碼回寫的注意事項

您已在租用戶上並為特定使用者設定[密碼回寫](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)時，若使用者使用傳遞驗證登入，他們將和之前一樣能夠變更或重設其密碼。 密碼將會如預期般回寫至內部部署 Active Directory。

然而，如果這些條件的其中之一不為 true (未在租用戶上設定密碼回寫，或未將有效的 Azure AD 授權指派給使用者)，則不會允許使用者更新雲端中的密碼，包括他們的過期密碼。 使用者將會看到一則訊息，如下所示：「您的組織不允許您在此網站更新您的密碼。 請依據您組織所建議的方法更新密碼，或洽詢您的管理員尋求協助」。

## <a name="troubleshooting-pass-through-authentication"></a>針對傳遞驗證進行疑難排解

本節可幫助您尋找安裝期間、註冊或解除安裝傳遞驗證連接器 (透過 Azure AD Connect 或獨立連接器) 的一些常見問題的疑難排解資訊。 還有在租用戶上啟用和操作功能期間。

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>安裝連接器 (透過 Azure AD Connect 或獨立連接器) 期間的問題

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD 應用程式 Proxy 連接器已經存在

傳遞驗證連接器無法和 [Azure AD 應用程式 Proxy](../../active-directory/active-directory-application-proxy-get-started.md) 連接器安裝在同一部伺服器上。 您必須在不同的伺服器上安裝傳遞驗證連接器。

#### <a name="an-unexpected-error-occured"></a>發生意外的錯誤

從伺服器[收集連接器記錄檔](#collecting-pass-through-authentication-connector-logs)，並連絡 Microsoft 支援服務解決您的問題。

### <a name="issues-during-registration-of-connectors"></a>註冊連接器期間發生的問題

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>由於連接埠遭到封鎖，而使註冊連接器連線失敗

確認已安裝連接器的伺服器能與我們的服務 URL 和連接埠通訊，如[這裡](#pre-requisites)所列。

#### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>因為權杖或帳戶授權錯誤，註冊連接器失敗

請確定您在所有 Azure AD Connect 或獨立連接器安裝和註冊作業中，使用僅限雲端的全域管理員帳戶。 啟用 MFA 的全域管理員帳戶有一個已知的問題，請暫時關閉 MFA (只是為了完成作業) 作為因應措施。

#### <a name="an-unexpected-error-occurred"></a>發生意外的錯誤

從伺服器[收集連接器記錄檔](#collecting-pass-through-authentication-connector-logs)，並連絡 Microsoft 支援服務解決您的問題。

### <a name="issues-during-un-installation-of-connectors"></a>解除安裝連接器期間發生的問題

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>解除安裝 Azure AD Connect 時出現的警告訊息

如果您已在租用戶上啟用傳遞驗證，但您嘗試解除安裝 Azure AD Connect，其將會顯示下列警告訊息：「除非您在其他伺服器上有安裝其他傳遞驗證代理程式，否則使用者將無法登入 Azure AD」。

在您解除安裝 Azure AD Connect 之前，您必須完成[高可用性](#ensuring-high-availability)設定以避免中斷使用者登入。

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>啟用傳遞驗證功能時發生的問題

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>因為沒有任何可用的連接器，而使啟用功能失敗

您必須至少有一個使用中的連接器，才能在租用戶上啟用傳遞驗證。 您可以安裝 Azure AD Connect 或獨立連接器來安裝連接器。

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>因為連接埠遭到封鎖，而使功能啟用失敗

確認已安裝 Azure AD Connect 的伺服器能與我們的服務 URL 和連接埠通訊，如[這裡](#pre-requisites)所列。

#### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>因為權杖或帳戶授權錯誤，啟用功能失敗

請確定您使用僅限雲端的全域管理員帳戶來啟用此功能。 啟用 Multi-Factor Authentication (MFA) 的全域管理員帳戶有一個已知的問題，請暫時關閉 MFA (只是為了完成作業) 作為因應措施。

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>操作傳遞驗證功能時發生的問題

#### <a name="user-facing-sign-in-errors"></a>使用者對應登入錯誤

功能在 Azure AD 登入畫面上報告下列使用者對應錯誤。 其連同適當的解決步驟詳述如下。

|錯誤|說明|解決方案
| --- | --- | ---
|AADSTS80001|無法連線至 Active Directory|確定連接器伺服器和必須驗證其密碼的使用者都是相同 AD 樹系的成員，而且都能連線到 Active Directory。  
|AADSTS8002|連線至 Active Directory 時發生逾時|請檢查以確定 Active Directory 可用，並且會回應來自連接器的要求。
|AADSTS80004|傳遞給連接器的使用者名稱無效|請確定使用者嘗試用來登入的使用者名稱正確無誤。
|AADSTS80005|驗證發生無法預期的 WebException|這可能是暫時性的錯誤。 重試要求。 如果持續發生失敗，請連絡 Microsoft 支援服務。
|AADSTS80007|和 Active Directory 通訊時發生錯誤|請檢查連接器記錄檔以了解詳細資訊，並確認 Active Directory 如預期般運作。

### <a name="collecting-pass-through-authentication-connector-logs"></a>收集傳遞驗證連接器記錄

根據發生的問題類型，您必須在不同位置中查看傳遞驗證連接器記錄檔。

#### <a name="connector-event-logs"></a>連接器事件記錄檔

如需與連接器有關的錯誤，可開啟伺服器上的 [事件檢視器] 應用程式，並查看 [應用程式及服務記錄檔\Microsoft\AadApplicationProxy\Connector\Admin] 底下。

如需詳細的分析和偵錯記錄檔，您可以啟用「工作階段」記錄檔。 在正常作業期間，請勿在此記錄檔啟用時執行連接器。這只適用於進行疑難排解。 請注意，只有再次停用此記錄檔之後，才能看見記錄檔的內容。

#### <a name="detailed-trace-logs"></a>詳細的追蹤記錄檔

若要對使用者登入失敗進行疑難排解，請查看追蹤記錄檔，位於：**C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**。 這些記錄檔包含使用傳遞驗證功能的特定使用者為什麼會登入失敗的原因。 以下是範例記錄檔項目︰

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

您可以開啟命令提示字元並執行下列命令，以取得錯誤 (上述範例為 '1328') 的描述性詳細資料。 注意︰您必須使用您在記錄檔中看到的實際錯誤號碼取代 '1328'。

`Net helpmsg 1328`

結果應該會看起來像這樣：

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>網域控制站記錄檔

如果已經啟用稽核記錄，您可以在網域控制站的安全性記錄檔中找到其他資訊。 查詢傳遞驗證連接器所傳送登入要求的簡單方式如下所示︰

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>意見反應

您的意見反應對我們非常寶貴。 您可以透過下列電子郵件地址連絡我們：[aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com)。 如果您有新功能的要求，請使用我們的 [UserVoice 論壇 (英文)](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 我們洗耳恭聽。

