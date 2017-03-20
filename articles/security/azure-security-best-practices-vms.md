---
title: "Azure 虛擬機器安全性最佳作法 | Microsoft Docs"
description: "本文提供一組可在位於 Azure 的虛擬機器中使用的安全性最佳作法。"
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Azure 虛擬機器安全性最佳作法

在大部分的 IaaS (基礎結構即服務) 案例中，[虛擬機器](https://docs.microsoft.com/en-us/azure/virtual-machines/)對於使用雲端運算的組織而言是主要的工作負載。 在組織想要慢慢地將工作負載移轉至雲端的[混合式案例](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)中，這非常重要。 您應該遵循[適用於 IaaS 的一般安全性考量 (英文)](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) 案例，並確定已將安全性最佳作法套用到您位於 Azure 中的所有 VM。

本文將討論一組 Azure VM 安全性最佳作法。 這些最佳作法衍生自我們使用 Azure VM 的體驗以及客戶的體驗。 

針對每個最佳作法，我們會說明︰

- 最佳作法是什麼
- 您為何想要啟用該最佳作法
- 如果無法啟用最佳作法，結果可能為何
- 最佳作法的可能替代方案
- 如何學習啟用最佳作法

＜Azure 虛擬機器安全性最佳作法＞這篇文章是以共識意見及 Azure 平台功能和特性集 (因為它們在撰寫本文時已存在) 為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure VM 安全性最佳作法包括：

- 虛擬機器驗證和存取控制
- 虛擬機器可用性和網路存取
- 強制執行加密以保護 Azure VM 中待用資料的安全
- 管理虛擬機器更新
- 管理虛擬機器安全狀態
- 監視虛擬機器效能

## <a name="virtual-machine-authentication-and-access-control"></a>虛擬機器驗證和存取控制

保護虛擬機器的第一個步驟是確保只有已獲授權的使用者能夠佈建新的 VM。 您可以使用 [Resource Manager 原則](../azure-resource-manager/resource-manager-policy.md)來建立組織中資源的慣例、建立自訂原則，並將這些原則套用到資源，例如[資源群組](../azure-resource-manager/resource-group-overview.md)。 屬於該資源群組的 VM 將繼承這些原則。 儘管建議您使用這個方法來管理具有不同需求且且位於不同資源群組的資源 (包括 VM)，但您也可以使用[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 來控制對 VM 的個別存取。

您可以啟用 Azure Resource Manager 原則和 RBAC 來控制 VM 存取，藉以增強 VM 的整體安全性。 建議您將關係密切且共用相同生命週期的 VM 置於同一個資源群組。 資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組列出帳單成本。 使用[最低權限](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)方法以啟用使用者的存取來佈建 VM，並規劃在為使用者指派權限時，於 Azure 中使用下列內建角色：

- [虛擬機器參與者](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)：可以管理虛擬機器，但無法管理他們連接的虛擬網路或儲存體帳戶。
- [傳統虛擬機器參與者](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor)：可以管理傳統的虛擬機器，但無法管理他們連接的虛擬網路或儲存體帳戶。
- [安全性管理員](../active-directory/role-based-access-built-in-roles.md#security-manager)：可以管理安全性元件、安全性原則及虛擬機器。
- [DevTest Labs 使用者](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user)：可以檢視所有項目，並連接、啟動、重新啟動和關閉虛擬機器。

請勿讓系統管理員共用帳戶和密碼，或在多個使用者帳戶或服務上重複使用密碼，特別是用於社交媒體或其他非系統管理活動的帳戶或服務。 在理想情況下，您應該使用 [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 範本，安全佈建您的 VM。 您可以藉由使用這種方法，來強化部署選項，並強制執行整個部署的安全性設定。

未利用諸如 RBAC 等功能來強制執行資料存取控制的組織，可能會對其使用者提供超過所需的權限。 一開始就能存取您不應具備的某些資料層級，就會直接導致資料洩漏。
 

## <a name="virtual-machine-availability-and-network-access"></a>虛擬機器可用性和網路存取

如果您的 VM 會執行需要具備高可用性的重要應用程式，則強烈建議您使用多個 VM。  如需更佳的可用性，請在[可用性設定組](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md)中至少建立兩個 VM。 Azure [負載平衡器](../load-balancer/load-balancer-overview.md)也會要求已負載平衡的 VM 屬於同一個可用性設定組。 如果需要從網際網路存取這些 VM，您必須設定[網際網路面向的負載平衡器](../load-balancer/load-balancer-internet-overview.md)。

將 VM 公開至網際網路時，務必確定您會[使用網路安全性群組來控制網路流量](../virtual-network/virtual-networks-nsg.md)。  由於 NSG 可以套用至子網路，依子網路群組您的資源並將 NSG 套用至子網路，即可減少 NSG 的數量。 目的是要建立網路隔離層，您可以藉由在 Azure 中適當設定[網路安全性](../best-practices-network-security.md)功能來完成此動作。  

您也可以從 Azure 資訊安全中心使用 Just In Time VM 存取功能，來控制人員以及某人可遠端存取特定 VM 多久的時間。 如需如何使用此功能的詳細資訊，請觀看以下影片：


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

未對網際網路面向的 VM 強制執行網路存取限制的組織會暴露於安全性風險中，例如 RDP 暴力密碼破解攻擊。 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>強制執行加密以保護 Azure VM 中待用資料的安全

現今，[待用資料加密 (英文)](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) 是達到資料隱私性、合規性及資料主權的必要步驟。 [Azure 磁碟加密](../security/azure-security-disk-encryption.md)可讓 IT 系統管理員加密 Windows 和 Linux IaaS 虛擬機器 (VM) 磁碟。 Azure 磁碟加密利用 Windows 的業界標準 BitLocker 功能和 Linux 的 DM-Crypt 功能，為 OS 和資料磁碟提供磁碟區加密。

您可以利用 Azure 磁碟加密來協助保護資料安全，以符合您的組織安全性和法規遵循承諾。 組織也應該考慮使用加密，協助降低與未經授權存取資料相關的風險。 此外，也建議您在將敏感性資料寫入磁碟機之前，先將磁碟機加密。 

確定會將 VM 的資料磁碟區加密，以保護您 Azure 儲存體帳戶中待用資料磁碟區的安全。 利用 [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/) 來保護加密金鑰和密碼。 

未強制執行資料加密的組織受資料完整性問題影響的程度更大，例如惡意或粗暴使用者竊取資料與入侵帳戶，且未經授權存取單純格式的資料。 除了這些風險，必須遵守業界法規的公司必須證明他們是十分用心，並使用正確的安全性控制項來增強資料安全性。

若要深入了解 Azure 磁碟加密，請閱讀[適用於 Windows 和 Linux IaaS VM 的 Azure 磁碟加密](azure-security-disk-encryption.md)一文。


## <a name="manage-virtual-machine-updates"></a>管理虛擬機器更新

Azure 不會將 Windows Update 推送至 Microsoft Azure 虛擬機器，因為這些機器預計是由使用者管理。 這就像任何內部部署機器一樣。 不過，建議客戶讓自動安裝 Windows Update 設定保持啟用狀態。 另一個選項是在其他 Azure 虛擬機器或內部部署上部署 [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 伺服器或其他適當的更新管理產品。 WSUS 和 Windows Update 都會讓 VM 保持最新狀態。 此外，建議使用掃描產品來確認所有的 IaaS 虛擬機器都是最新狀態。

Azure 提供的原廠映像會定期更新，以包含最新一輪的 Windows Update。 不過，不保證映像在部署期間是最新的。 比起公開發行版本，可能會些微延遲 (不會超過數週)。 檢查並安裝所有 Windows Update，應該是每個部署的第一個步驟。 在部署您提供的映像或從您自己的程式庫進行部署時，請務必記住這一點。 做為 Microsoft Azure 資源庫一部分提供的映像，預設一律會啟用自動安裝 Windows Update。

未強制執行軟體更新原則的組織，受會攻擊已經修正之已知弱點的威脅影響的程度更大。 除了這些風險，必須遵守業界法規的公司必須證明他們是十分用心，並使用正確的安全性控制項來增強他們位於雲端上的工作負載安全性。
重要的是要強調傳統資料中心與 Azure IaaS 之間的軟體更新最佳作法有許多相似之處，因此建議您評估目前的軟體更新原則，以包含 Azure VM。

## <a name="manage-virtual-machine-secure-posture"></a>管理虛擬機器安全狀態

網路威脅日新月異，因此保護您的 VM 需要更豐富的監視功能，以便能夠快速偵測到威脅、觸發警示，以及減少誤判。 這種類型工作負載的安全狀態包含 VM 的所有安全性層面，範圍可從更新管理到安全網路存取，同時還能主動監視嘗試取得未經授權的權限來存取您的資源的威脅。

您可以使用 [Azure 資訊安全中心](../security-center/security-center-intro.md)，來監視 [Windows](../security-center/security-center-virtual-machine.md) 和 [Linux VM](../security-center/security-center-linux-virtual-machine.md) 的安全狀態。 您可以利用 Azure 資訊安全中心的下列功能來監視 VM：

- 具有建議設定規則的作業系統 (OS) 安全性設定
- 系統安全性和重大更新遺失
- 端點保護建議 (反惡意程式碼)
- 磁碟加密驗證
- 弱點評估和補救
- 威脅偵測

資訊安全中心可以主動監視威脅，並將這些威脅公開於 [安全性警示] 之下。 相互關聯的威脅將彙總於稱為「安全性事件」的單一檢視中。 您可以觀賞下列影片，以了解資訊安全中心如何在您位於 Azure 的 VM 中協助您識別潛在的威脅。

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

未針對他們的 VM 強制執行安全狀態的組織，不會察覺可能會適當避開安全性控制項的嘗試。

## <a name="monitoring-virtual-machine-performance"></a>監視虛擬機器效能

若您在 VM 中的處理序會比它在雲端中消耗更多資源，則資源濫用也會造成問題。 具效能問題的虛擬機器會導致服務中斷，這違背了其中一個安全性主體：可用性。 因此，請務必了解，不只需要以反應性方式監視 VM 存取 (發生問題時)，還必須在正常作業期間內完成基準。

[Azure 診斷記錄 (英文)](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) 可協助您監視虛擬機器的資源，並識別可能影響它的效能和可用性的問題。 Azure 診斷擴充功能會在以 Windows 為基礎的 Azure 虛擬機器上提供監視和診斷功能。 您可以將此擴充功能納入為 Azure Resource Manager [範本](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)的一部分，藉以在虛擬機器上啟用這些功能。 您也可以使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-metrics.md)來查看您的資源健康狀態。

未監視虛擬機器效能的組織，將無法判斷效能模式中的特定變更是否屬於它的正常使用情況，或者，是否正發生異常作業 (因為它耗用的資源比平常更多)。 異常狀況可能表示來自外部資源的潛在攻擊，或是在此虛擬機器中執行的入侵程序。 
