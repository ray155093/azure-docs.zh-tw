---
title: "使用現有的 NPS 伺服器提供 Azure MFA 功能 | Microsoft Docs"
description: "Azure Multi-Factor Authentication 的網路原則伺服器擴充功能是可將雲端式雙步驟驗證功能新增至現有驗證基礎結構的簡單解決方案。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c9cf1540c0f8d16b0a5cdbedc78ac58cd5686107
ms.openlocfilehash: 51ebadc241892ebc6fb26b29955e3030ae1bdcb3


---
# <a name="augment-your-existing-authentication-infrastructure-with-the-nps-extension-for-azure-multi-factor-authentication---public-preview"></a>利用 Azure Multi-Factor Authentication 的 NPS 擴充功能強化現有的驗證基礎結構 - 公開預覽

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 利用 NPS 擴充功能，您可以在現有驗證流程中新增通話、簡訊或電話應用程式驗證，而不必安裝、設定及維護新的伺服器。 
 
使用 Azure MFA 的 NPS 擴充功能時，驗證流程會包含下列元件︰ 

1. **NAS/VPN 伺服器**會從 VPN 用戶端接收要求，並將其轉換為對 NPS 伺服器的 RADIUS 要求。 
2. **NPS 伺服器**會連線至 Active Directory，以對 RADIUS 要求執行主要驗證，並於成功時將要求傳遞至任何已安裝的擴充功能。  
3. **NPS 擴充功能**會觸發 Azure MFA 要求以進行第二項驗證。 當擴充功能收到回應後，如果 MFA 挑戰成功，擴充功能便會藉由為 NPS 伺服器提供包含 Azure STS 所發行之 MFA 宣告的安全性權杖來完成驗證要求。  
4. **Azure MFA** 會與 Azure Active Directory 通訊以擷取使用者的詳細資料，並使用為使用者設定的驗證方法執行第二項驗證。

下圖說明此高階驗證要求流程︰ 

![驗證流程圖](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>必要條件

NPS 擴充功能是為了搭配現有基礎結構來運作。 請確定您已備妥這些必要條件，然後再開始。

### <a name="licenses"></a>授權

Azure MFA 的 NPS 擴充功能可透過 [Azure Multi-Factor Authentication 授權](multi-factor-authentication.md) (隨附於 Azure AD Premium、EMS 或 MFA 訂用帳戶) 來提供給客戶使用。

### <a name="software"></a>軟體

已啟用 NPS 元件的 Windows Server 2008 R2 SP1 或更新版本。

### <a name="libraries"></a>程式庫

NPS 擴充功能需要兩個程式庫。 設定程序進行期間會安裝這些程式庫︰

-   Microsoft Visual Studio 2013 C++ 可轉散發套件 (X64)
-   適用於 Windows PowerShell 1.1.166 版本的 Microsoft Azure Active Directory 模組

### <a name="azure-active-directory"></a>Azure Active Directory

使用 NPS 擴充功能的每位使用者都必須使用 Azure AD Connect 同步到 Azure Active Directory ，且必須啟用 MFA。

當您安裝擴充功能時，您的 Azure AD 租用戶需要目錄識別碼和系統管理員認證。 您可以在 [Azure 入口網站](https://portal.azure.com)中找到您的目錄識別碼。 請以系統管理員身分登入，選取左側的 [Azure Active Directory] 圖示，然後選取 [屬性]。 複製 [目錄識別碼] 方塊中的 GUID，然後儲存。

![在 Azure Active Directory 屬性下尋找您的目錄識別碼](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>安裝 NPS 擴充功能

若要安裝 Azure MFA 的 NPS 擴充功能：

1.  從 Microsoft 下載中心[下載 NPS 擴充功能](https://aka.ms/npsmfa)
2.  將二進位檔複製到您要設定的網路原則伺服器
3.  執行 setup.exe 並遵循安裝指示

安裝完成後，安裝程式會在下列位置建立 PowerShell 指令碼︰`C:\Program Files\Microsoft\AzureMfa\Config` (其中 C:\ 是您的安裝磁碟機)。 此 PowerShell 指令碼會執行下列動作：

-   建立自我簽署憑證。
-   讓憑證的公開金鑰與 Azure AD 的服務主體產生關聯。
-   將憑證儲存在本機電腦的憑證存放區。
-   將憑證的私密金鑰存取權授與給網路使用者。
-   重新啟動 NPS。

除非您想要使用自己的憑證 (而非 PowerShell 指令碼產生的自我簽署憑證)，否則請執行 PowerShell 指令碼來完成安裝。

## <a name="configure-your-nps-extension"></a>設定 NPS 擴充功能

本節包含成功部署 NPS 擴充功能的設計考量和建議。

### <a name="configurations-limitations"></a>組態限制

- NPS 擴充功能是為了搭配現有部署來運作，並不適用於新部署。 因此，Azure MFA 的 NPS 擴充功能並未包含從 MFA Server 將使用者和設定移轉至雲端的工具。

- NPS 擴充功能會使用來自內部部署 Active Directory 的 UPN，識別 Azure MFA 上用來執行次要驗證的使用者。 擴充功能無法設定為使用不同的識別碼，例如 UPN 以外的替代登入識別碼或自訂 AD 欄位。  

### <a name="control-radius-clients-that-require-mfa"></a>控制需要 MFA 的 RADIUS 用戶端

為使用 NPS 擴充功能的 RADIUS 用戶端啟用 MFA 後，便需要此用戶端的所有驗證才能執行 MFA。 如果您想要為一些 RADIUS 用戶端啟用 MFA，但其他的用戶端則不啟用，您可以設定兩部 NPS 伺服器，並只在其中一部上安裝擴充功能。 將您想要讓其必須使用 MFA 來傳送要求的 RADIUS 用戶端設定到設定了擴充功能的 NPS 伺服器，並將其他 RADIUS 用戶端設定到未設定擴充功能的 NPS 伺服器。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>針對未註冊 MFA 的使用者做準備

如果您有未註冊 MFA 的使用者，您可以決定在其嘗試驗證時會有什麼結果。 使用登錄路徑 HKLM\Software\Microsoft\AzureMFA 中的登錄設定 *REQUIRE_USER_MATCH* 來控制功能的行為。 此設定具有單一組態選項︰

| 索引鍵 | 值 | 預設值 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 當此索引鍵不存在、未設定或是設為 TRUE 時，若使用者未註冊，則擴充功能將無法通過 MFA 挑戰。 當此索引鍵設為 FALSE 時，若使用者未註冊，將會繼續驗證但不執行 MFA。

您可以選擇建立此索引鍵，並在使用者登入期間將它設為 FALSE。 設定索引鍵可讓未註冊 MFA 的使用者進行登入而不需要接受挑戰，因此您應該先移除此索引鍵再移至生產環境。

## <a name="troubleshooting"></a>疑難排解

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>如何確認已如預期安裝用戶端憑證？

在憑證存放區中尋找安裝程式所建立的自我簽署憑證，並確認私密金鑰已將權限授與給使用者 **NETWORK SERVICE**。 憑證的主體名稱會是 **CN \<tenantid\>, OU = Microsoft NPS Extension**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>如何確認用戶端憑證是否已和 Azure Active Directory 中的租用戶相關聯？

開啟 PowerShell 命令提示字元並執行下列命令：

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

這些命令會列印出將租用戶與 PowerShell 工作階段中之 NPS 擴充功能執行個體相關聯的所有憑證。 將用戶端憑證匯出為不含私密金鑰的 "Base-64 encoded X.509(.cer)" 檔案，然後與 PowerShell 中的清單比較，以尋找您的憑證。

如果命令傳回多個憑證，則可以使用採人類看得懂之格式的 Valid-From 和 Valid-Until 時間戳記來篩選出明顯不符者。

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>為何要求會失敗並出現 ADAL 權杖錯誤？

此錯誤可能來自多種原因之一。 請使用下列步驟來協助疑難排解︰

1. 重新啟動 NPS 伺服器。
2. 確認已如預期安裝用戶端憑證。
3. 確認憑證已與 Azure AD 上的租用戶相關聯。
4. 確認可以從執行擴充功能的伺服器存取 https://login.windows.new/。

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>驗證為何失敗，並且 HTTP 記錄中有指出找不到使用者的錯誤？

確認 AD Connect 正在執行，且使用者已存在於 Windows Active Directory 與 Azure Active Directory。

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>為何我會在記錄中看到 HTTP 連線錯誤，且我的所有驗證都失敗？

確認可以從執行 NPS 擴充功能的伺服器連線到 https://adnotifications.windowsazure.com。

## <a name="next-steps"></a>後續步驟

了解如何整合 Azure MFA 與[Active Directory](multi-factor-authentication-get-started-server-dirint.md)、[RADIUS 驗證](multi-factor-authentication-get-started-server-radius.md)和 [LDAP 驗證](multi-factor-authentication-get-started-server-ldap.md)。



<!--HONumber=Feb17_HO1-->


