<properties
    pageTitle="Azure Government 文件 | Microsoft Azure"
    description="這為 Azure Government 的開發應用程式提供功能和指引的比較"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="08/25/2016"
    ms.author="ryansoc"/>



#  <a name="azure-government-documentation-overview"></a>Azure Government 文件概觀

##  <a name="introduction-to-azure-government-documentation"></a>Azure Government 文件簡介

本網站描述 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) 服務的功能，並提供適用於所有客戶的一般指引。 在 Azure Government 訂用帳戶中納入受具體管制的資料之前，請先熟悉 Azure Government 的功能，如有任何問題，請向客戶小組洽詢。

您應該參閱 [Microsoft Azure 信任中心的規範頁面](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)，以取得特定認證和法規下所涵蓋之 Azure Government 服務的最新資訊。 可能也會有其他 Microsoft 服務可供使用，但不在 Azure Government 所涵蓋服務的範圍內，而且本文件也不會加以說明。 Azure Government 服務也可能會允許您使用協力廠商 (或 Microsoft 依據使用和隱私權原則的個別條款) 所提供的各種不在本文涵蓋範圍內的額外資源、應用程式或服務。 您有責任檢閱所有這類「附加」服務 (例如 Martketplace 服務) 的條款，以確保它們符合您在規範方面的需求。

Azure Government 可供處理需遵守特定政府法規和規定 (例如 NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS) 之資料的實體使用，但使用 Azure Government 時必須遵守法規。 Azure Government 客戶必須接受資格驗證。

實體若有關於 Azure Government 資格的問題，應向其客戶小組洽詢。

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Azure Government 中的客戶資料保護原則

Azure Government 提供了各種功能和服務，供您建置雲端解決方案以符合受管制/受控制資料的需求。 符合規範的客戶解決方案無非是現成 Azure Government 功能的有效實作，再結合穩固的資料安全性作法。
當您在 Azure Government 中裝載解決方案時，Microsoft 會在雲端基礎結構層級處理其中的許多需求。

下圖顯示 Azure 的深度防禦模型。 例如，Microsoft 會提供基本的雲端基礎結構 DDOS 以及客戶功能，例如適用於客戶專屬的應用程式 DDOS 需求的安全性應用裝置。

![替代文字](./media/azure-government-Defenseindepth.png)

此頁面概述用來保護服務和應用程式的基本原則，提供如何套用這些原則的指引和最佳作法；換句話說就是客戶該如何巧妙運用 Azure Government，以符合處理 ITAR 資訊之解決方案所需的責任與義務。

保護客戶資料的首要原則是︰
* 使用加密保護資料
* 管理密碼
* 隔離以限制資料存取

##  <a name="protecting-customer-data-using-encryption"></a>使用加密保護客戶資料

降低風險並符合法規義務的需求促使資料加密的關注度和重要性日益增加。 請使用有效的加密實作來加強目前的網路和應用程式安全性措施，並降低雲端環境的整體風險。

### <a name="<a-name="overview"></a>encryption-at-rest"></a><a name="Overview"></a>待用加密
待用資料加密適用於保護磁碟儲存體中保留的客戶內容。 有數種方法能夠達成此目的：

### <a name="<a-name="overview"></a>storage-service-encryption"></a><a name="Overview"></a>儲存體服務加密

Azure 儲存體服務加密是在儲存體帳戶層級啟用，讓區塊 Blob 和分頁 Blob 可以在寫入 Azure 儲存體時自動加密。 當您從 Azure 儲存體讀取資料時，儲存體服務會在傳回資料之前將之解密。 使用此方法可保護資料，而不需修改程式碼或將程式碼加入任何應用程式。

### <a name="<a-name="overview"></a>azure-disk-encryption"></a><a name="Overview"></a>Azure 磁碟加密
使用 Azure 磁碟加密可加密 Azure 虛擬機器所使用的作業系統磁碟和資料磁碟。 與 Azure 金鑰保存庫整合可給予您控制權，並協助您管理磁碟加密金鑰。

### <a name="<a-name="overview"></a>client-side-encryption"></a><a name="Overview"></a>用戶端加密
用戶端加密內建於 Java 和 .NET 儲存體用戶端程式庫，其可以使用 Azure 金鑰保存庫 API，讓此方法的實作變得很簡單。 使用 Azure 金鑰保存庫來為使用 Azure Active Directory 的特定個人取得 Azure 金鑰保存庫中密碼的存取權。

### <a name="<a-name="overview"></a>encryption-in-transit"></a><a name="Overview"></a>傳輸中加密

適用於 Azure Government 連線的基本加密支援傳輸層安全性 (TLS) 1.2 通訊協定和 X.509 憑證。 美國聯邦資訊處理標準 (FIPS) 140-2 Level 1 的密碼編譯演算法也可用於 Azure Government 資料中心之間的基礎結構網路連線。  Windows Server 2012 R2、Windows 8-plus VM 和 Azure 檔案共用可以在 VM 與檔案共用之間使用 SMB 3.0 進行加密。 使用用戶端加密以在將資料傳輸至用戶端應用程式中的儲存體之前加密資料，以及在從儲存體傳出後解密資料。

### <a name="<a-name="overview"></a>best-practices-for-encryption"></a><a name="Overview"></a>加密的最佳作法

* IaaS VM︰使用 Azure 磁碟加密。 開啟儲存體服務加密，來加密在 Azure 儲存體中用來備份這些磁碟的 VHD 檔案，但它只會加密新寫入的資料。 這表示，如果您建立 VM，然後在保留 VHD 檔案的儲存體帳戶上啟用儲存體服務加密，則只會加密變更，而不會加密原始的 VHD 檔案。
* 用戶端加密：這是加密資料的最安全方法，因為它會在傳輸前加密資料，並加密待用資料。 不過，它需要您使用儲存體將程式碼加入應用程式，而您可能不想這樣做。 在這些情況下，您可以針對傳輸中的資料使用 HTTPS，以及儲存體服務加密來加密待用資料。 用戶端加密也會在用戶端上產生更多負載，您必須在延展性計畫中考慮到這一點，特別是如果您要加密並傳輸許多資料。

如需 Azure 中加密選項的詳細資訊，請參閱 [儲存體安全性指南](/storage-security-guide)。

##  <a name="protecting-customer-data-by-managing-secrets"></a>管理密碼以保護客戶資料

安全金鑰管理對於雲端保護資料十分重要。 客戶應努力簡化金鑰管理，並持續掌控雲端應用程式和服務用來加密資料的金鑰。

### <a name="<a-name="overview"></a>best-practices-for-managing-secrets"></a><a name="Overview"></a>管理密碼的最佳作法

* 使用金鑰保存庫將可能透過硬式編碼組態檔、指令碼或原始程式碼公開密碼的風險降到最低。 Azure 金鑰保存庫會加密金鑰 (例如 Azure 磁碟加密的加密金鑰) 和機密資料 (例如密碼)，方法是將它們儲存在通過 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 中。 為了加強保證，您可以在這些 HSM 中匯入或產生金鑰。
* 應用程式的程式碼和範本只應包含密碼的 URI 參考 (也就是說，實際的密碼不在程式碼、組態或原始程式碼儲存機制中)。 這可防止內部或外部儲存機制的金鑰網路釣魚攻擊，例如 GitHub 中的 harvest-bots。
* 在金鑰保存庫內使用強式 RBAC 控制。 如果受信任的操作員離職或轉調到公司內的新群組，則應該防止讓他們能夠存取機密資料。  

如需詳細資訊，請參閱 [Azure Government 的金鑰保存庫](/azure-government/azure-government-tech-keyvault)

##  <a name="isolation-to-restrict-data-access"></a>隔離以限制資料存取

隔離的關鍵在於使用界限、分割和容器，以限制只有經過授權的使用者、服務和應用程式能夠存取資料。 例如，租用戶之間的分隔是多租用戶雲端平台 (例如 Microsoft Azure) 的基本安全性機制。 邏輯隔離有助於防止租用戶干擾任何其他租用戶的作業。

### <a name="<a-name="overview"></a>environment-isolation"></a><a name="Overview"></a>環境隔離
Azure Government 環境是與 Microsoft 其他網路隔開的實體執行個體。 這透過一系列包括下列各項的實體和邏輯控制即可達成︰使用生物識別裝置與相機來確保實體屏障。  需要以邏輯方式存取生產環境的 Microsoft 人員需使用特定認證和多重要素驗證。  Azure Government 的所有服務基礎結構都位於美國境內。

#### <a name="<a-name="overview"></a>per-customer-isolation"></a><a name="Overview"></a>每個客戶隔離
Azure 會透過 VLAN 隔離、ACL、負載平衡器和 IP 篩選器，實作網路存取控制和隔離

客戶可進一步隔離其在訂用帳戶、資源群組、虛擬網路和子網路的資源。

如需在 Microsoft Azure 隔離的詳細資訊，請參閱 [Azure 安全性指南的隔離章節](/azure-security-getting-started/#isolation)。

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>



<!--HONumber=Oct16_HO2-->


