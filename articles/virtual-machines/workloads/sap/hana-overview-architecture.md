---
title: "SAP HANA on Azure (大型執行個體) 的概觀和架構 | Microsoft Docs"
description: "如何部署 SAP HANA on Azure (大型執行個體) 的架構概觀。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 758c9112539ebaedc6c8180b7e6642d5d26fa16d
ms.lasthandoff: 04/12/2017


---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Azure 上 SAP HANA (大型執行個體) 的概觀和架構 
這是一份由五個部分構成的架構和技術部署指南，提供可協助您在 Azure 中的新 SAP HANA on Azure (大型執行個體) 上部署 SAP 的資訊。 本指南並不完整，其中並未包含有關設定 SAP 解決方案的特定詳細資料。 取而代之的是，提供了寶貴的資訊來協助您進行初次部署和後續的作業。 請勿使用本指南來取代與 SAP HANA 安裝相關的 SAP 文件 (或許多涵蓋該主題的＜SAP 支援附註＞)。 本指南也提供有關安裝 SAP HANA on Azure (大型執行個體) 的詳細資料。


本指南的五個部分涵蓋下列主題：

- [概觀和架構](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [基礎結構和連線能力](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA 安裝](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [疑難排解和監視](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定義

《架構和技術部署指南》中廣泛使用數個常見的定義。 請注意下列詞彙及其意義：

- **IaaS：**基礎結構即服務
- **PaaS：**平台即服務
- **SaaS：**軟體即服務
- **SAP 元件︰**個別的 SAP 應用程式，例如 ECC、BW、Solution Manager 或 EP。 SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
- **SAP 環境 (SAP Environment)︰**一或多個以邏輯方式分組的 SAP 元件，可執行像是開發、QAS、訓練、DR 或生產等商務功能。
- **SAP 架構 (SAP Landscape)︰**這是指您 IT 架構中的整個 SAP 資產。 SAP 架構包含所有生產和非生產的環境。
- **SAP 系統︰**SAP ERP 開發系統、SAP BW 測試系統、SAP CRM 生產系統等的 DBMS 層與應用程式層的組合。Azure 部署不支援在內部部署與 Azure 之間分割這兩個層。 這意謂著 SAP 系統不是在內部部署就是在 Azure 部署。 不過，您可以將 SAP 環境的不同系統部署到 Azure 或內部部署。 例如，您可以在 Azure 部署 SAP CRM 開發和測試系統，而在內部部署 SAP CRM 生產系統。 就 SAP HANA on Azure (大型執行個體) 來說，支援在 Azure VM 中執行 SAP 系統的 SAP 應用程式層，並在「大型執行個體」戳記中的某個單位上執行 HANA 執行個體。
- **大型執行個體戳記︰**經 SAP HANA TDI 認證並專門用來執行 Azure 內 SAP HANA 執行個體的硬體基礎結構堆疊。
- **SAP HANA on Azure (大型執行個體)：**Azure 中產品方案的正式名稱，此產品方案可在經 SAP HANA TDI 認證並部署在不同 Azure 區域之「大型執行個體」戳記中的硬體上執行 HANA 執行個體。 「HANA 大型執行個體」這個相關詞彙是 SAP HANA on Azure (大型執行個體) 的簡稱，並在本技術部署指南中廣泛使用。
- **跨單位：**描述將 VM 部署到 Azure 訂用帳戶的案例，該訂用帳戶在內部部署資料中心與 Azure 之間具有站對站、多站台或 ExpressRoute 連線能力。 在一般 Azure 文件中，這類部署也會描述為「跨單位」案例。 連線的原因是為了將內部部署網域、內部部署 Active Directory/OpenLDAP 和內部部署 DNS 延伸到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 在此擴充下，VM 可以是內部部署網域的一部分。 內部部署網域的網域使用者可以存取伺服器，並可在這些 VM 上執行服務 (例如 DBMS 服務)， 但無法在內部部署的 VM 和 Azure 部署的 VM 之間進行通訊和名稱解析。 這是部署大部分 SAP 資產時將會有的典型案例。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 Azure 入口網站建立具有網站間連線的 VNet](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

針對在 Microsoft Azure 公用雲端上部署 SAP 工作負載的主題，已發佈了各種不同的額外資源。 強烈建議所有規劃 Azure 中之 SAP HANA 部署的人都需有經驗，並且熟悉 Azure IaaS 的主體及 Azure IaaS 上的 SAP 工作負載部署。 下列資源提供詳細資訊，應在繼續進行之前先參考：


- [在 Microsoft Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>認證

除了 NetWeaver 認證之外，SAP 還需要特殊的 SAP HANA 認證，才能在特定的基礎結構 (例如 Azure IaaS) 上支援 SAP HANA。

NetWeaver (以及就某種程度而言 SAP HANA 認證) 的相關核心 SAP 附註是 [SAP 附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。

這個 [SAP 附註 #2316233 - SAP HANA on Microsoft Azure (大型執行個體)](https://launchpad.support.sap.com/#/notes/2316233/E) 也很重要。 它涵蓋本指南中所述的解決方案。 此外，也支援您在 GS5 VM 類型的 Azure 中執行 SAP HANA。 [此案例的資訊發佈在 SAP 網站上](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP 附註 #2316233 中提及的 SAP HANA on Azure (大型執行個體) 解決方案讓 Microsoft 和 SAP 客戶能夠在 Azure 中部署大型 SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA 或其他 SAP HANA 工作負載。 這是以經 SAP-HANA 認證的專用硬體戳記 ([SAP HANA Tailored Datacenter Integration - TDI](https://scn.sap.com/docs/DOC-63140)) 為基礎。 以 SAP TDI 設定的解決方案方式執行可讓您確知所有 SAP HANA 型應用程式 (包括 SAP Business Suite on SAP HANA、SAP Business Warehouse (BW) on SAP HANA、S4/HANA 及 BW4/HANA) 都將能夠運作。

與在「Azure 虛擬機器」中執行 SAP HANA 相比，此解決方案有一個優點 - 它提供更大的記憶體磁碟區。 如果您想要使此解決方案能夠執行，需了解一些重要層面：

- SAP 應用程式層和非 SAP 應用程式是在裝載於一般 Azure 硬體戳記中的「Azure 虛擬機器」(VM) 中執行。
- 客戶內部部署基礎結構、資料中心及應用程式部署會透過 Azure ExpressRoute (建議使用) 或「虛擬私人網路」(VPN) 連接到 Microsoft Azure 雲端平台。 Active Directory (AD) 和 DNS 也會延伸到 Azure。
- 用於 HANA 工作負載的 SAP HANA 資料庫執行個體會在 SAP HANA on Azure (大型執行個體) 上執行。 「大型執行個體」戳記會連接到 Azure 網路中，讓在 Azure VM 中執行的軟體能夠與在「HANA 大型執行個體」中執行的 HANA 執行個體互動。
- SAP HANA on Azure (大型執行個體) 的硬體是已預先安裝 SUSE Linux Enterprise Server 或 Red Hat Enterprise Linux 的「基礎結構即服務」(IaaS) 中所提供的專用硬體。 與使用「Azure 虛擬機器」一樣，進一步的作業系統更新和維護是由您負責。
- 不論是安裝 HANA 或任何在 HANA 大型執行個體的單位上執行 SAP HANA 所需的額外元件，還是 SAP HANA on Azure 的所有個別進行中作業和管理，都是由您負責。
- 除了這裡所述的解決方案之外，您也可以在連接到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶中安裝其他元件。  例如，可啟用與 SAP HANA 資料庫 (跳板伺服器、RDP 伺服器、SAP HANA Studio、適用於 SAP BI 案例的 SAP Data Services，或網路監視解決方案) 之通訊和/或直接通訊功能的元件。
- 與在 Azure 中一樣，「HANA 大型執行個體」也提供支援「高可用性」和「災害復原」的功能。

## <a name="architecture"></a>架構

簡要說來，SAP HANA on Azure (大型執行個體) 解決方案是將 SAP 應用程式層放在 Azure VM 中，而將資料庫層放在 SAP TDI 設定的硬體上，此硬體是位於相同 Azure 區域中連接到 Azure IaaS 的「大型執行個體」戳記中。

> [!NOTE]
> SAP 應用程式層必須部署在與 SAP DBMS 層相同的 Azure 區域中。 這在已發佈的 Azure 上 SAP 工作負載的相關資訊中有詳細記載。

SAP HANA on Azure (大型執行個體) 的整體架構不僅提供一個經 SAP TDI 認證的硬體組態 (適用於 SAP HANA 資料庫的非虛擬化、裸機、高效能伺服器)，還提供 Azure 的能力與彈性來調整 SAP 應用程式層的資源以符合您的需求。

![SAP HANA on Azure (大型執行個體) 的架構概觀](./media/hana-overview-architecture/image1-architecture.png)

上述架構分成三個部分︰

- **右邊：**一個執行資料中心內不同應用程式的內部部署基礎結構，其中使用者會存取 LOB 應用程式 (例如 SAP)。 在理想的情況下，這個內部部署基礎結構會接著以 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 連接到 Azure。

- **中間：**顯示 Azure IaaS，在此案例中是顯示使用 Azure VM 來裝載 SAP 或其他利用 SAP HANA 作為 DBMS 系統的應用程式。 以 Azure VM 所提供的記憶體運作的較小 HANA 執行個體會與其應用程式層一起部署在 Azure VM 中。 深入了解[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)。
<br />「Azure 網路」可用來將 SAP 系統及其他應用程式一起群集到「Azure 虛擬網路」(VNet) 中。 這些 VNet 既會連接到 SAP HANA on Azure (大型執行個體)，也會連接到內部部署系統。
<br />針對受支援在 Microsoft Azure 中執行的 SAP NetWeaver 應用程式和資料庫，請參閱 [SAP 支援附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)。 如需有關在 Azure 上部署 SAP 解決方案的文件，請檢閱：

  -  [在 Windows 虛擬機器 (VM) 上使用 SAP](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [在 Microsoft Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左邊：**顯示「Azure 大型執行個體」戳記中經 SAP HANA TDI 認證的硬體。 「HANA 大型執行個體」單位是使用與從內部部署環境連線到 Azure 時相同的技術來連線到您訂用帳戶的 Azure VNet。

「Azure 大型執行個體」戳記本身結合了下列元件：

- **運算：**以 Intel Xeon E7-8890v3 處理器為基礎的伺服器，提供必要的運算能力且經 SAP HANA 認證。
- **網路：**將運算、儲存體及 LAN 元件互連的整合式高速網路網狀架構。
- **儲存體：**可透過整合式網路網狀架構存取的儲存體基礎結構。 根據所要部署的特定 SAP HANA on Azure (大型執行個體) 組態，會提供特定的儲存體容量 (若要使用更多儲存體容量，則需每月額外付費)。

在「大型執行個體」戳記的多租用戶基礎結構內，是將客戶部署成隔離的租用戶。 這些租用戶與 Azure 訂用帳戶具有一對一關係。 這意謂著您無法存取在來自兩個不同 Azure 訂用帳戶之「Azure 大型執行個體」戳記中執行的相同 SAP HANA on Azure (大型執行個體)。

與 Azure VM 一樣，在多個 Azure 區域中都有提供 SAP HANA on Azure (大型執行個體)。 為了提供「災害復原」功能，您可以選擇加入。 各個 Azure 區域中的不同「大型執行個體」戳記會彼此互連。

就像使用「Azure 虛擬機器」時您可以在不同的 VM 類型之間做選擇一樣，您也可以從針對不同 SAP HANA 工作負載類型量身打造的不同「HANA 大型執行個體」SKU 中做選擇。 SAP 會根據 Intel 處理器世代，為不同的工作負載套用記憶體與處理器插槽比例 - 提供四種不同的 SKU 類型：

截至 2016 年 12 月為止，在美國西部和美國東部的 Azure 區域中，SAP HANA on Azure (大型執行個體) 有六個組態︰

| SAP 解決方案 | CPU | RAM | 儲存體 |
| --- | --- | --- | --- |
| 已針對 OLAP 最佳化：SAP BW、BW/4HANA<br /> 或 SAP HANA (一般 OLAP 工作負載) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3 |  768 GB |  3 TB |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3 |  1.5 TB |  6 TB |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4 |  2.0 TB |  8 TB |
| 已針對 OLTP 最佳化：SAP Business Suite<br /> on SAP HANA 或 S/4HANA (OLTP)<br /> (一般 OLTP) | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3 |  1.5 TB |  6 TB |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3 |  3.0 TB |  12 TB |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4 |  4.0 TB |  16 TB |

上述各種不同組態在 [SAP 附註 #2316233 - SAP HANA on Microsoft Azure (大型執行個體)](https://launchpad.support.sap.com/#/notes/2316233/E) 中均有提到。

選擇的特定組態取決於工作負載、CPU 資源及所需的記憶體。 OLTP 工作負載可以利用已針對 OLAP 工作負載最佳化的 SKU。 同樣地，您也可以利用適用於 OLAP HANA 工作負載的 OLTP SKU。 不過，您可能需要限制 HANA 所利用的記憶體，使其符合已認證的 Intel E7 處理器世代記憶體大小，如 [SAP 網站上所列](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html)適用於 Scale up BW on HANA (相應增加 BW on HANA 規模) 設備類型的記憶體大小。

請務必注意「大型執行個體」戳記的整個基礎結構並非僅配置給單一客戶使用。 這也適用於 SAP HANA on Azure (大型執行個體)，就像對透過 Azure 中所部署的網路網狀架構連接的計算與儲存資源機架進行配置時一樣。 「HANA 大型執行個體」基礎結構 (例如 Azure) 會部署透過網路隔離來彼此隔離的不同客戶 &quot;租用戶&quot;。 因此，這些「HANA 大型執行個體」部署既不會互相干擾，彼此也看不到對方。 系統會將「大型執行個體」戳記中部署的租用戶指派給一個 Azure 訂用帳戶。 如果您有第二個也需要「HANA 大型執行個體」的 Azure 訂用帳戶，系統將以在網路中完全隔離的方式將該執行個體部署在「大型執行個體」戳記中的某個個別租用戶內，以防止這些執行個體之間直接通訊。

不過，在「HANA 大型執行個體」上執行 SAP HANA 與在於 Azure 中部署的 Azure VM 上執行 SAP HANA 之間有顯著的不同：

- SAP HANA on Azure (大型執行個體) 沒有虛擬化層。 您會獲得基礎裸機硬體的效能。
- 與 Azure 不同，SAP HANA on Azure (大型執行個體) 伺服器是特定客戶專用。 重新啟動或關閉伺服器並不會導致作業系統和 SAP HANA 被部署到另一部伺服器。 (唯一的例外狀況是當伺服器發生問題而必須在另一個刀鋒視窗上執行重新部署的時候)。
- 與 Azure 中配合最佳性價比來選取主機處理器類型的方式不同，為 SAP HANA on Azure (大型執行個體) 選擇的處理器類型是 Intel E7v3 處理器系列中效能最佳的類型。

將會有多個客戶在 SAP HANA on Azure (大型執行個體) 硬體上進行部署，而每個客戶都會透過在自己的 VLAN 中進行部署來彼此防護。 為了將「HANA 大型執行個體」連接到「Azure 虛擬網路」(VNet) 中，適當的網路功能元件會在 Azure VNet IP 位址範圍與硬體基礎結構內的 VLAN IP 位址空間之間執行網路位址轉譯 (NAT)。

## <a name="operations-model-and-responsibilities"></a>作業模型和職責

SAP HANA on Azure (大型執行個體) 提供的服務可與 Azure IaaS 服務合作。 您會獲得一個「HANA 大型執行個體」執行個體，其中已安裝針對 SAP HANA 最佳化的作業系統。 就像使用 Azure IaaS VM 時一樣，大多數強化 OS、安裝所需額外軟體、安裝 HANA、操作 OS 和 HANA 以及更新 OS 和 HANA 的工作都是由您負責。 Microsoft 不會強制您進行 OS 更新或 HANA 更新。

![SAP HANA on Azure (大型執行個體) 的職責](./media/hana-overview-architecture/image2-responsibilities.png)

如上圖所示，SAP HANA on Azure (大型執行個體) 是一個多租用戶的「基礎結構即服務」產品方案。 因此，大部分的職責劃分是以 OS 與基礎結構為界。 Microsoft 負責作業系統線以下的所有服務層面，而您則負責該線以上的部分，包括作業系統。 因此，您針對合規性、安全性、應用程式管理、基礎與 OS 管理可能採用的大多數目前內部部署方法都可以繼續使用。 這些系統在所有方面都會顯得它們彷彿就在您的網路中一樣。

不過，這項服務已針對 SAP HANA 最佳化，因此有些部分您需要與 Microsoft 合作，才能使用根本基礎結構功能來獲得最佳結果。

以下清單提供有關每一層及您職責的更多詳細資料：

**網路：**用於執行 SAP HANA 之「大型執行個體」戳記、其對儲存體之存取、執行個體間之連線 (用於相應放大及其他功能)、對架構之連線以及對 SAP 應用程式層裝載於「Azure 虛擬機器」的 Azure 之連線的所有內部網路。 它也包含用於「災害復原」用途複寫的「Azure 資料中心」間 WAN 連線。 所有網路都是依租用戶分割並已套用 QOS。

**儲存體：**除了用於快照的虛擬化分割儲存體之外，也包括用於 SAP HANA 伺服器所需之所有磁碟區的虛擬化分割儲存體。

**伺服器：**用以執行指派給租用戶之 SAP HANA DB 的專用實體伺服器。 它們已經過硬體虛擬化。

**SDDC：**可將資料中心當作一個軟體定義的實體來管理的管理軟體。 它可讓 Microsoft 基於延展、可用性及效能等理由將資源集中存放。

**O/S：**您所選擇在伺服器上執行的 OS (SUSE Linux 或 Red Hat Linux)。 您所提供的 OS 映像將是個別 Linux 廠商提供給 Microsoft 來執行 SAP HANA 的映像。 您需要有 Linux 廠商的訂用帳戶，才能取得特定的 SAP HANA 最佳化映像。 您的責任包括向 OS 廠商註冊映像。 自 Microsoft 移交時開始，您也要負責對 Linux 作業系統採取進一步的修補措施。 這涉及成功安裝 SAP HANA 可能需要的其他套件 (請參閱 SAP 的 HANA 安裝文件和 SAP 注意事項)，而特定 Linux 廠商的 SAP HANA 最佳化 OS 映像並未包含這些套件。 客戶的責任也包括與 OS 發生問題/最佳化相關的 OS 修補，以及與特定伺服器硬體相關的驅動程式。 或 OS 的任何安全性或功能性修補。 客戶也要負責監視和容量規劃︰

- CPU 資源耗用量
- 記憶體耗用量
- 與可用空間、IOPS 及延遲相關的磁碟區
- 「HANA 大型執行個體」與 SAP 應用程式層之間的網路磁碟區流量

「HANA 大型執行個體」的根本基礎結構提供備份和還原 OS 磁碟區的功能。 利用這項功能也是您的職責所在。

**中介軟體：**主要是「SAP HANA 執行個體」。 管理、操作及監視是您的職責。 有提供的功能可讓您使用儲存體快照來進行備份/還原及「災害復原」。 這些功能是由基礎結構所提供。 不過，您的職責還包括使用這些功能來建構「高可用性」或「災害復原」架構、利用它們，以及監視儲存體快照是否已順利執行。

**資料：**您受 SAP HANA 管理的資料，以及其他位於磁碟區或檔案共用上的資料 (例如備份檔案)。 您的職責包括監視磁碟可用空間和管理磁碟區上的內容，以及監視磁碟區和儲存體快照的備份是否已順利執行。 不過，將資料順利複寫到 DR 站台則是 Microsoft 的職責。

**應用程式：**SAP 應用程式執行個體，或就非 SAP 應用程式而言，則是這些應用程式的應用程式層。 您的職責包括部署、管理、操作及監視與 CPU 資源耗用、記憶體耗用、「Azure 儲存體」耗用及 Azure VNet 內和從 Azure VNet 到 SAP HANA on Azure (大型執行個體) 之網路頻寬耗用相關的應用程式。

**WAN：**您為工作負載建立的從內部部署到 Azure 部署的連線。 如果工作負載具任務關鍵性，請使用 Azure ExpressRoute。 此連線不是 SAP HANA on Azure (大型執行個體) 解決方案的一部分，因此您需負責設定此連線。

**封存：**您可能會偏好使用自己的方法在儲存體帳戶中封存資料複本。 這會需要管理、合規性、成本及操作。 您需負責在 Azure 上產生封存複本和備份，並以符合規範的方式儲存它們。

請參閱 [SAP HANA on Azure (大型執行個體) SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)。

## <a name="sizing"></a>調整大小

「HANA 大型執行個體」的大小調整與 HANA 的大小調整大致上沒有差別。 針對您想要就現有的和已部署的系統從其他 RDBMS 移到 HANA 的情況，SAP 提供一些可在您現有 SAP 系統上執行的報告。 如果資料庫是要移到 HANA，它們會檢查資料並計算資料表記憶體需求。 若要取得有關如何執行這些報告及如何取得其最新修補程式/版本的詳細資訊，請參閱下列 SAP 附註：

- SAP 附註 #1793345 - SAP Suite on HANA 的大小調整
- SAP 附註 #1872170 - Suite on HANA 與 S/4 HANA 大小調整報告
- SAP 附註 #2121330 - 常見問題集：SAP BW on HANA 大小調整報告
- SAP 附註 #1736976 - BW on HANA 的大小調整報告
- SAP 附註 #2296290 - 新的 BW on HANA 大小調整報告

針對嶄新的實作，可使用 SAP Quick Sizer 來計算在 HANA 上實作 SAP 軟體時的記憶體需求。

HANA 的記憶體需求會隨著資料量的成長增加，因此您會想要知道現在的記憶體耗用量，以便能夠預測未來的記憶體耗用量。 根據記憶體需求，您便可以接著將您的需求與其中一個「HANA 大型執行個體」SKU 對應。

## <a name="requirements"></a>需求

以下是執行 SAP HANA on Azure (大型執行個體) 的需求。

**Microsoft Azure：**

- 可連結到 SAP HANA on Azure (大型執行個體) 的 Azure 訂用帳戶。
- Microsoft 頂級支援合約。 如需有關在 Azure 中執行 SAP 的特定資訊，請參閱 [SAP 支援附註 #2015553 - Microsoft Azure 上的 SAP：支援的必要條件](https://launchpad.support.sap.com/#/notes/2015553)。
- 在執行 SAP 大小調整演練之後，知道您所需的 HANA 大型執行個體 SKU。

**網路連線︰**

- 內部部署環境與 Azure 之間的 Azure ExpressRoute：請務必向 ISP 訂購一條 1 Gbps 的連線，以將您的內部部署資料中心連接到 Azure

**作業系統︰**

- SUSE Linux Enterprise Server 12 for SAP Applications 的授權。

> [!NOTE] 
> Microsoft 提供的作業系統未向 SUSE 註冊，也未連接 SMT 執行個體。

- 部署在 Azure VM 上 Azure 中的 SUSE Linux Subscription Management Tool (SMT)。 這會提供可讓 SUSE 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新 (因為在「HANA 大型執行個體」資料中心內無法存取網際網路) 的功能。 
- Red Hat Enterprise Linux for SAP HANA 6.7 或 7.2 的授權。

> [!NOTE]
> Microsoft 提供的作業系統未向 Red Hat 註冊，也未連接至 Red Hat Subscription Manager 執行個體。

- 部署在 Azure VM 上 Azure 中的 Red Hat Subscription Manager。 這會提供可讓 Red Hat 註冊 SAP HANA on Azure (大型執行個體) 並對其分別進行更新 (因為從部署在「Azure 大型執行個體」戳記上的租用戶內無法直接存取網際網路) 的功能。
- 與 Linux 提供者之間簽訂的服務和支援合約 (特定 Linux 版本訂用帳戶中隱含包含)，或涵蓋所用之特定 Linux 版本且符合 SAP 準則的其他服務和支援合約。

**資料庫：**

- SAP HANA (平台或企業版) 的授權和軟體安裝元件。

**應用程式︰**

- 任何連接到 SAP HANA 及相關 SAP 支援合約之 SAP 應用程式的授權和軟體安裝元件。
- 所使用與 SAP HANA on Azure (大型執行個體) 環境及相關支援合約有關的任何非 SAP 應用程式的授權和軟體安裝元件。

**技能︰**

- Azure IaaS 及其元件的相關經驗與知識。
- 在 Azure 中部署 SAP 工作負載的相關經驗與知識。
- 合格的 SAP HANA 安裝人員。
- 可設計以 SAP HANA 為中心之「高可用性」和「災害復原」的 SAP 架構師技能。

## <a name="networking"></a>網路

「Azure 網路」的架構是成功部署 SAP 應用程式的關鍵元件。 一般而言，SAP HANA on Azure (大型執行個體) 部署具有較大的 SAP 架構，其中包含數個擁有不同資料庫大小、CPU 資源耗用量及記憶體使用量的不同 SAP 解決方案。 這些 SAP 系統中很可能只有一或兩個系統是以 SAP HANA 為基礎，因此您的 SAP 架構可能會是一個利用下列各項的混合式架構：

- 部署在內部部署環境中的 SAP 系統。 由於其大小的緣故，這些系統目前無法裝載於 Azure 中；典型的例子是在 Microsoft SQL Server (作為資料庫) 上執行、具有 &gt;100 個 CPU 和 1 TB 記憶體的生產環境 SAP ERP 系統。
- 部署在內部部署環境中的 SAP HANA 型 SAP 系統 (例如，其 SAP HANA 資料庫需要 6 TB 或更多記憶體的 SAP ERP 系統)。
- 部署在 Azure VM 中的 SAP 系統。 根據資源耗用量與記憶體需求，這些系統可以是任何可在 Azure (在 VM 上) 中順利部署之 SAP NetWeaver 型應用程式的開發、測試、沙箱或生產環境執行個體。 這些系統也可以根據資料庫，例如 SQL Server (請參閱 [SAP 支援附註 #1928533 - Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E)) 或 SAP HANA (請參閱 [SAP HANA 認證的 IaaS 平台](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html))。
- 部署在 Azure (在 VM 上) 中並利用「Azure 大型執行個體」戳記中 SAP HANA on Azure (大型執行個體) 的 SAP 應用程式伺服器。

雖然混合式 SAP 架構 (包含四個或更多個不同部署案例) 是典型架構，但 Azure 中有完整 SAP 架構的執行個體，而隨著 Microsoft Azure VM 越來越強大，在 Azure VM 上進行部署的 SAP 解決方案數目也會增加。

在部署於 Azure 中之 SAP 系統內容中的 Azure 網路並不複雜。 它是根據下列原則：

- 「Azure 虛擬網路」(VNet) 必須連接到會連接到內部部署網路的 Azure ExpressRoute 線路。
- ExpressRoute 線路通常應有 1 Gbps 或更高的頻寬。 這會允許有足夠的頻寬，可供在內部部署系統與在 Azure VM 上執行的系統之間傳輸資料 (以及從內部部署環境中的使用者連線到 Azure 系統)。
- Azure 中的所有 SAP 系統都必須在 Azure VNet 中設定妥當，才能彼此通訊。
- 裝載於內部部署環境中的 Active Directory 與 DNS 會透過 ExpressRoute 延伸到 Azure。

**建議：**在單一 Azure 訂用帳戶內將整個 SAP 架構部署在 Azure。 SAP 架構內的許多程序都需要 SAP 開發、測試及生產執行個體之間的通透 (可能較少) 網路連線，而 SAP NetWeaver 架構有許多自動作用都倚賴這個在這些不同執行個體之間的通透網路。 因此，強烈建議將整個 SAP 架構置於一個 Azure 訂用帳戶中，即使已跨多個 Azure 區域部署該架構。

SAP HANA on Azure (大型執行個體) 的相關架構與程序便是建立在上述建議的基礎上。

> [!NOTE] 
> 單一 Azure 訂用帳戶只能連結到特定 Azure 區域中某個「大型執行個體」戳記內的單一租用戶，相反地，單一「大型執行個體」戳記租用戶也只能連結到一個 Azure 訂用帳戶。

將 SAP HANA on Azure (大型執行個體) 部署在兩個不同的 Azure 區域中會導致在「大型執行個體」戳記中部署個別的租用戶。 不過，只要這些執行個體都是相同 SAP 架構的一部分，您便可以預期兩個租用戶最終都會在同一個 Azure 訂用帳戶下。

> [!IMPORTANT] 
> 使用 SAP HANA on Azure (大型執行個體) 時，僅支援「Azure 資源管理」部署。

### <a name="additional-azure-vnet-information"></a>其他 Azure VNet 資訊

為了將 Azure VNet 連接到 ExpressRoute，必須建立 Azure 閘道 (請參閱[關於 ExpressRoute 的虛擬網路閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 Azure 閘道可以與連到 Azure 外部基礎結構 (或連到「Azure 大型執行個體」戳記) 的 ExpressRoute 搭配使用，或用來在 Azure VNet 之間做連接 (請參閱[使用 PowerShell 設定 Resource Manager 的 VNet 對 VNet 連線](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 您可以將 Azure 閘道最多連接到四個不同的 ExpressRoute 連線，只要這些連線是來自不同的 MS Enterprise Exchanges (MSEE) 即可。

> [!NOTE] 
> Azure 閘道為這兩個使用案例提供的輸送量是不同的 (請參閱[關於 VPN 閘道](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 在使用 ExpressRoute 連線的情況下，VNet 閘道可達到的最大輸送量是 10 Gbps。 請記住，在位於 Azure VNet 中的 Azure VM 與內部部署系統之間複製檔案 (以單一複製串流的形式) 並不會達到不同閘道 SKU 的最大輸送量。 若要利用 VNet 閘道的全部頻寬，您必須使用多個串流，或是以單一檔案的平行串流複製不同的檔案。

當您閱讀上述文章時，請仔細注意有關不同 Azure 區域中 UltraPerformance 閘道 SKU 可用性的資訊。

### <a name="networking-for-sap-hana-on-azure"></a>SAP HANA on Azure 的網路

若要連接到 SAP HANA on Azure (大型執行個體)，必須使用 ExpressRoute 透過 Azure VNet 的 VNet 閘道，將 Azure VNet 連接到客戶的內部部署網路。 此外，還必須透過第二個 ExpressRoute 線路，將它連接到位於「Azure 大型執行個體」戳記中的「HANA 大型執行個體」。 VNet 閘道將至少有兩個 ExpressRoute 連線，這兩個連線會共用 VNet 閘道的最大頻寬。

> [!IMPORTANT] 
> 考慮到 SAP 應用程式與資料庫層之間的整體網路流量，因此僅支援使用 VNet 的 HighPerformance 或 UltraPerformance 閘道 SKU 來連接到 SAP HANA on Azure (大型執行個體)。

![連接到 SAP HANA on Azure (大型執行個體) 與內部部署環境的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>單一 SAP 系統

上面顯示的內部部署基礎結構是透過 ExpressRoute 連接到 Azure，而 ExpressRoute 線路則會連接到 Microsoft Enterprise Edge (MSEE) 路由器 (請參閱 [ExpressRoute 技術概觀](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 建立該路由之後，它會連接到 Microsoft Azure 骨幹，而讓所有 Azure 區域都可供存取。

> [!NOTE] 
> 若要在 Azure 中執行 SAP 架構，請連接到距離 SAP 架構中的 Azure 區域最近的 MSEE。 「Azure 大型執行個體」戳記是透過專用 MSEE 裝置連接，可將 Azure IaaS 中的 Azure VM 與「大型執行個體」戳記之間的網路延遲降到最低。

Azure VM (裝載 SAP 應用程式執行個體) 的 VNet 閘道會連接到該 ExpressRoute 線路，而該相同 VNet 會連接到專門用來連接到「大型執行個體」戳記的個別 MSEE 路由器。

這是一個單一 SAP 系統的簡單範例，其中 SAP 應用程式層是裝載在 Azure 中，而 SAP HANA 資料庫則是在 SAP HANA on Azure (大型執行個體) 上執行。 假設的是 VNet 閘道頻寬 2 Gbps 或 10 Gbps 並不代表瓶頸。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>多個 SAP 系統或大型 SAP 系統

如果有多個 SAP 系統或大型 SAP 系統被部署成連接到 SAP HANA on Azure (大型執行個體)，就可以合理假設 HighPerformance VNet 閘道 SKU 的輸送量可能成為瓶頸。 在此情況下，請選擇 UltraPerformance SKU (如果可用)。 不過，如果只有 HighPerformance SKU (上限為 2 Gbps) 可用，或 UltraPerformance SKU (上限為 10 Gbps) 可能不敷使用，您就必須將應用程式層分割成多個 Azure VNet。

讓網路架構更具彈性：

- 針對單一的較大 SAP 應用程式層，運用多個 Azure VNet。
- 相較於將所部署的 SAP 系統結合在相同 VNet 下的個別子網路中，為這些 SAP 系統中的每一個系統部署一個個別的 Azure VNet。

 適用於 SAP HANA on Azure (大型執行個體) 的更有彈性網路架構：

![跨多個 Azure VNet 部署 SAP 應用程式層](./media/hana-overview-architecture/image4-networking-architecture.png)

如上面所示跨多個 Azure VNet 部署 SAP 應用程式層或元件時，已產生無法避免的延遲額外負荷，其發生時機是在這些 Azure VNet 中裝載的應用程式間進行通訊時。 根據預設，在此組態中，不同 VNet 中的 Azure VM 之間的網路流量會透過 MSEE 路由器發生。 不過，自 2016 年 9 月起，已可以避免並最佳化這種情形。 若要最佳化並縮短兩個 VNet 之間的通訊延遲，做法是將相同區域內的 Azure VNet 對等互連。 即使在不同的訂用帳戶中也一樣。 如果使用 Azure VNet 對等互連，兩個不同 Azure VNet 中的 VM 之間的通訊就可以使用 Azure 網路骨幹彼此直接通訊。 因此會有相似的延遲，就好像 VM 位於相同的 VNet 中一樣。 反之，以透過 Azure VNet 閘道器連接的 IP 位址範圍來定址的流量，將會透過 VNet 的個別 VNet 閘道器而路由傳送。 您可以在 [VNet 對等互連](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)一文中取得 Azure VNet 對等互連的詳細資訊。
### <a name="minimal-deployment"></a>最基本部署

就小型 SAP 系統 (最基本部署) 而言，Azure VM 會將 SAP 應用程式層裝載在原生 Azure 中 (在單一 VNet 內)，並透過 ExpressRoute 連接到「大型執行個體」戳記。 請依照下列步驟來備妥 SAP HANA on Azure (大型執行個體) 以供使用：

- 收集與四個不同 IP 位址範圍相關的特定資訊：

  1. 要用於 ExpressRoute 線路的 P2P 連線 /29 位址範圍。
  
  2. 要用於指派 SAP HANA on Azure (大型執行個體) 所需之特定 IP 位址的 /24 (建議使用) 唯一 CIDR 區塊。
  3. 一或多個供您 Azure VNet 租用戶子網路使用的 /24 (建議使用) CIDR 區塊。 這些是 SAP 相關 Azure VM 將歸屬的客戶 Azure 訂用帳戶中的子網路；這些位址將被允許存取 SAP HANA on Azure (大型執行個體)。 應該每一子網路一個租用戶位址區塊，而如果區塊是連續的且位於相同的 VNet 中，則可以加以彙總。
  4. 一個您 VNet 閘道子網路的 /28 (如果想要 P2S 網路功能，則必須使用 /27)。

  - 前兩個範圍是所需範圍 (每一 Azure 訂用帳戶和區域各一個)。 每一 Azure VNet 至少需要項目 3 和 4 中所述的 IP 位址範圍，而如果想要 VNet 中有多個子網路/租用戶，則應該針對項目 3 指定多個範圍。
![SAP HANA on Azure (大型執行個體) 最基本部署中所需的 IP 位址範圍](./media/hana-overview-architecture/image5-ip-address-range-a.png)

  -  如果在 Azure VNet 中設定多個租用戶子網路：![具有連續位址空間的 Azure VNet IP 位址範圍](./media/hana-overview-architecture/image6-ip-address-range-b.png)

> [!IMPORTANT] 
> 上面指定的每個 IP 位址皆不得與任何其他範圍重疊；每個位址都必須是不連續的且不得為任何其他範圍的子網路。 只有項目 3 和 4 中所定義的位址應該套用到 Azure VNet，所有其他位址則是用於「大型執行個體」的連線和路由。 此外，就最佳做法而言，「位址空間」位址範圍應該與子網路範圍相符，且不含空白或未指派的空間。 如果項目 1 和 2 中所定義的範圍與為項目 3 和 4 定義的範圍之間發生重疊，Azure VNet 將不會連接到 ExpressRoute 線路。

- ExpressRoute 線路是 Microsoft 在您的 Azure 訂用帳戶與「大型執行個體」戳記之間建立的線路。
- 在「大型執行個體」戳記上建立網路租用戶。
- 設定 SAP HANA on Azure (大型執行個體) 基礎結構中的網路功能，使其接受來自您 Azure VNet 內所指定範圍的 IP 位址，以便與「HANA 大型執行個體」通訊。
- 在「大型執行個體」戳記的客戶租用戶中設定「網路位址轉譯」(NAT) (為的是將租用戶內部 IP 位址對應到租用戶為 Azure 定義的 IP 位址)。
- 依據所購買的特定 SAP HANA on Azure (大型執行個體) SKU，在租用戶網路中指定一個計算單位、配置並掛接儲存體，以及安裝作業系統 (SUSE 或 RedHat Linux)。

SAP HANA on Azure (大型執行個體) 網路架構的最基本部署：

![含 IP 位址範圍的最基本部署](./media/hana-overview-architecture/image7-minimal-deployment.png)

### <a name="routing-in-azure"></a>Azure 中的路由

SAP HANA on Azure (大型執行個體) 有兩個重要的網路路由考量：

1. SAP HANA on Azure (大型執行個體) 僅供 Azure VM 以專用 ExpressRoute 連線存取；無法從內部部署環境直接存取。 因此管理用戶端及任何需要直接存取權的應用程式 (例如在內部部署環境中執行的 SAP Solution Manager) 無法連接到 SAP HANA 資料庫。

2. SAP HANA on Azure (大型執行個體) 有一個來自已定義之 NAT 集區的已指派 IP 位址。 透過 Azure 訂用帳戶和 ExpressRoute 可以存取此 IP 位址。 由於 IP 位址是 NAT 集區的一部分，因此您將需要在環境中執行額外的網路功能設定。 如需詳細資料，請參閱「SAP HANA 安裝」的相關文章。

> [!NOTE] 
> 如果在「資料倉儲」案例中您需要連接到 SAP HANA on Azure (大型執行個體)，其中應用程式和 (或) 使用者需要連接到 SAP HANA 資料庫 (直接執行)，您就必須使用另一個網路功能元件：一個可將資料正反方向路由傳送的反向 Proxy。 例如，部署在 Azure 中作為虛擬防火牆/流量路由解決方案的附帶 Traffic Manager 的 F5 BIG-IP。

### <a name="leveraging-in-multiple-regions"></a>在多個區域中運用

除了災害復原之外，您可能還有其他理由促使您在多個 Azure 區域中部署 SAP HANA on Azure (大型執行個體)。 也許您想要從部署在這些區域之不同 VNet 中的每個 VM 存取「HANA 大型執行個體」。 由於經 NAT 處理的不同「HANA 大型執行個體」伺服器 IP 位址在傳播時不會超出 Azure VNet (這些 VNet 是透過其閘道直接連接到執行個體) 範圍，因此對上面介紹的 VNet 設計有些微變更：Azure VNet 閘道可以處理來自不同 MSEE 的四種不同 ExpressRoute 線路，而每個連接到其中一個「大型執行個體」戳記的 VNet 都可連接到另一個 Azure 區域中的「大型執行個體」戳記。

![連接到不同 Azure 區域中「Azure 大型執行個體」戳記的 Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

上圖顯示兩個區域中的不同 Azure VNet 如何連接到兩個不同的 ExpressRoute 線路，而這些線路是用來連接到該兩個 Azure 區域中的 SAP HANA on Azure (大型執行個體)。 新導入的連線是矩形的紅線。 有了這些來自 Azure VNet 的連線，在這其中一個 VNet 中執行的 VM 便可存取部署在該兩個區域中的每一個不同「HANA 大型執行個體」單位 (假設您使用相同的訂用帳戶)。 如上圖所示，這是假設您有兩條從內部部署環境連到該兩個 Azure 區域的 ExpressRoute 連線；如果是為了「災害復原」，則建議使用此做法。

> [!IMPORTANT] 
> 如果使用多個 ExpressRoute 線路，就應該在前面加上「AS 路徑」和使用「本機喜好 BGP」設定來確保流量路由正確。



