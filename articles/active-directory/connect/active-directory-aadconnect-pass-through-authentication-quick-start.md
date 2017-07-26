---
title: "Azure AD 傳遞驗證 - 快速入門 | Microsoft Docs"
description: "本文說明如何開始使用 Azure Active Directory (Azure AD) 傳遞驗證。"
services: active-directory
keywords: "Azure AD Connect 傳遞驗證, 安裝 Active Directory, Azure AD 的必要元件, SSO, 單一登入"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 傳遞驗證：快速入門

Azure Active Directory (Azure AD) 傳遞驗證可讓您的使用者以相同密碼登入內部部署和雲端式應用程式。 它會直接向內部部署 Active Directory 驗證使用者的密碼，以決定是否讓使用者登入。

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>如何部署 Azure AD 傳遞驗證

若要部署傳遞驗證，您需要遵循下列步驟：
1. 檢查必要條件：在啟用此功能之前，正確設定您的租用戶和內部部署環境。
2. 啟用功能：在您的租用戶上啟用傳遞驗證，並安裝輕量型內部部署代理程式來處理密碼驗證要求。
3. 測試功能：使用傳遞驗證來測試使用者登入。
4. 確保高可用性：安裝第二個獨立代理程式，以提供高可用性來滿足登入要求。

## <a name="step-1-check-prerequisites"></a>步驟 1：檢查必要條件

請確保已具備下列必要條件︰

### <a name="on-the-azure-portal"></a>在 Azure 入口網站上

1. 在 Azure AD 租用戶上建立僅限雲端的「全域管理員」帳戶。 如此一來，如果您的內部部署服務失敗或無法使用，您便可以管理租用戶組態。 了解如何[新增僅限雲端管理員帳戶 (英文)](../active-directory-users-create-azure-portal.md)。 這是確保您不會被租用戶封鎖的關鍵步驟。
2. 將一或多個[自訂網域名稱](../active-directory-add-domain.md)新增至 Azure AD 租用戶。 您的使用者會使用其中一個網域名稱登入。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 識別一部執行 Windows Server 2012 R2 或更新版本的伺服器來執行 Azure AD Connect。 在需要驗證密碼的使用者所在的同一個 AD 樹系中，新增此伺服器。
2. 在步驟 2 所識別的伺服器上，安裝[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。 如果您已執行 Azure AD Connect，請確定版本是 1.1.486.0 或更新版本。
3. 識別額外一部執行 Windows Server 2012 R2 或更新版本的伺服器來執行獨立驗證代理程式。 驗證代理程式版本必須是 1.5.58.0 或更新版本。 這是確保有高可用性可滿足登入要求的伺服器。 在需要驗證密碼的使用者所在的同一個 AD 樹系中，新增此伺服器。
4. 如果您的伺服器和 Azure AD 之間有防火牆，則您需要設定下列項目：
   - 開啟連接埠：請確定伺服器上的驗證代理程式可以透過連接埠 80 和 443 向 Azure AD 提出輸出要求。 如果您的防火牆根據原始使用者強制執行規則，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。
   - 允許 Azure AD 端點：如果 URL 篩選已啟用，請確定驗證代理程式可以與 **\*.msappproxy.net** 和 **\*.servicebus.windows.net** 通訊。
   - 確認直接 IP 連線：請確定伺服器上的驗證代理程式可以對 [Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=41653)建立直接 IP 連線。

## <a name="step-2-enable-the-feature"></a>步驟 2︰啟用功能

您可以使用 [Azure AD Connect](active-directory-aadconnect.md) 來啟用傳遞驗證。

>[!IMPORTANT]
>您可以在 Azure AD Connect 主要或暫存伺服器上啟用傳遞驗證。 強烈建議您從主要伺服器啟用此功能。

如果您是第一次安裝 Azure AD Connect，請選擇[自訂安裝路徑](active-directory-aadconnect-get-started-custom.md)。 在 [使用者登入] 頁面上，選擇 [傳遞驗證] 作為登入方法。 成功完成時，傳遞驗證代理程式會安裝在 Azure AD Connect 所在的同一部伺服器上。 此外，您的租用戶上也會啟用傳遞驗證功能。

![Azure AD Connect - 使用者登入](./media/active-directory-aadconnect-sso/sso3.png)

如果您已安裝 Azure AD Connect (使用[快速安裝](active-directory-aadconnect-get-started-express.md)或[自訂安裝](active-directory-aadconnect-get-started-custom.md)路徑)，請在 Azure AD Connect 上選取 [變更使用者登入] 頁面，並按 [下一步]。 然後選取 [傳遞驗證] 作為登入方法。 成功完成時，傳遞驗證代理程式會安裝在 Azure AD Connect 所在的同一部伺服器上，且您的租用戶上會啟用此功能。

![Azure AD Connect - 變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>傳遞驗證是租用戶層級的功能。 開啟此功能會影響租用戶中「所有」受管理網域的使用者登入。

## <a name="step-3-test-the-feature"></a>步驟 3：測試功能

完成步驟 2 之後，租用戶中所有受管理網域的使用者會使用傳遞驗證來登入。 不過，同盟網域的使用者會繼續使用 Active Directory Federation Services (ADFS) 或您先前設定的其他同盟提供者來登入。 如果您將同盟網域轉換成受管理網域，該網域中的所有使用者都會自動開始使用傳遞驗證來登入。 傳遞驗證功能不會影響僅限雲端的使用者。

## <a name="step-4-ensure-high-availability"></a>步驟 4：確保高可用性

如果您打算在生產環境中部署傳遞驗證，您應該安裝獨立驗證代理程式。 在執行 Azure AD Connect 和第一個驗證代理程式「以外」的伺服器上，安裝這第二個驗證代理程式。 此設定可提供高可用性來滿足登入要求。 請依照下列指示來部署獨立驗證代理程式：

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>下載驗證代理程式軟體並安裝在您的伺服器上

1.  [下載](https://go.microsoft.com/fwlink/?linkid=837580)最新的驗證代理程式軟體。 請確認版本是 1.5.58.0 或更新版本。
2.  以系統管理員身分開啟命令提示字元。
3.  執行下列命令 (**/q** 選項表示「靜音安裝」- 安裝不會提示您接受使用者授權合約)：`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>每一部伺服器只能安裝單一驗證代理程式。

### <a name="register-the-authentication-agent-with-azure-ad"></a>向 Azure AD 註冊驗證代理程式

1.  以系統管理員身分開啟 PowerShell 視窗。
2.  瀏覽至 **C:\Program Files\Microsoft AAD App Proxy Connector**，然後執行指令碼，如下所示：`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  出現提示時，輸入您 Azure AD 租用戶上全域系統管理員帳戶的認證。

## <a name="next-steps"></a>後續步驟
- [**目前的限制**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 此功能目前為預覽狀態。 了解支援的情節和不支援的情節。
- [**技術性深入探討**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解這項功能的運作方式。
- [**常見問題集**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常見問題集的答案。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解決此功能的常見問題。
- [**Azure AD 無縫 SSO**](active-directory-aadconnect-sso.md) - 深入了解此互補功能。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。

