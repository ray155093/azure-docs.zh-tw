---
title: "Azure AD Connect︰版本發行歷程記錄 | Microsoft Docs"
description: "本文章列出 Azure AD Connect 和 Azure AD Sync 的所有版本"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4703cd03db398d8dc59fb5f5c0cf71214c606bc8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect︰版本發行歷程記錄
Azure Active Directory (Azure AD) 團隊會定期以新的特性和功能更新 Azure AD Connect。 並非所有新增項目都適用於所有的對象。

本文旨在協助您追蹤已發行的版本，以及了解您是否需要更新為最新版本。

下列為相關主題的清單︰


主題 |  詳細資料
--------- | --------- |
從 Azure AD Connect 升級的步驟 | [從舊版升級到最新版本](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect 的多種方法。
所需的權限 | 如需套用更新所需權限的詳細資訊，請參閱[帳戶和權限](./active-directory-aadconnect-accounts-permissions.md#upgrade)。
下載| [下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="115240"></a>1.1.524.0
發行日期：2017 年 5 月

> [!IMPORTANT]
> 這個組建引進了結構描述和同步處理規則變更。 升級之後，Azure AD Connect 同步處理服務將會觸發完整匯入和完整同步處理步驟。 變更的詳細資料如下所述。
>
>

**已修正的問題：**

Azure AD Connect 同步處理

* 已修正導致下列情況的問題：即使客戶已使用 Set-ADSyncAutoUpgrade Cmdlet 停用此功能，Azure AD Connect 伺服器仍發生自動升級。 透過此修正，伺服器上的自動升級程序仍會定期檢查升級，但下載的安裝程式會接受自動升級組態。
* 在 DirSync 就地升級期間，Azure AD Connect 會建立 Azure AD 服務帳戶，以供 Azure AD 連接器用來與 Azure AD 同步處理。 建立帳戶之後，Azure AD Connect 會使用此帳戶向 Azure AD 進行驗證。 有時候，驗證會因為暫時性問題而失敗，這會導致 DirSync 就地升級失敗，並出錯「實行設定 AAD 同步工作時發生錯誤︰AADSTS50034︰若要登入此應用程式，必須將帳戶新增至 xxx.onmicrosoft.com 目錄」錯誤。 為了改善 DirSync 升級的彈性，Azure AD Connect 現在會重試驗證步驟。
* 組建 443 發生問題，其導致 DirSync 就地升級成功，但未建立目錄同步作業所需的執行設定檔。 修復邏輯已包含在 Azure AD Connect 的此組建中。 當客戶升級到此組建時，Azure AD Connect 會偵測遺漏的執行設定檔並加以建立。
* 已修正導致下列情況的問題：密碼同步處理程序無法啟動，出現事件識別碼 6900 和「已新增具有相同索引鍵的項目」錯誤。 如果您將 OU 篩選組態更新為包含 AD 組態分割，就會發生此問題。 若要修正此問題，密碼同步處理程序現在只會從 AD 網域分割同步處理密碼變更。 系統會略過非網域分割，例如組態分割。
* 在快速安裝期間，Azure AD Connect 會建立內部部署 AD DS 帳戶，以供 AD 連接器用來與內部部署 AD 通訊。 先前建立此帳戶時，PASSWD_NOTREQD 旗標設定於 user-Account-Control 屬性上，而隨機密碼會設定於此帳戶上。 現在，Azure AD Connect 會在密碼設定於帳戶之後，明確地移除 PASSWD_NOTREQD 旗標。
* 已修正導致下列情況的問題：在內部部署 AD 結構描述中找到 mailNickname 屬性，但該屬性並未繫結至 AD 使用者物件類別時，DirSync 升級失敗，並出現「嘗試取得應用程式鎖定的 SQL Server 發生死結」錯誤。
* 已修正導致下列情況的問題：當系統管理員使用 Azure AD Connect 精靈更新 Azure AD Connect 同步處理組態時，裝置回寫功能自動停用。 這是由對內部部署 AD 中現有裝置回寫組態執行先決條件檢查的精靈所造成，而且檢查失敗。 如果先前已啟用裝置回寫，此修正就會略過檢查。
* 若要設定 OU 篩選，您可以使用 Azure AD Connect 精靈或 Synchronization Service Manager。 先前，如果您使用 Azure AD Connect 精靈來設定 OU 篩選，則會包含後來建立的新 OU 以便進行目錄同步作業。 如果您不想要包含新的 OU，您必須使用 Synchronization Service Manager 設定 OU 篩選。 現在，您可以使用 Azure AD Connect 精靈達成相同的行為。
* 已修正導致下列情況的問題：Azure AD Connect 所需的預存程序建立於安裝系統管理員的結構描述之下，而不是建立於 dbo 結構描述之下。
* 已修正導致下列情況的問題：AAD Connect 伺服器事件記錄中省略 Azure AD 所傳回的 TrackingId 屬性。 如果 Azure AD Connect 收到來自 Azure AD 的重新導向訊息，且 Azure AD Connect 無法連線到所提供的端點，就會發生此問題。 在疑難排解期間，支援工程師可以使用 TrackingId 與服務端記錄相互關聯。
* 當 Azure AD Connect 從 Azure AD 收到 LargeObject 錯誤時，Azure AD Connect 會產生一個事件 (EventID 6941) 並出現「佈建的物件是太大。請修剪此物件的屬性值數量」訊息。 在此同時，Azure AD Connect 也會產生誤導事件 (EventID 6900) 並出現「Microsoft.Online.Coexistence.ProvisionRetryException︰無法與 Windows Azure Active Directory 服務通訊」訊息。 為了避免混淆，Azure AD Connect 不再於收到 LargeObject 錯誤時產生後面的事件。
* 已修正導致下列情況的問題：Synchronization Service Manager 在嘗試更新「一般 LDAP 連接器」的組態時變得沒有反應。

**新功能/改進︰**

Azure AD Connect 同步處理
* 同步處理規則變更 – 已實作下列同步處理規則變更︰
  * 已將預設同步處理規則集更新為 **userCertificate** 和 **userSMIMECertificate** 屬性有超過 15 個值時不匯出屬性。
  * AD 屬性 **employeeID** 和 **msExchBypassModerationLink** 現在已包含在預設同步處理規則集中。
  * 已經從預設同步處理規則集中移除 AD 屬性 **photo**。
  * 已將 **preferredDataLocation** 新增至 Metaverse 結構描述和 AAD 連接器結構描述。 想要在 Azure AD 中更新任一屬性的客戶若要這樣做，可以實作自訂同步處理規則。 若要深入了解屬性，請參閱 [Azure AD Connect 同步處理︰如何變更預設組態 - 啟用 PreferredDataLocation 的同步處理](active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-preferreddatalocation)文章章節。
  * 已將 **userType** 新增至 Metaverse 結構描述和 AAD 連接器結構描述。 想要在 Azure AD 中更新任一屬性的客戶若要這樣做，可以實作自訂同步處理規則。

* Azure AD Connect 現在能夠自動使用 ConsistencyGuid 屬性作為內部部署 AD 物件的來源錨點屬性。此外，Azure AD Connect 會使用 objectGuid 屬性值填入 ConsistencyGuid 屬性 (如果它是空的)。 這項功能僅適用於新的部署。 若要深入了解此功能，請參閱 [Azure AD Connect︰設計概念 - 使用 msDS-ConsistencyGuid 作為 sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor) 文章章節。
* 已新增疑難排解 Cmdlet Invoke-ADSyncDiagnostics，可協助診斷密碼雜湊同步處理相關問題。
* Azure AD Connect 現在支援將擁有郵件功能的公用資料夾物件從內部部署 AD 同步處理到 Azure AD。 您可以在 [選用功能] 之下使用 Azure AD Connect 精靈啟用此功能。
* Azure AD Connect 要求 AD DS 帳戶從內部部署 AD 進行同步處理。 先前，如果您使用快速模式安裝 Azure AD Connect，您可以提供企業管理帳戶的認證。 將它留給 Azure AD Connect 建立所需的 AD DS 帳戶。 不過，如需自訂安裝以及將樹系新增至現有的部署，您必須改為提供 AD DS 帳戶。 現在，您也可以選擇在自訂安裝期間提供企業管理帳戶的認證，讓 Azure AD Connect 建立所需的 AD DS 帳戶。
* Azure AD Connect 現在支援 SQL AOA。 您必須在安裝 Azure AD Connect 之前啟用 SQL： 在安裝期間，Azure AD Connect 會偵測所提供的 SQL 執行個體是否已啟用 SQL AOA。 如果已啟用 SQL AOA，Azure AD Connect 會進一步指出 SQL AOA 已設定為使用同步複寫或非同步複寫。 設定可用性群組接聽程式時，建議將 RegisterAllProvidersIP 屬性設定為 0。 這是因為 Azure AD Connect 目前使用 SQL Native Client 來連線至 SQL，且 SQL Native Client 不支援使用 MultiSubNetFailover 屬性。
* 如果您使用 LocalDB 作為 Azure AD Connect 伺服器的資料庫，而且已達到其 10 GB 大小的限制，同步處理服務便無法再啟動。 先前，您需要在 LocalDB 上執行 ShrinkDatabase 作業，以收回足夠的 DB 空間以供同步處理服務啟動。 在那之後，您可以使用 Synchronization Service Manager 來刪除執行歷程記錄，以回收更多的 DB 空間。 現在，您可以使用 Start-ADSyncPurgeRunHistory Cmdlet 來清除 LocalDB 中的執行歷程記錄資料，以回收 DB 空間。 此外，此 Cmdlet 支援可在同步處理服務未執行時使用的離線模式 (藉由指定 -offline 參數)。 注意︰只有在同步處理服務未執行且使用的資料庫是 LocalDB 時，才能使用離線模式。
* 為了減少所需的儲存體空間，Azure AD Connect 現在會在 LocalDB/SQL 資料庫中儲存同步處理錯誤詳細資料前加以壓縮。 從舊版 Azure AD Connect 升級到此版本時，Azure AD Connect 會對現有的同步處理錯誤詳細資料執行一次性壓縮。
* 先前，在更新 OU 篩選組態之後，您必須手動執行完整匯入，以確保目錄同步作業正確包含/排除現有的物件。 現在，Azure AD Connect 會在下一個同步處理週期內自動觸發完整匯入。 此外，完整匯入只會套用至受到更新影響的 AD 連接器。 注意︰這項改進只適用於使用 Azure AD Connect 精靈進行的 OU 篩選更新。 不適用於使用 Synchronization Service Manager 進行的 OU 篩選更新。
* 先前，以群組為基礎的篩選僅支援使用者、群組和連絡人物件。 現在，以群組為基礎的篩選也支援電腦物件。
* 先前，您可以刪除連接器空間資料，而不需停用 Azure AD Connect 同步排程器。 現在，如果 Synchronization Service Manager 偵測到已啟用排程器，則會阻止刪除連接器空間資料。 此外，還會傳回警告，通知客戶有關刪除連接器空間資料可能會遺失資料。
* 先前，您必須停用 PowerShell 轉譯，Azure AD Connect 精靈才能正常執行。 此問題已部份解決。 如果您使用 Azure AD Connect 精靈來管理同步處理組態，您可以啟用 PowerShell 轉譯。 如果您使用 Azure AD Connect 精靈來管理 ADFS 組態，您必須停用 PowerShell 轉譯。



## <a name="114860"></a>1.1.486.0
發行日期︰2017 年 4 月

**已修正的問題：**
* 修正 Azure AD Connect 無法在已當地語系化的 Windows Server 版本上成功安裝的問題。

## <a name="114840"></a>1.1.484.0
發行日期︰2017 年 4 月

**已知問題︰**

* 如果下列條件都成立，此 Azure AD Connect 版本將無法成功安裝：
   1. 您執行 DirSync 就地升級或全新的 Azure AD Connect 安裝。
   2. 您使用已當地語系化的 Windows Server 版本，其中伺服器上內建的「系統管理員」群組名稱不是 "Administrators"。
   3. 您使用與 Azure AD Connect 一起安裝的預設 SQL Server 2012 Express LocalDB，而不是提供自己的完整 SQL。

**已修正的問題：**

Azure AD Connect 同步處理
* 修正當一或多個連接器遺漏某個同步處理步驟的執行設定檔時，同步排程器會略過該整個同步處理步驟的問題。 例如，您已使用 Synchronization Service Manager 來手動新增某個連接器，但沒有為它建立「差異匯入」執行設定檔。 這項修正可確保同步排程器會繼續執行其他連接器的「差異匯入」。
* 修正「同步處理服務」在遇到其中一個執行步驟發生問題時，會立即停止處理執行設定檔的問題。 這項修正可確保「同步處理服務」會略過該執行步驟並繼續處理其餘步驟。 例如，您有一個「差異匯入」執行設定檔，用於具有多個執行步驟 (每個內部部署 AD 網域各有一個執行步驟) 的 AD 連接器。 即使其中一個 AD 網域有網路連線問題，「同步處理服務」也會在其他 AD 網域執行「差異匯入」。
* 修正導致在進行「自動升級」時略過 Azure AD Connector 更新的問題。
* 修正導致 Azure AD Connect 在設定時不正確地判斷伺服器是否為網域控制站的問題，此問題會進而導致 DirSync 升級失敗。
* 修正導致 DirSync 就地升級不會為 Azure AD Connector 建立任何執行設定檔的問題。
* 修正在嘗試設定「一般 LDAP 連接器」時，Synchronization Service Manager 使用者介面變成沒有反應的問題。

AD FS 管理
* 修正在已將 AD FS 主要節點移至另一部伺服器的情況下，Azure AD Connect 精靈會發生失敗的問題。

傳統型 SSO
* 修正 Azure AD Connect 精靈中的問題，也就是在進行新安裝時，如果您選擇 [密碼同步處理] 作為登入選項，[登入] 畫面將不會讓您啟用 [傳統型 SSO] 功能。

**新功能/改進︰**

Azure AD Connect 同步處理
* Azure AD Connect Sync 現在支援使用「虛擬服務帳戶」、「受管理的服務帳戶」及「群組受管理服務帳戶」作為其服務帳戶。 這僅適用於新的 Azure AD Connect 安裝。 安裝 Azure AD Connect 時：
    * 根據預設，Azure AD Connect 精靈會建立一個「虛擬服務帳戶」，並使用它作為其服務帳戶。
    * 如果您是在網域控制站上進行安裝，Azure AD Connect 就會回復成先前的行為，也就是會建立網域使用者帳戶，並改用它作為服務帳戶。
    * 您可以透過提供下列其中一項，來覆寫預設的行為：
      * 群組受管理服務帳戶
      * 受管理的服務帳戶
      * 網域使用者帳戶
      * 本機使用者帳戶
* 以前，如果您升級至包含連接器更新或同步處理規則變更的新 Azure AD Connect 組建，Azure AD Connect 會觸發一個完整的同步處理週期。 現在，Azure AD Connect 會選擇性地僅針對具有更新的連接器觸發「完整匯入」步驟，以及僅針對具有同步處理規則變更的連接器觸發「完整同步處理」步驟。
* 以前，「匯出刪除閾值」僅適用於透過同步排程器觸發的匯出。 現在，該功能已延伸到包含由客戶使用 Synchronization Service Manager 來手動觸發的匯出。
* 在您的 Azure AD 租用戶上，有一個指出是否已為租用戶啟用「密碼同步處理」功能的服務組態。 以前，當您有作用中伺服器和預備環境伺服器時，Azure AD Connect 很容易以不正確的方式設定服務組態。 現在，Azure AD Connect 會嘗試讓服務組態只與您的作用中 Azure AD Connect 伺服器保持一致。
* 如果內部部署 AD 未啟用「AD 資源回收筒」，Azure AD Connect 精靈現在會偵測到並傳回警告。
* 以前，如果批次中物件的組合大小超出特定閾值，「匯出至 Azure AD」就會逾時。 現在，如果遇到此問題，「同步處理服務」會重新嘗試以個別、較小的批次重新傳送物件。
* 「同步處理服務金鑰管理」應用程式已從 Windows [開始] 功能表中移除。 將會透過命令列介面使用 miiskmu.exe 來繼續支援加密金鑰的管理。 如需有關加密金鑰的資訊，請參閱[放棄 Azure AD Connect Sync 加密金鑰](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key)一文。
* 以前，如果您變更 Azure AD Connect 同步服務帳戶密碼，「同步處理服務」將會無法正確啟動，直到您放棄加密金鑰並將 Azure AD Connect 同步服務帳戶密碼重新初始化為止。 現在，已不再需要這麼做。

傳統型 SSO

* 設定「傳遞驗證」和「傳統型 SSO」時，Azure AD Connect 精靈已不再需要於網路上開啟連接埠 9090。 只需要開啟連接埠 443。 

## <a name="114430"></a>1.1.443.0
發行日期︰2017 年 3 月

**已修正的問題：**

Azure AD Connect 同步處理
* 修正當「Azure AD 連接器」的顯示名稱不包含指派給 Azure AD 租用戶的初始 onmicrosoft.com 網域時，會造成 Azure AD Connect 精靈發生失敗的問題。
* 修正當「同步處理服務帳戶」的密碼包含特殊字元 (例如單引號、冒號及空格) 時，會造成 Azure AD Connect 精靈在連線到 SQL Database 時發生失敗的問題。
* 修正在您暫時將內部部署 AD 物件從同步處理作業中排除又再重新包含在同步處理作業中之後，會造成在處於預備模式的 Azure AD Connect 伺服器上發生「映像具有與映像不同的錨點」錯誤的問題。
* 修正在您暫時將內部部署 AD 物件從同步處理作業中排除又再重新包含在同步處理作業中之後，會造成在處於預備模式的 Azure AD Connect 伺服器上發生「DN 所定位的物件是虛設項目」錯誤的問題。

AD FS 管理
* 修正在設定「替代登入識別碼」之後，Azure AD Connect 精靈不會更新 AD FS 組態並在信賴憑證者信任上設定正確宣告的問題。
* 修正 Azure AD Connect 精靈對於服務帳戶是使用 userPrincipalName 格式而非 sAMAccountName 格式來設定的 AD FS 伺服器，無法正確進行處理的問題。

傳遞驗證
* 修正在已選取「傳遞驗證」但註冊其連接器失敗的情況下，會造成 Azure AD Connect 精靈發生失敗的問題。
* 修正在已啟用「傳統型 SSO」功能的情況下，會造成 Azure AD Connect 精靈在所選登入方法上略過驗證檢查的問題。

密碼重設
* 已修正如果防火牆或 Proxy 伺服器已清除連線，可能導致 Azure AAD Connect 伺服器不會嘗試重新連線的問題。

**新功能/改進︰**

Azure AD Connect 同步處理
* Get-ADSyncScheduler Cmdlet 現在會傳回一個名為 SyncCycleInProgress 的新布林值屬性。 如果傳回的值為 true，即表示有已排定的同步處理循環正在進行。
* 用來儲存 Azure AD Connect 安裝和設定記錄檔的目的地資料夾已經從 %localappdata%\AADConnect 移到 %programdata%\AADConnect，以提升記錄檔的存取便利性。

AD FS 管理
* 新增對更新「AD FS 伺服器陣列 SSL 憑證」的支援。
* 新增對管理 AD FS 2016 的支援。
* 您現在可以在安裝 AD FS 時指定現有的 gMSA (群組受管理服務帳戶)。
* 您現在可以為 Azure AD 信賴憑證者信任設定 SHA-256 作為簽章雜湊演算法。

密碼重設
* 已導入允許產品在具有更嚴格防火牆規則的環境中運作的增強功能。
* 已改良對 Azure 服務匯流排的連線可靠性。

## <a name="113800"></a>1.1.380.0
發行日期︰2016 年 12 月

**修正的問題︰**

* 修正此組建中遺漏 Active Directory Federation Services (AD FS) 之 issuerid 宣告規則的問題。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113710"></a>1.1.371.0
發行日期︰2016 年 12 月

**已知問題︰**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure Active Directory (Azure AD) 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](active-directory-aadconnect-multiple-domains.md)上的此文章。

**修正的問題︰**

* 如果未開啟連接埠 9090 供輸出連線，Azure AD Connect 安裝或升級就會失敗。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113700"></a>1.1.370.0
發行日期︰2016 年 12 月

**已知問題︰**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure AD 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](active-directory-aadconnect-multiple-domains.md)上的此文章。
* 連接埠 9090 必須開啟輸出以完成安裝。

**新功能︰**

* 傳遞驗證 (預覽)。

>[!NOTE]
>此組建無法透過 Azure AD Connect 自動升級功能提供客戶使用。

## <a name="113430"></a>1.1.343.0
發行日期：2016 年 11 月

**已知問題︰**

* 此組建中遺漏 ADFS 的 issuerid 宣告規則。 如果您要讓多個網域與 Azure AD 同盟，就必須要有此 issuerid 宣告規則。 如果您使用 Azure AD Connect 來管理您的內部部署 ADFS 部署，則升級至此組建將會從 ADFS 組態中移除現有的 issuerid 宣告規則。 您可以在安裝/升級之後新增 issuerid 宣告規則來解決此問題。 如需有關新增 issuerid 宣告規則的詳細資料，請參閱[與 Azure AD 同盟的多網域支援](active-directory-aadconnect-multiple-domains.md)上的此文章。

**已修正的問題：**

* 有時候，Azure AD Connect 會安裝失敗，因為無法建立密碼符合組織密碼原則所指定之複雜程度的本機服務帳戶。
* 已修正以下問題：當連接器空間中的物件同時落在某個聯結規則的範圍外以及另一個聯結規則的範圍內時，不會重新評估聯結規則。 如果有兩個或多個聯結規則的聯結條件互斥，也可能發生此問題。
* 已修正以下問題：相較於包含聯結規則的輸入同步處理規則，如果未包含聯結規則的輸入同步處理規則 (來自 Azure AD) 具有較低的優先順序值，則不會處理未包含聯結規則的輸入同步處理規則。

**改進：**

* 新增了在 Windows Server 2016 標準版或更新版本上安裝 Azure AD Connect 的支援。
* 新增了使用 SQL Server 2016 做為 Azure AD Connect 之遠端資料庫的支援。

## <a name="112810"></a>1.1.281.0
發行日期：2016 年 8 月

**已修正的問題：**

* 直到下一個同步週期完成後，才會進行同步間隔的變更。
* Azure AD Connect 精靈不接受使用者名稱以底線 (\_) 開頭的 Azure AD 帳戶。
* 如果帳戶密碼包含太多特殊字元，Azure AD Connect 精靈便無法驗證所提供的 Azure AD 帳戶。 此時會傳回「無法驗證認證。 已發生未預期的錯誤。 」錯誤訊息。
* 解除安裝預備伺服器會使 Azure AD 租用戶的密碼同步處理停用，並導致作用中伺服器的密碼同步處理失敗。
* 在使用者未儲存任何密碼雜湊的罕見情況下，密碼同步處理會失敗。
* 當 Azure AD Connect 伺服器啟用預備模式時，不會暫時停用密碼回寫。
* 當伺服器處於預備模式時，Azure AD Connect 精靈不會顯示實際的密碼同步處理和密碼回寫組態。 這些一律會顯示為停用。
* 伺服器處於預備模式時，Azure AD Connect 精靈不會保存密碼同步處理和密碼回寫的組態變更。

**改進：**

* 已更新 Start-ADSyncSyncCycle Cmdlet，可指出是否能夠順利啟動新的同步處理週期。
* 已新增 Stop-ADSyncSyncCycle Cmdlet，可終止目前正在進行中的同步處理週期和作業。
* 已更新 Stop-ADSyncScheduler Cmdlet，可終止目前正在進行中的同步處理週期和作業。
* 在 Azure AD Connect 精靈中設定[目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md)時，現在可以選取「Teletex 字串」類型的 Azure AD 屬性。

## <a name="111890"></a>1.1.189.0
發行日期：2016 年 6 月

**已修正的問題和改進︰**

* Azure AD Connect 現在可以安裝於符合 FIPS 規範的伺服器上。
  * 針對密碼同步處理，請參閱[密碼同步處理和 FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)。
* 已修正下列問題：NetBIOS 名稱無法解析為 Active Directory 連接器中的 FQDN。

## <a name="111800"></a>1.1.180.0
發行日期：2016 年 5 月

**新功能︰**

* 如果您未先驗證網域就執行 Azure AD Connect，系統將會發出警告並協助您驗證網域。
* 已新增對 [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany)的支援。
* 已新增對最新 [Microsoft Azure Government 雲端](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) 基礎結構與新的 URL 需求的支援。

**已修正的問題和改進︰**

* 對同步處理規則編輯器新增篩選，以更加容易找到同步處理規則。
* 提升刪除連接器空間時的效能。
* 修正在同一個回合中同時刪除和新增相同物件 (稱為刪除/新增) 時的問題。
* 已停用的同步處理規則不會在升級或目錄結構描述重新整理時，再重新啟用包含的物件和屬性。

## <a name="111300"></a>1.1.130.0
發行日期︰2016 年 4 月

**新功能︰**

* [目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md)已新增支援多重值屬性。
* [自動升級](active-directory-aadconnect-feature-automatic-upgrade.md) 已新增支援更多組態變化，以符合升級資格。
* 已新增一些適用於 [自訂排程器](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)的 Cmdlet。

## <a name="111190"></a>1.1.119.0
發行日期︰2016 年 3 月

**已修正的問題：**

* 請確定 Windows Server 2008 (發行前 R2) 上無法使用快速安裝，因為此作業系統不支援密碼同步處理。
* 使用自訂篩選器設定從 DirSync 升級並未如預期般運作。
* 升級至較新版本且沒有進行任何組態變更時，不應排程完整匯入/同步處理。

## <a name="111100"></a>1.1.110.0
發行日期︰2016 年 2 月

**已修正的問題：**

* 如果安裝不是位於預設的 C:\Program Files 資料夾中，則無法從舊版升級。
* 如果您安裝並在結束安裝精靈時清除 [啟動同步處理程序]，再次執行安裝精靈將不會啟用排程器。
* 在日期/時間格式並非美國英文的伺服器上，排程器將無法正常運作。 並且會封鎖 `Get-ADSyncScheduler` 傳回正確的時間。
* 如果您是以 ADFS 做為登入選項和升級，來安裝舊版 Azure AD Connect，便無法再次執行安裝精靈。

## <a name="111050"></a>1.1.105.0
發行日期︰2016 年 2 月

**新功能︰**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) 功能。
* 使用 Azure Multi-Factor Authentication 和 Privileged Identity Management 安裝精靈，支援全域管理。
  * 如果您使用 Multi-Factor Authentication，就必須讓您的 Proxy 也允許流向 https://secure.aadcdn.microsoftonline-p.com 的流量。
  * 您必須將 https://secure.aadcdn.microsoftonline-p.com 新增至信任的網站清單，Multi-Factor Authentication 才能正常運作。
* 允許在初始安裝之後變更使用者的登入方法。
* 允許在安裝精靈中使用 [網域和 OU 篩選](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering)。 這也會允許連線到並非所有網域都可供使用的樹系。
* [排程器](active-directory-aadconnectsync-feature-scheduler.md)是同步處理引擎的內建功能。

**從預覽版升級到 GA 的功能：**

* [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)。
* [目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md)。

**新的預覽功能：**

* 新的預設同步處理週期間隔為 30 分鐘。 過去所有舊版本都是 3 小時。 已新增對變更 [排程器](active-directory-aadconnectsync-feature-scheduler.md) 行為的支援。

**已修正的問題：**

* 驗證 DNS 網域頁面不一定都能辨識網域。
* 設定 ADFS 時，出現網域系統管理員認證提示。
* 當內部部署 AD 帳戶所在網域的 DNS 樹狀目錄與根網域不同時，安裝精靈即無法辨識這些帳戶。

## <a name="1091310"></a>1.0.9131.0
發行日期︰2015 年 12 月

**已修正的問題：**

* 您變更 Active Directory Domain Services (AD DS) 中的密碼時，密碼同步處理可能會無法作用，但是在您設定密碼時將可作用。
* 如果您有 Proxy 伺服器，在安裝期間或於組態頁面上取消升級時，向 Azure AD 進行驗證可能會失敗。
* 若您並非 SQL Server 系統管理員 (SA)，從有完整 SQL Server 執行個體的舊版 Azure AD Connect 更新將會失敗。
* 從有遠端 SQL Server 的舊版 Azure AD Connect 更新時，將顯示錯誤訊息「無法存取 ADSync SQL Database」。

## <a name="1091250"></a>1.0.9125.0
發行日期：2015 年 11 月

**新功能︰**

* 可以將 ADFS 重新設定為 Azure AD 信任。
* 可以重新整理 Active Directory 結構描述並重新產生同步處理規則。
* 可以停用同步處理規則。
* 可以在同步處理規則中定義 "AuthoritativeNull" 做為新的常值。

**新的預覽功能：**

* [適用於同步的 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md)。
* 支援 [Azure AD 網域服務](../active-directory-passwords-update-your-own-password.md) 密碼同步處理。

**新的支援案例：**

* 支援多個內部部署的 Exchange 組織。 如需詳細資訊，請參閱[內含多個 Active Directory 樹系的混合式部署](https://technet.microsoft.com/library/jj873754.aspx)。

**已修正的問題：**

* 密碼同步處理問題：
  * 從範圍外移到範圍內的物件不會同步處理其密碼。 這包含 OU 及屬性篩選。
  * 選取要包含在同步處理的新 OU 不需要完整密碼同步處理。
  * 啟用已停用的使用者時，不會同步處理密碼。
  * 密碼重試佇列為無限，而且已移除 5000 個物件要淘汰的先前限制。
<<<<<<< HEAD <<<<<<< HEAD
  * [改良的疑難排解](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)。
=======
  * [改良的疑難排解](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> <a name="487b660b6d3bb5ce9e64b6fdbde2ae621cb91922"></a>487b660b6d3bb5ce9e64b6fdbde2ae621cb91922
=======
  * [改良的疑難排解](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。
>>>>>>> 4b2e846c2cd4615f4e4be7195899de11e3957c83
* 無法連接到具 Windows Server 2016 樹系功能等級的 Active Directory。
* 初始安裝後，無法變更群組篩選所使用的群組。
* 對於在啟用密碼回寫的情況下執行密碼變更的每個使用者，將無法再於 Azure AD Connect 伺服器上建立新的使用者設定檔。
* 無法在同步處理規則範圍中使用長整數值。
* 如果有無法連線的網域控制站，[裝置回寫] 核取方塊會維持停用狀態。

## <a name="1086670"></a>1.0.8667.0
發行日期：2015 年 8 月

**新功能︰**

* Azure AD Connect 安裝精靈現在已進行所有 Windows Server 語言的當地語系化。
* 新增在使用 Azure AD 密碼管理時的帳戶解除鎖定支援。

**已修正的問題：**

* 如果另一位使用者繼續安裝，而不是最先開始安裝的人，則 Azure AD Connect 安裝精靈會當機。
* 如果 Azure AD Connect 的先前解除安裝作業無法將 Azure AD Connect Sync 完全解除安裝，則不可能重新安裝。
* 如果使用者不在樹系的根網域中或已使用非英文版的 Active Directory，則無法使用 Express 安裝 Azure AD Connect。
* 如果無法解析 Active Directory 使用者帳戶的 FQDN，則會顯示「無法認可結構描述」的誤導錯誤訊息。
* 如果 Active Directory 連接器上使用的帳戶已在精靈外部變更，精靈將無法進行後續的執行。
* Azure AD Connect 有時無法在網域控制站上安裝。
* 如果已加入擴充屬性，則無法啟用和停用「預備模式」。
* 因為 Active Directory 連接器上的密碼不正確，所以有些設定的密碼回寫失敗。
* 如果辨別名稱 (DN) 用於屬性篩選，則無法升級 DirSync。
* 使用密碼重設時，CPU 使用率過高。

**已移除的預覽功能：**

* 根據預覽版客戶的意見反應，已暫時移除 [ [使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback) ] 預覽功能。 當我們處理所提供的意見反應之後，未來將會重新新增它。

## <a name="1086410"></a>1.0.8641.0
發行日期：2015 年 6 月

**Azure AD Connect 的最初發行版本。**

名稱從 Azure AD Sync 變更為 Azure AD Connect。

**新功能︰**

* [快速設定](active-directory-aadconnect-get-started-express.md)安裝
* 可以[設定 AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* 可以[從 DirSync 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* 引入 [預備模式](active-directory-aadconnectsync-operations.md#staging-mode)

**新的預覽功能：**

* [使用者回寫](active-directory-aadconnect-feature-preview.md#user-writeback)
* [群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback)
* [裝置回寫](active-directory-aadconnect-feature-device-writeback.md)
* [目錄擴充](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
發行日期：2015 年 5 月

**新需求︰**

* Azure AD Sync 現在需要安裝 .Net framework 4.5.1 版。

**已修正的問題：**

* Azure AD 密碼回寫出現 Azure 服務匯流排連線錯誤。

## <a name="104910413"></a>1.0.491.0413
發行日期︰2015 年 4 月

**已修正的問題和改進︰**

* 如果已啟用資源回收筒且樹系中有多個網域，則 Active Directory 連接器不會正確處理刪除。
* Azure Active Directory 連接器的匯入作業效能已改善。
* 當群組已超過成員資格限制 (預設限制已設為 50000 個物件)，則會在 Azure Active Directory 中刪除此群組。 具有新的行為，不會刪除群組，不會擲回錯誤，且不會匯出新的成員資格變更。
* 如果連接器空間已存在具有相同 DN 的分段刪除，則無法佈建新的物件。
* 在差異同步處理期間，儘管物件上沒有預備的變更，某些物件仍會被標示為同步處理中。
* 強制執行密碼同步也會移除慣用的 DC 清單。
* CSExportAnalyzer 有一些物件狀態的問題。

**新功能︰**

* 聯結現在可以連接到 MV 中的「任何」物件類型。

## <a name="104850222"></a>1.0.485.0222
發行日期︰2015 年 2 月

**改進：**

* 改進匯入效能。

**已修正的問題：**

* 密碼同步處理會接受屬性篩選所用的 cloudFiltered 屬性。 經過篩選的物件不再於密碼同步處理的範圍中。
* 在拓撲有很多網域控制站的少數情況下，無法進行密碼同步處理。
* 在 Azure AD/Intune 中啟用裝置管理之後，從 Azure AD 連接器匯入時的「停止的伺服器」。
* 從相同樹系中的多個網域加入外部安全性主體 (FSP) 會造成模稜兩可的加入錯誤。

## <a name="104751202"></a>1.0.475.1202
發行日期︰2014 年 12 月

**新功能︰**

* 現在支援透過以屬性為基礎的篩選進行密碼同步處理。 如需詳細資訊，請參閱[透過篩選進行密碼同步處理](active-directory-aadconnectsync-configure-filtering.md)。
* msDS-ExternalDirectoryObjectID 屬性會寫回至 Active Directory。 這項功能會新增適用於 Office 365 應用程式的支援。 它會使用 OAuth2 存取混合式 Exchange 部署的線上和內部部署信箱。

**已修正的升級問題︰**

* 在伺服器上可使用較新版的登入小幫手。
* 自訂安裝路徑用來安裝 Azure AD Sync。
* 無效的自訂加入準則會封鎖升級。

**其他修正︰**

* 已修正適用於 Office Pro Plus 的範本。
* 已修正以破折號開頭的使用者名稱所造成的安裝問題。
* 已修正第二次執行安裝精靈時遺失 sourceAnchor 設定的問題。
* 已修正密碼同步處理的 ETW 追蹤。

## <a name="104701023"></a>1.0.470.1023
發行日期︰2014 年 10 月

**新功能︰**

* 從多個內部部署 Active Directory 至 Azure AD 的密碼同步處理。
* 所有 Windows Server 語言的當地語系化安裝 UI。

**從 AADSync 1.0 GA 升級**

如果您已安裝 Azure AD Sync，您必須進行一個額外步驟，以免您已變更任何現成可用的同步處理規則。 在升級至 1.0.470.1023 版本之後，您已修改的同步規則會重複。 針對每個已修改的同步處理規則，執行下列作業︰

1.  找出您已修改的同步處理規則並記下變更。
* 刪除同步處理規則。
* 找出 Azure AD Sync 建立的新同步處理規則，然後重新套用變更。

**Active Directory 帳戶的權限**

Active Directory 帳戶必須獲得其他權限，才能讀取來自 Active Directory 的密碼雜湊。 要授與的權限名為「複寫目錄變更」和「複寫所有目錄變更」。 需要有這兩個權限才能讀取密碼雜湊。

## <a name="104190911"></a>1.0.419.0911
發行日期︰2014 年 9 月

**Azure AD Sync 的最初發行版本。**

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

