---
title: "Azure Government 安全性 | Microsoft Docs"
description: "提供 Azure Government 中可用服務的概觀"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2
ms.lasthandoff: 11/17/2016


---
# <a name="security"></a>安全性
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Azure Government 中的客戶資料保護原則
Azure Government 提供了各種功能和服務，供您建置雲端解決方案以符合受管制/受控制資料的需求。 符合規範的客戶解決方案無非是現成 Azure Government 功能的有效實作，再結合穩固的資料安全性作法。

當您在 Azure Government 中裝載解決方案時，Microsoft 會在雲端基礎結構層級處理其中的許多需求。

下圖顯示 Azure 的深度防禦模型。 例如，Microsoft 會提供基本的雲端基礎結構 DDOS 以及客戶功能，例如適用於客戶專屬的應用程式 DDOS 需求的安全性應用裝置。

![替代文字](./media/azure-government-Defenseindepth.png)

此頁面概述用來保護服務和應用程式的基本原則，提供如何套用這些原則的指引和最佳作法；換句話說就是客戶該如何巧妙運用 Azure Government，以符合處理 ITAR 資訊之解決方案所需的責任與義務。

 保護客戶資料的首要原則是︰

* 使用加密保護資料
* 管理密碼
* 隔離以限制資料存取

### <a name="protecting-customer-data-using-encryption"></a>使用加密保護客戶資料
降低風險並符合法規義務的需求促使資料加密的關注度和重要性日益增加。 請使用有效的加密實作來加強目前的網路和應用程式安全性措施，並降低雲端環境的整體風險。

#### <a name="encryption-at-rest"></a>待用加密
待用資料加密適用於保護磁碟儲存體中保留的客戶內容。 有數種方法能夠達成此目的：

#### <a name="storage-service-encryption"></a>儲存體服務加密
Azure 儲存體服務加密是在儲存體帳戶層級啟用，讓區塊 Blob 和分頁 Blob 可以在寫入 Azure 儲存體時自動加密。 當您從 Azure 儲存體讀取資料時，儲存體服務會在傳回資料之前將之解密。 使用此方法可保護資料，而不需修改程式碼或將程式碼加入任何應用程式。

#### <a name="client-side-encryption"></a>用戶端加密
用戶端加密內建於 Java 和 .NET 儲存體用戶端程式庫，其可以使用 Azure 金鑰保存庫 API，讓此方法的實作變得很簡單。 使用 Azure 金鑰保存庫來為使用 Azure Active Directory 的特定個人取得 Azure 金鑰保存庫中密碼的存取權。

#### <a name="encryption-in-transit"></a>傳輸中加密
適用於 Azure Government 連線的基本加密支援傳輸層安全性 (TLS) 1.2 通訊協定和 X.509 憑證。 美國聯邦資訊處理標準 (FIPS) 140-2 Level 1 的密碼編譯演算法也可用於 Azure Government 資料中心之間的基礎結構網路連線。  Windows Server 2012 R2、Windows 8-plus VM 和 Azure 檔案共用可以在 VM 與檔案共用之間使用 SMB 3.0 進行加密。 使用用戶端加密以在將資料傳輸至用戶端應用程式中的儲存體之前加密資料，以及在從儲存體傳出後解密資料。

#### <a name="best-practices-for-encryption"></a>加密的最佳作法
* IaaS VM︰使用 Azure 磁碟加密。 開啟儲存體服務加密，來加密在 Azure 儲存體中用來備份這些磁碟的 VHD 檔案，但它只會加密新寫入的資料。 這表示，如果您建立 VM，然後在保留 VHD 檔案的儲存體帳戶上啟用儲存體服務加密，則只會加密變更，而不會加密原始的 VHD 檔案。
* 用戶端加密：這是加密資料的最安全方法，因為它會在傳輸前加密資料，並加密待用資料。 不過，它需要您使用儲存體將程式碼加入應用程式，而您可能不想這樣做。 在這些情況下，您可以針對傳輸中的資料使用 HTTPS，以及儲存體服務加密來加密待用資料。 用戶端加密也會在用戶端上產生更多負載，您必須在延展性計畫中考慮到這一點，特別是如果您要加密並傳輸許多資料。

### <a name="protecting-customer-data-by-managing-secrets"></a>管理密碼以保護客戶資料
安全金鑰管理對於雲端保護資料十分重要。 客戶應努力簡化金鑰管理，並持續掌控雲端應用程式和服務用來加密資料的金鑰。

#### <a name="best-practices-for-managing-secrets"></a>管理密碼的最佳作法
* 使用金鑰保存庫將可能透過硬式編碼組態檔、指令碼或原始程式碼公開密碼的風險降到最低。 Azure 金鑰保存庫會加密金鑰 (例如 Azure 磁碟加密的加密金鑰) 和機密資料 (例如密碼)，方法是將它們儲存在通過 FIPS 140-2 Level 2 驗證的硬體安全性模組 (HSM) 中。 為了加強保證，您可以在這些 HSM 中匯入或產生金鑰。
* 應用程式的程式碼和範本只應包含密碼的 URI 參考 (也就是說，實際的密碼不在程式碼、組態或原始程式碼儲存機制中)。 這可防止內部或外部儲存機制的金鑰網路釣魚攻擊，例如 GitHub 中的 harvest-bots。
* 在金鑰保存庫內使用強式 RBAC 控制。 如果受信任的操作員離職或轉調到公司內的新群組，則應該防止讓他們能夠存取機密資料。

如需詳細資訊，請參閱 [Azure 金鑰保存庫公用文件](../key-vault/index.md)。

### <a name="isolation-to-restrict-data-access"></a>隔離以限制資料存取
隔離的關鍵在於使用界限、分割和容器，以限制只有經過授權的使用者、服務和應用程式能夠存取資料。 例如，租用戶之間的分隔是多租用戶雲端平台 (例如 Microsoft Azure) 的基本安全性機制。 邏輯隔離有助於防止租用戶干擾任何其他租用戶的作業。

#### <a name="environment-isolation"></a>環境隔離
Azure Government 環境是與 Microsoft 其他網路隔開的實體執行個體。 這可透過一系列的實體和邏輯控制來達成，包括下列各項：

* 使用生物識別裝置與相機來確保實體屏障。
* 需要以邏輯方式存取生產環境的 Microsoft 人員需使用特定認證和多重要素驗證。
* Azure Government 的所有服務基礎結構都位於美國境內。

#### <a name="per-customer-isolation"></a>每個客戶隔離
Azure 會透過 VLAN 隔離、ACL、負載平衡器和 IP 篩選器，實作網路存取控制和隔離

客戶可進一步隔離其在訂用帳戶、資源群組、虛擬網路和子網路的資源。

## <a name="screening"></a>篩選
最新公佈的 FedRAMP 高影響等級和美國國防部 (DoD) 影響等級 4 的資格鑑定。 這使得整個 Azure Government 環境的安全性和法規遵循標準，提升到全新的高度。

我們現在會依照《美國國防部雲端運算安全性需求指南》(SRG) 第 5.6.2.2 節中所定義的規定，以全國性機關法律與信用調查 (NACLC) 篩選所有作業人員︰

> [!NOTE]
> 可以存取「非關鍵敏感」(例如 DoD 的 ADP-2) 所界定的等級 4 和 5 資訊的 CSP 人員 (「非關鍵敏感」承包商)，所需的最基本背景調查，是全國性機關法律與信用調查 (NACLC)；而中度風險職位所需的則是中度風險背景調查 (MBI)。
> 
> 

下表彙總了目前對 Azure Government 作業人員所進行的篩選︰

| Azure Government 篩選與背景調查 | 說明 |
| --- | --- |
| 美國公民身分 |美國公民身分的驗證。 |
| Microsoft Cloud 背景調查 (每兩年) |社會安全號碼搜尋、犯罪記錄檢查、美國財政部外國資產管制局 (OFAC) 名單、美國商務部產業安全局 (BIS) 名單、美國國務院國防貿易管制辦公室拒絕人員名單。 |
| 全國性機關法律與信用調查 (NACLC) (每五年) |新增比對 FBI 資料庫的指紋背景檢查。 如需詳細資訊，請前往<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/">美國人事管理局網站</a>。 |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS">美國刑事司法資訊服務 (CJIS)</a> |CJIS 是州、地方和 FBI 政府所進行的篩選，對於可能會具有重要刑事司法資訊 (CJI) 資料存取權限的作業人員，會處理其指紋記錄並確認其犯罪記錄。  每個州政府對所有可能存取 CJI 的員工，都會進行各自的背景調查及後續的核准。 |

Azure 作業人員適用下列存取原則︰

* 職責明確，對變更的要求、核准及部署具有個別責任。
* 會透過已定義而具有特定功能的介面來進行存取。
* 以 Just-In-Time (JIT) 方式存取，只有發生事件或特定維護事件時，才會授與存取權，而且一律只能在限制的期間內存取。
* 存取是基於規則，具有已定義的角色，只會指派進行疑難排解所需的存取權限。

篩選標準包括對於所有 Microsoft 支援服務和作業人員，進行其美國公民身分的驗證，通過之後才會授與 Azure Government 託管系統的存取權。 需要傳輸資料的支援人員，會使用 Azure Government 中的安全功能。 安全的資料傳輸，會需要一組個別的驗證認證才能存取。 例如，若要存取系統中繼資料，作業人員會使用特定的 Web 型內部管理工具、唯讀 API 和 JIT 提高權限。

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>


