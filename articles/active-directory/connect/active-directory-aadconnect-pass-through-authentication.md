---
title: "Azure AD Connect：傳遞驗證 | Microsoft Docs"
description: "這篇文章說明 Azure Active Directory (Azure AD) 傳遞驗證，以及它如何向內部部署 Active Directory 驗證使用者的密碼以允許 Azure AD 登入。"
services: active-directory
keywords: "什麼是 Azure AD Connect 傳遞驗證、安裝 Active Directory，以及 Azure AD、SSO、單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>使用 Azure Active Directory 傳遞驗證設定使用者登入

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>什麼是 Azure Active Directory 傳遞驗證？

允許使用者使用相同的認證 (密碼) 登入內部部署資源和雲端服務，讓使用者與組織受益。 使用者需要記得的密碼愈少，就能獲得更佳的使用者體驗。 這也會減少使用者忘記如何登入的機會。 支援人員成本進而降低，因為密碼相關問題通常會耗用最多支援資源。

許多組織使用 [Azure AD 密碼同步處理 (英文)](active-directory-aadconnectsync-implement-password-synchronization.md)，這種 [Azure AD Connect (英文)](active-directory-aadconnect.md) 功能可將使用者的密碼雜湊從內部部署 Active Directory 同步處理至 Azure Active Directory (Azure AD)，以這種方式跨內部部署資源和雲端服務提供使用者相同的認證。 然而，也有許多組織不希望讓密碼 (即使是雜湊形式) 傳輸到內部組織的界限之外。

Azure AD 傳遞驗證功能為這些組織提供簡易的解決方案。 當使用者登入 Azure AD 時，此功能可確保會向內部部署 Active Directory 直接驗證使用者的密碼。 此功能也提供下列優點：

- 容易使用
  - 執行密碼驗證，而不需要複雜的內部部署或網路組態。
  - 此輕量型內部部署連接器會接聽並回應密碼驗證要求。
  - 內部部署連接器會自動接收功能改良與錯誤修正。
  - 它可以和 [Azure AD Connect](active-directory-aadconnect.md) 一起設定。 輕量型內部部署連接器和 Azure AD Connect 都安裝在相同的伺服器上。
- 安全
  - 內部部署密碼絕對不會以任何形式儲存在雲端。
  - 輕量型內部部署連接器只會從您的網路內進行輸出連線。 因此，不需要將連接器安裝在周邊網路 (又稱做 DMZ) 中。
  - 傳遞驗證可與 Azure Multi-Factor Authentication 完美配合運作。
- 可靠且可調整
  - 可以在多部伺服器安裝其他輕量型內部部署連接器，以達到登入要求的高可用性。

![Azure AD 傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

您可以將傳遞驗證與[順暢單一登入 (英文)](active-directory-aadconnect-sso.md) (SSO) 功能結合在一起。 如此一來，當使用者在公司網路內的公司電腦上，甚至不需要輸入密碼就能登入 Azure AD，可說是真正的整合體驗。

## <a name="whats-available-during-preview"></a>預覽期間有什麼可用功能？

>[!NOTE]
>Azure AD 傳遞驗證目前為預覽狀態。 這是免費功能，您不需要任何付費的 Azure AD 版本即可使用。

預覽期間完全支援的案例如下︰

- 所有 Web 瀏覽器型應用程式
- 支援[新式驗證 (英文)](https://aka.ms/modernauthga) 的 Office 365 用戶端應用程式

預覽期間「不」支援的案例如下︰

- 舊版 Office 用戶端應用程式和 Exchange ActiveSync (亦即，行動裝置上的原生電子郵件應用程式)。 <br>組織應該盡可能切換到新式驗證。 這樣可允許支援傳遞驗證，但也可使用 Multi-Factor Authentication 等[條件式存取 (英文)](../active-directory-conditional-access.md) 功能，協助您保護身分識別。
- 適用於 Windows 10 裝置的 Azure AD Join。

>[!IMPORTANT]
>為了因應傳遞驗證功能目前不支援的案例 (舊版 Office 用戶端應用程式、Exchange ActiveSync 和適用於 Windows 10 裝置的 Azure AD Join)，當您啟用傳遞驗證時，預設也會啟用密碼同步處理。 只有在這些特定情況下可將密碼同步處理做為後援。 若無此需要，您可以在 Azure AD Connect 精靈中的[選用功能 (英文) ](active-directory-aadconnect-get-started-custom.md#optional-features)頁面上關閉密碼同步處理。

### <a name="prerequisites"></a>必要條件

在啟用 Azure AD 傳遞驗證之前，您需要已備妥下列項目：

- 一個您必須是全域管理員的 Azure AD 租用戶。

  >[!NOTE]
  >強烈建議全域管理員帳戶為僅限雲端帳戶。 如此一來，如果您的內部部署服務失敗或無法使用，您便可以管理租用戶組態。 了解如何[新增僅限雲端管理員帳戶 (英文)](../active-directory-users-create-azure-portal.md)。

- Azure AD Connect 1.1.486.0 或更新版本。 建議您使用[最新版的 Azure AD Connect (英文)](https://www.microsoft.com/download/details.aspx?id=47594)。
- 執行 Windows Server 2012 R2 或更新版本，以在其上執行 Azure AD Connect 的伺服器。
  - 此伺服器和必須驗證其密碼的使用者必須都是相同 AD 樹系的成員。
  - 請注意，傳遞驗證連接器和 Azure AD Connect 安裝在相同的伺服器上。 確認連接器版本是 1.5.58.0 或更新版本。

    >[!NOTE]
    >如果 AD 樹系之間有樹系信任且名稱尾碼路由已正確設定，就支援多樹系環境。

- 如果您想要高可用性，您必須執行 Windows Server 2012 R2 或更新版本的其他伺服器來安裝獨立連接器。 (連接器版本必須是 1.5.58.0 或更新版本。)
- 如果任何連接器和 Azure AD 之間有防火牆：
    - 如果已經啟用 URL 篩選，請確定連接器可以和下列 URL 通訊：
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - 確定連接器也會對 [Azure 資料中心 IP 範圍 (英文)](https://www.microsoft.com/en-us/download/details.aspx?id=41653) 直接建立 IP 連線。
    - 請確定防火牆不會執行 SSL 檢查，因為連接器會使用用戶端憑證來與 Azure AD 通訊。
    - 請確定連接器可以透過連接埠 80 和 443 對 Azure AD 發出輸出要求。
      - 如果您的防火牆根據原始使用者強制執行規則，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。
      - 連接器會透過連接埠 80 發出 HTTP 要求，以下載 SSL 憑證撤銷清單。 為了讓自動更新功能正確運作，也需要如此。
      - 連接器會透過連接埠 443 發出 HTTPS 要求，以支援其他所有作業，例如啟用和停用功能、註冊連接器、下載連接器更新和處理所有使用者登入要求。

     >[!NOTE]
     >我們最近已經改善，降低連接器與我們的服務通訊時所需的連接埠數目。 如果您正在執行舊版 Azure AD Connect 和/或獨立連接器，您應該繼續將這些其他的連接埠 (5671、8080、9090、9091、9350、9352、10100-10120) 保持開啟。

### <a name="enable-azure-ad-pass-through-authentication"></a>啟用 Azure AD 傳遞驗證

Azure AD 傳遞驗證能透過 Azure AD Connect 啟用。

如果您要執行新安裝的 Azure AD Connect，請選擇[自訂的安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，選取 [傳遞驗證]。 成功完成時，這會在和 Azure AD Connect 相同的伺服器上安裝傳遞驗證。 此外，它還會對您的租用戶啟用傳遞驗證功能。

![Azure AD Connect - 使用者登入](./media/active-directory-aadconnect-sso/sso3.png)

如果您已安裝 Azure AD Connect，可使用[快速安裝](active-directory-aadconnect-get-started-express.md)或[自訂安裝](active-directory-aadconnect-get-started-custom.md)路徑，或選取 Azure AD Connect 上的 [變更使用者登入頁面]，然後按一下 [下一步]。 然後選取 [傳遞驗證]，在和 Azure AD Connect 相同的伺服器上安裝傳遞驗證連接器，然後對您的租用戶啟用該功能。

![Azure AD Connect - 變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD 傳遞驗證為租用戶層級功能。 它會影響租用戶中所有受管理網域的使用者登入。 不過，同盟網域的使用者仍可繼續使用 Active Directory Federation Services (ADFS) 或您先前設定的任何其他同盟提供者來登入。 如果您將同盟網域轉換成受管理網域，該網域中的所有使用者都可透過使用傳遞驗證自動開始登入。 純雲端的使用者不會受傳遞驗證影響。

如果您打算在生產部署中使用傳遞驗證，強烈建議您在不同的伺服器 (非執行 Azure AD Connect 和第一個連接器的伺服器) 上安裝第二個連接器。 如此一來，可確保登入要求的高可用性。 您可以視需要安裝其他的連接器，數量不拘。 這個數目是根據租用戶處理的尖峰和平均登入要求數而定。

請依照下列指示來部署獨立連接器。

#### <a name="step-1-download-and-install-the-connector"></a>步驟 1：下載及安裝連接器

在此步驟中，您可以在伺服器上下載並安裝連接器軟體。

1.    [下載 (英文)](https://go.microsoft.com/fwlink/?linkid=837580) 最新的連接器。 確認連接器版本是 1.5.58.0 或更新版本。
2.    以系統管理員身分開啟命令提示字元。
3.    執行下列命令。 **/q** 選項表示「無訊息安裝」，亦即安裝不會提示您接受《使用者授權合約》：`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>每部伺服器都只能安裝單一連接器。

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>步驟 2：在 Azure AD 註冊連接器以用於傳遞驗證

在此步驟中，您可以向我們的服務註冊伺服器上安裝的連接器，並讓它可以接收登入要求。

1.    以系統管理員身分開啟 PowerShell 視窗。
2.    瀏覽至 **C:\Program Files\Microsoft AAD App Proxy Connector**，然後執行指令碼，如下所示：`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    出現提示時，輸入您 Azure AD 租用戶上全域系統管理員帳戶的認證。

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Azure AD 傳遞驗證的運作方式為何？

當使用者嘗試登入 Azure AD (且若租用戶上已啟用傳遞驗證)，會發生下列情況：

1. 使用者將其使用者名稱和密碼輸入 Azure AD 登入頁面。 我們的服務會將用於驗證的使用者名稱和密碼 (使用公開金鑰加密) 置於佇列。
2. 其中一個可用的內部部署連接器會對佇列進行輸出呼叫，並擷取使用者名稱和密碼。
3. 該連接器接著會使用標準 Windows API (和 ADFS 所使用類似的機制)，向 Active Directory 使用驗證使用者名稱和密碼。 請注意，使用者名稱可以是內部部署的預設使用者名稱 (通常是 `userPrincipalName`)，或可在 Azure AD Connect 中設定的另一個屬性 (又稱為 `Alternate ID`)。
4. 然後內部部署網域控制站會評估要求並將回應 (成功或失敗) 傳回給連接器。
5. 連接器再將此回應傳回給 Azure AD。
6. Azure AD 接著評估回應，並視情況回應使用者。 例如，它會發行權杖傳回給應用程式，或要求 Multi-Factor Authentication。

下圖說明各種步驟。 所有要求和回應都是透過 HTTPS 通道進行。

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>密碼回寫

您已在租用戶上並為特定使用者設定[密碼回寫](../active-directory-passwords-update-your-own-password.md)時，若使用者使用傳遞驗證登入，他們將和之前一樣能夠變更或重設其密碼。 密碼將會如預期般回寫至內部部署 Active Directory。

然而，如果在您的租用戶上未設定密碼回寫，或使用者未獲指派有效的 Azure AD 授權，則將不允許使用者更新其在雲端中的密碼。 即使他們的密碼已過期，也是如此。 使用者會看到這則訊息：「您的組織不允許您在此網站更新您的密碼。 請依據您組織所建議的方法更新密碼，或洽詢您的管理員尋求協助。」

## <a name="next-steps"></a>後續步驟
- 請參閱如何啟用您租用戶上的 [Azure AD 順暢 SSO (英文)](active-directory-aadconnect-sso.md) 功能。
- 參閱我們的[疑難排解指南 (英文)](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)，了解如何解決 Azure AD 傳遞驗證的常見問題。

## <a name="feedback"></a>意見反應
您的意見反應對我們非常寶貴。 如果您有疑問，請使用註解區段。 使用我們的 [UserVoice 論壇 (英文)](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) 了解新功能要求。


