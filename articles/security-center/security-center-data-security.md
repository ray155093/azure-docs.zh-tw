---
title: "Azure 資訊安全中心資料安全性 | Microsoft Docs"
description: "本文件說明如何在 Azure 資訊安全中心管理和保護資料。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0
ms.lasthandoff: 02/07/2017


---
# <a name="azure-security-center-data-security"></a>Azure 資訊安全中心資料安全性
為了協助客戶防範、偵測和回應威脅，Azure 資訊安全中心會收集和處理安全性的相關資料，包括組態資訊、中繼資料、事件記錄檔、損毀傾印檔等等。 Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

本文說明如何在 Azure 資訊安全中心管理和保護資料。


## <a name="data-sources"></a>資料來源
Azure 資訊安全中心會分析來自下列來源的資料，以掌握您的安全性狀態、識別弱點並提供建議防護功能，並偵測作用中的威脅︰

- Azure 服務︰與 Azure 服務的資源提供者通訊，以使用您所部署之 Azure 服務的組態相關資訊。
- 網路流量︰使用從 Microsoft 的基礎結構取樣的網路流量中繼資料，例如來源/目的地 IP/連接埠、封包大小和網路通訊協定。
- 合作夥伴解決方案：使用整合式合作夥伴解決方案 (例如防火牆和反惡意程式碼解決方案) 的安全性警示。
- 您的虛擬機器：使用組態資訊以及安全性事件的相關資訊，例如來自您虛擬機器的 Windows 事件和稽核記錄檔、IIS 記錄檔、syslog 訊息和損毀傾印檔。


## <a name="data-protection"></a>資料保護
**資料隔離**：資料會以邏輯方式分開保存在服務的每個元件上。 每個組織加上標記的所有資料。 這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。

**資料存取**︰ 為了提供安全性建議和調查潛在的安全性威脅，Microsoft 人員可能會存取 Azure 服務所收集或分析的資訊，包含損毀傾印檔案、處理建立事件、VM 磁碟快照集和構件，這些資訊可能會在無意中包含客戶資料或您虛擬機器中的個人資料。 我們會遵守 [Microsoft Online Services Terms 和隱私權聲明](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，其中陳述 Microsoft 不會使用客戶資料或從中衍生資訊作為任何廣告或類似的商業用途。 我們會視需要只使用客戶資料為您提供 Azure 服務，包括與提供這些服務相容的用途。 您可保有客戶資料的所有權限。

**資料使用**：Microsoft 使用可見於多個租用戶的模式和威脅智慧來加強我們的防護和偵測功能；我們會根據[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)中所述的隱私權承諾進行。

## <a name="data-location"></a>資料位置
**儲存體帳戶**︰針對執行虛擬機器的每個區域指定儲存體帳戶。 這可讓您將資料儲存在與從中收集資料的虛擬機器相同的區域中。 此資料 (包括損毀傾印檔案) 將會持續儲存在儲存體帳戶中。 VM 磁碟快照集會儲存在與 VM 磁碟相同的同一個儲存體帳戶中。

**Azure 資訊安全中心儲存體**：安全性警示的相關資訊 (包含合作夥伴警示、建議和安全性健康狀態) 會集中儲存 (目前位於美國)。 此資訊可能包括視需要從您的虛擬機器收集的相關組態資訊和安全性事件，以提供給您安全性警示、建議或安全性健康狀態。

Azure 資訊安全中心會收集損毀傾印檔案的暫時複本並加以分析，以取得惡意探索嘗試和成功入侵的證明。 Azure 資訊安全中心會在與工作區相同的地理區域內執行這項分析，並且在分析完成時刪除暫複本。

機器構件會集中儲存在與 VM 相同的區域。


## <a name="managing-data-collection-from-virtual-machines"></a>從虛擬機器管理資料收集
當您選擇啟用 Azure 資訊安全中心時，已針對每個訂用帳戶開啟資料收集。 您可以在 Azure 資訊安全中心的 [安全性原則] 區段中開啟資料收集。 開啟資料收集後，Azure 資訊安全中心會在所有現有支援的虛擬機器和任何新建立的虛擬機器上佈建 Azure 監視代理程式。 「Azure 安全性監視」擴充功能會掃描各種安全性相關設定，並將其轉換成 [Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的追蹤事件。 此外，作業系統會在執行機器的過程中引發事件記錄檔事件。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄檔 (Windows 事件記錄檔)、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。 「Azure 監視代理程式」會讀取事件記錄項目和 ETW 追蹤，並將它們複製到您的儲存體帳戶進行分析。

您可以隨時停用從虛擬機器收集資料，這將會移除 Azure 資訊安全中心先前所安裝的任何監視代理程式。 即使已停用資料集合，仍然會啟用VM 磁碟快照集和構件集合。


## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何在 Azure 資訊安全中心管理和保護資料。 若要深入了解 Azure 資訊安全中心，請參閱：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md) — 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 尋找有關 Azure 安全性與相容性的部落格文章。

