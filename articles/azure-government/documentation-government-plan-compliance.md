---
title: "Azure Government 法規遵循 | Microsoft Docs"
description: "提供 Azure Government 可用法規遵循服務的概觀"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Azure Government 法規遵循

## <a name="azure-blueprint"></a>Azure Blueprint

Azure Blueprint 程式目的是要協助代表政府為政府機關和協力廠商提供者建置的 Azure，在使用上既安全且遵循法規。 Azure Government 客戶可以運用 Azure Government 的 FedRAMP JAB 運作臨時授權 (P-ATO) 或 DoD 臨時授權 (PA)，減少客戶在以 Azure 為基礎的系統中必須負責的安全性控制項範圍。 從 Azure Government 繼承安全性控制項實作，可讓客戶將重點放在特別針對其於 IaaS、PaaS 或 SaaS 環境在 Azure 中建置的特定控制項實作。

> [!NOTE]
> 在 Azure Blueprint 的內容中，「客戶」是指直接在 Azure 內建置的組織。 Azure 客戶包含代表政府建置的第三方 ISV，或直接在 Azure 中建置的政府機關。

## <a name="blueprint-customer-responsibilities-matrix"></a>Blueprint 客戶責任矩陣

Azure Blueprint 客戶責任矩陣 (CRM) 目的是協助 Azure Government 客戶實作和記錄 Azure 內實作的系統特定安全性控制項。 CRM 明確列出 FedRAMP 和 DISA 基準的所有 [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) 安全性控制項需求，包括客戶實作需求在內。 這包括 Azure 和 Azure 客戶共同承擔責任的控制項，以及必須完全由 Azure 客戶實作的控制項。 適當時，會以更細微的控制項子需求來區分控制項，以提供更具體的指引。

CRM 格式的設計以實用為考量，且有助於專注記錄客戶實作的那部分安全性控制項。

例如，對於尋求 ATO 的系統，控制項 AC-1 會要求記錄存取控制原則和程序。 對這個控制項，關於用來管理 Azure 基礎結構與平台的存取控制機制，Microsoft 會有內部的 Azure 特定原則和程序。 客戶也必須建立自己的存取控制原則和程序，用於在 Azure 中建置的特定系統。 CRM 會記錄控制項組件 AC-1a (需要包含特定內容的原則和程序)，以及 AC-1b (需要客戶每年檢閱並更新這些文件)。

現以 Microsoft Excel 活頁簿的形式提供 FedRAMP 中等和高度基準及 DISA 雲端運算 SRG L4 與 L5 基準的 Blueprint CRM。

若要取得一份 Azure Blueprint CRM 或提供意見反應，請將電子郵件寄至 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

## <a name="blueprint-system-security-plan"></a>BluePrint 系統安全性計劃

Azure Blueprint 系統安全性計劃 (SSP) 範本是客戶為主，目的是用來開發 SSP，記錄客戶安全性控制項實作以及從 Azure 繼承的控制項。 客戶責任內的控制項包含以完整且符合規範的回應記錄控制項實作的相關指引。 Azure 繼承的各節記載如何由 Azure 代表客戶實作安全性控制項。

現已提供 FedRAMP 中等和高度基準及 DISA 雲端運算 SRG L4 與 L5 基準的 Azure Blueprint SSP。 

若要取得一份 Azure Blueprint SSP 或提供意見反應，請將電子郵件寄至 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

## <a name="azure-blueprint-implementation-guidance"></a>Azure Blueprint 實作指引

Azure Blueprint 實作指引目的是協助雲端解決方案架構設計人員和安全人員了解如何部署 Azure Government 服務和功能以實作客戶負責的 FedRAMP 和 DoD 安全性控制項子集。 提供一系列文件、工具、範本和其他資源，引導安全地部署 Azure 服務和功能。 您可以使用 Azure Resource Manager 範本[建置組塊](https://github.com/mspnp/template-building-blocks)、社群提供的 Azure [快速入門範本](https://azure.microsoft.com/resources/templates/)，或使用[客戶撰寫](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) JSON 型 Resource Manager 範本來部署 Azure 資源。 Azure 中的基本部署架構包含計算、網路及儲存體資源。 此實作指引將討論如何部署這些資源，幫助您滿足安全性控制項實作需求。

### <a name="virtual-machines"></a>虛擬機器

Azure Resource Manager 範本可以用來部署預先設定的虛擬機器，其符合美國政府與業界安全性技術實作指南 (STIG) 和安全性基準測試。 您可以使用現有的預先設定機器建立自訂 Azure 虛擬機器，或部署新的虛擬機器，以及對加入網域的電腦使用 Active Directory 套用安全性原則，或對獨立電腦使用本機群組原則物件公用程式。 Azure 虛擬機器的自訂指令碼延伸模組可以用來管理這些部署後設定工作。

Windows 虛擬機器組態包括安全性控制項實作︰

- 系統使用通知和認可 [NIST 特刊 800-53 控制項 AC-8]

  > AC-8 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據組織需求自訂，包括使用適當的系統使用讓使用者必須在登入之前確認的通知 (請參閱CIS 基準測試 2.3.7.4、2.3.7.5 小節)。*

- 本機電腦帳戶限制包括帳戶鎖定 [NIST 特刊 800-53 控制項 AC-7] 並行工作階段控制項 [NIST 特刊 800-53 控制項 AC-10]、工作階段鎖定 [NIST 特刊 800-53 控制項 AC-11]、驗證器管理 [NIST 特刊 800-53 控制項 IA-5] 及其他限制

  > AC-7 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據組織需求自訂，包括在連續的登入嘗試失敗後啟動帳戶鎖定 (請參閱CIS 基準測試 1.2.1、1.2.2、1.2.3 小節。注意：務必根據 FedRAMP 和 DoD 參數需求來變更 CIS 基準測試預設值)。*

  > AC-10 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據組織需求自訂，包括限制使用者只能存取符合 FedRAMP 和 DoD 需求的單一遠端桌面服務工作階段 (請參閱CIS 基準測試 18.9.48.3.2 小節)。*

  > AC-11 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據組織需求自訂，包括維持 900 秒 (15 分鐘) 無活動狀態後，啟動工作階段鎖定，直到使用者重新認證為止 (請參閱CIS 基準測試 2.3.7.3、19.1.3.1、19.1.3.3 小節)。*

  > IA-5 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據組織需求自訂，包括本機帳戶密碼的限制，以強制使用符合 FedRAMP 需求的最短 (1 天) 和最長 (60 天) 存留期限制、重複使用條件 (24 個密碼)、長度 (14 個字元) 及複雜度需求 (請參閱CIS 基準測試 1.1.1、1.1.2、1.1.3、1.1.4、1.1.5 小節。注意：務必根據 DoD 參數需求來變更 CIS 基準測試預設值)。*

- 組態設定，包括最少的功能 [NIST 特刊 800-53 控制項 CM-6、CM-7]

  > CM-6 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 保留在 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據特定系統運作需要的組織需求自訂。除了其他組態資源，還會建立此群組原則物件，來記錄和確保以一致的方式實作組態設定。*

  > CM-7 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 保留在 Azure 內所有客戶控制的 Windows 機器。群組原則物件是依照 Windows Server 2012 R2 的 CIS 基準測試來設定，並根據特定系統運作需要的組織需求自訂。您可以使用群組原則物件來建立基準虛擬機器映像，保留在 Azure 內以供所有 Windows 機器使用，並反映 Windows 機器組態，只提供系統運作所需的基本功能。*

  包括適用於 Windows 機器的預先設定群組原則物件 (GPO) 和適用於 Linux 機器的殼層指令碼在內的安全性基準補救工具可從 [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines) 取得，而[網際網路安全性中心 (Center for Internet Security)](https://benchmarks.cisecurity.org/)和[國防資訊系統局 (Defense Information Systems Agency，DISA)](http://iase.disa.mil/stigs) 對於 Windows 和 Linux 機器均可使用。  

您可以根據安全性控制項需求將 Azure 虛擬機器加密以保護待用資訊 [NIST 特刊 800-53 控制項 SC-28]。 Azure 快速入門範本可用來將加密部署到[新的](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)和現有 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) 與 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 虛擬機器。 

> SC-28 範例控制項實作說明：*Azure 內客戶控制的虛擬機器會實作磁碟加密來保護待用資訊的完整性與機密性。 適用於 Windows 的 Azure 磁碟加密是使用 Windows 的 BitLocker 功能來實作。適用於 Linux 虛擬機器的磁碟加密是使用 Linux 的 DM-Crypt 功能來實作。

您可以根據安全性控制項需求，部署數個 Azure 虛擬機器擴充功能。 [Microsoft Antimalware 虛擬機器擴充功能](https://docs.microsoft.com/azure/security/azure-security-antimalware)可以部署到新的和現有的虛擬機器。 Antimalware 擴充功能可以啟用即時保護，以及定期的排程掃描 [NIST 特刊 800-53 控制項 SI-3]。

> SI-3 範例控制項實作說明︰*適用於 Azure 中客戶所控制 Windows 虛擬機器的主機型反惡意程式碼保護，可以使用 Microsoft Antimalware 虛擬機器擴充功能來實作。設定此擴充功能以執行即時和定期掃描 (每週)，自動更新反惡意程式碼引擎和保護簽章、執行自動補救動作，以及透過 Azure 診斷提供通知。*

*其他工具和資源*

Azure Resource Manager 範本[建置組塊](https://github.com/mspnp/template-building-blocks)，可以自訂和用來部署 Azure 資源，包括部署[虛擬機器](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage)和[虛擬機器擴充功能](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions)。 

### <a name="virtual-network"></a>虛擬網路

Azure 虛擬網路 (VNet) 透過部署和設定子網路、網路安全性群組及使用者定義的路由，允許完整控制虛擬網路架構內的安全性原則和路由。 網路安全性群組可套用至子網路或個別電腦，根據多層式架構以邏輯方式依照工作負載來分隔資源，以用於其他用途。 在下面的參考架構中，會依照不同的 Web、商務及資料層子網路，以及 Active Directory 資源和管理的子網路來將資源分組。 網路安全性群組可套用至每個子網路，以限制虛擬網路內的網路流量。 
 
![使用 Microsoft Azure 的多層式架構](./media/documentation-government-plan-compliance/compute-n-tier.png)

網路安全性群組能夠完整控制資源之間的通訊路徑 [NIST 特刊 800-53 控制項 AC-4] 。

> AC-4 範例控制項實作說明︰*在 Azure 中實作 Web、商務和資料層函式的客戶控制虛擬機器，會依照子網路分隔。為每個子網路定義和套用的網路安全性群組，可限制資訊系統功能在網路層只有最起碼的資訊流程。*

您可以對出自子網路與虛擬機器的傳出通訊套用網路安全性群組。 如此能夠完整控制 Azure 與外部資訊系統的資訊系統元件之間的通訊 [NIST 特刊 800-53 控制項 CA-3 (5)]。 網路安全性群組規則處理可實作為全部拒絕但允許例外狀況的函式。 此外，使用者定義的路由可以設定為透過像是防火牆或 IDS/IPS 等虛擬設備，路由出自特定子網路和虛擬機器的傳入和傳出通訊，進一步管理系統通訊。

> CA-3 (5) 範例控制項實作說明︰*源自 Azure 中客戶所控制資源的所有傳出通訊可透過實作網路安全性群組來限制，除非設定的輸出規則集明確允許，否則會拒絕所有流量，以支援符合 FedRAMP 和 DoD L4 需求的資訊系統功能。此外，將使用者定義的路由設定為透過虛擬防火牆設備路由所有輸出流量，利用規則集設定為只允許和經過核准的外部資訊系統通訊。*

上述參考架構示範如何利用套用的網路安全性群組規則集，以邏輯方式將 Azure 資源分組至不同的子網路，以確保將安全性函式和非安全性函式隔離。 在這種情況下，會從 Active Directory 子網路以及可能裝載資訊系統和安全性管理工具與資源的管理子網路，將三個 Web 應用程式層隔離出來 [NIST 特刊 800-53 控制項 SC-3]。

> SC-3 範例控制項實作說明︰*透過實作子網路與這些子網路套用的網路安全性群組，在客戶控制的 Azure 環境內將安全性函式與非安全性函式隔離。以邏輯方式從執行資訊系統安全性相關工作的管理子網路分隔 Web 應用程式的 Web、商務及資料層專用的資訊系統資源。*

此參考架構也實行受管理存取控制點，用於從遠端存取資訊系統 [NIST 特刊 800-53 控制項 AC-17 (3)]。 部署網際網路對應負載平衡器，以將傳入的網際網路流量散發至 Web 應用程式，而包括 jumpbox 或防禦主機的管理子網路可藉此控制從對系統進行的所有管理相關遠端存取。 網路安全性群組會在虛擬網路內限制流量，確保只會將外部流量路由到對外公開的指定資源。

> AC-17 (3) 範例控制項實作說明︰*只限在兩個受管理網路的存取控制點，從遠端存取 Azure 內客戶控制的資訊系統元件。1) 透過可將流量散發至 Web 層資源的網際網路對應負載平衡器，來管理指定給 Web 應用程式的網際網路流量。2) 透過環境內已隔離子網路上的防禦主機，來管理和管理相關的遠端存取。套用到 jumpbox 所在管理子網路的網路安全性群組，只讓允許清單中的公用 IP 位址連線，並以遠端桌面流量為限。*

網路安全性群組能夠完整控制 Azure 資源和外部主機與系統之間的通訊，以及內部子網路與主機之間的通訊，將指定為可公開存取和不可公開存取的資訊系統元件分隔。 除了上述參考架構中的解決方案，Azure 能夠部署像是防火牆和 IDS/IPS 解決方案等虛擬設備，配合使用者定義的路由一起使用時，可進一步保護和管理 Azure 資源與外部網路和資訊系統之間的連線 [NIST 特刊 800-53 控制項 SC-7]。

> SC-7 範例控制項實作說明︰*透過數個部署的界限保護機制，來保護 Azure 內客戶控制的資源。對網路子網路定義和套用的網路安全性群組，可在資訊系統界限和虛擬網路內限制流量。網路安全性群組包含的存取控制清單 (ACL) 規則可控制傳入和傳出子網路與虛擬機器的流量。部署的負載平衡器可將連入的網際網路流量散發至特定資源。子網路能確保以邏輯方式將對外公開的可存取資訊系統元件和非公用資源分隔。*

網路安全性群組的規則集能限制特定的網路連接埠和通訊協定，這個重要元件能確保只會以提供基本功能的方式實作系統 [NIST 特刊 800-53 控制項 CM-7]。

> CM-7 範例控制項實作說明︰*透過客戶控制的 Azure 環境套用至子網路和虛擬機器的網路安全性群組，可使用全部拒絕但允許例外狀況的方法來實作。這可確保只允許明確核准的連接埠和通訊協定能有網路流量，支援最少功能概念。*

*其他工具和資源*

上述參考架構的文件可在 Azure [文件網站](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm)取得。 在相同頁面上，還包括可部署參考架構的 Azure Resource Manager 範本。 Azure 文件網站也包含關於[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)和[使用者定義路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)的詳細資訊。

### <a name="storage"></a>儲存體

複寫儲存在 Azure 儲存體的資料，可確保高可用性。 使用多個複寫選項時，異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS) 會確保將資料複寫到次要地區。 將主要和次要地區配對，萬一整個地區的服務中斷或發生災害時，可確保資料中心之間有維持可用性所需的距離 [NIST 特刊 800-53 控制項 CP-9]。 如需異地備援儲存體、高可用性儲存體，可在建立新儲存體帳戶時，選取異地備援儲存體 (GRS) 或讀取權限異地備援儲存體 (RA-GRS)。 

> CP-9 範例控制項實作說明︰*Azure 內所有客戶控制的儲存體帳戶都實作異地備援儲存體，請務必跨兩個資料中心在不同節點上將所有資料保留六份。*

Azure 儲存體服務加密 (SSE) 可保護 Azure 儲存體帳戶內的待用資料 [NIST 特刊 800-53 控制項 SC-28、SC-28 (1)]。 啟用時，Azure 會先自動加密資料，再保存到儲存體。 使用 256 位元 AES 加密來加密資料。 SSE 支援將區塊 Blob、附加 Blob 和分頁 Blob 加密。

> SC-28、SC-28 (1) 範例控制項實作說明︰*透過使用 Azure SSE 對所有待用資料使用 256 位元 AES 加密，利用客戶控制的 Azure 環境來保護所有儲存體 Blob 的機密性與完整性。*

*其他工具和資源*

Azure 文件網站提供有關[儲存體服務加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)和[儲存體複寫](https://docs.microsoft.com/azure/storage/storage-redundancy)的詳細資訊。 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory 提供了在 Microsoft Azure 中執行資訊系統的身分識別和存取功能。 使用者能透過使用目錄服務、安全性群組及群組原則，協助控制使用 Azure Active Directory 的機器存取權和安全性原則。 使用帳戶和安全性群組來協助管理資訊系統的存取權。 群組原則可協助確保符合組態需求。

Azure Active Directory 安全性群組與目錄服務，可協助實作角色型存取控制 (RBAC) 的存取控制配置和控制資訊系統的存取權。 這可能包括安全性控制項實作︰

- 帳戶管理 [NIST 特刊 800-53 控制項 AC-2]

  > AC-2.a 範例控制項實作說明︰ *使用 Azure Active Directory，利用 Active Directory 群組實作角色型存取控制來管理存取權。管理帳戶和存取 AAD 所支援網域上的帳戶已有既定要求。只有安全性群組支援存取網域的成員伺服器。每一群組都有主要和次要擁有者。這些擁有者負責維護群組成員資格、使用權限以及描述的準確度。*

  > AC-2.c 範例控制項實作說明︰*當使用者要求存取任何安全性群組時，該要求必須由群組擁有者根據定義的成員資格準則來核准。帳戶條件由安全性群組擁有者決定。*

  > AC-2.f 範例控制項實作說明︰*使用 Azure Active Directory 來管理資訊系統的控制存取權。帳戶管理員要遵循帳戶管理原則，視需要建立、啟用、修改、停用或移除資訊系統帳戶。*

  > AC-2 (1) 範例控制項實作說明︰ *使用 Azure Active Directory，利用 Active Directory 群組實作角色型存取控制來管理存取權。管理帳戶和存取 AAD 所支援網域上的帳戶已有既定要求。只有安全性群組支援存取網域的成員伺服器。每一群組都有主要和次要擁有者。這些擁有者負責維護群組成員資格、使用權限以及描述的準確度。*

  > AC-2 (2) 範例控制項實作說明︰*使用 Azure Active Directory 來管理資訊系統的控制存取權。帳戶系統管理員要遵循帳戶管理原則，來建立暫時帳戶。這些暫時帳戶都必須根據原則需求設定到期時間。*

  > AC-2 (3) 範例控制項實作說明︰*Azure Active Directory 可管理資訊系統的控制存取權。帳戶系統管理員要遵循帳戶管理原則，來建立暫時帳戶。這些暫時帳戶都必須根據原則需求設定到期時間。*

  >AC-2 (7) 範例控制項實作說明︰*Azure Active Directory 依照角色型存取配置，將資訊系統權限分給指派至安全性群組的角色，來管理資訊系統的控制存取權。安全性群組系統管理員負責授與使用者的存取權，以將使用者指派至正確的安全性群組。指派每一安全性群組的權限，使其能以最少的存取權適當完成所屬工作。*

  > AC-2 (11) 範例控制項實作說明︰*Azure Active Directory 可管理 Azure 內資訊系統的控制存取權。帳戶系統管理員會在 Azure Active Directory 內，定義和強制執行安全性原則要求的使用限制。*

- 強制執行存取權 [NIST 特刊 800-53 控制項 AC-3]

  > AC-3 範例控制項實作說明︰*Azure Active Directory 使用角色型存取控制，對客戶環境強制執行核准的授權。Azure Active Directory 安全性群組的存取權由安全性群組系統管理員管理。根據使用者角色將其放置在適當的安全性群組，使用最低權限原則。*

- 最低權限 [NIST 特刊 800-53 控制項 AC-6]

  > AC-6 (10) 範例控制項實作說明︰*Azure Active Directory 使用角色型存取控制，對客戶環境強制執行核准的授權。Azure Active Directory 安全性群組的存取權由安全性群組系統管理員管理。安全性群組允許使用者存取有特殊權限的函式，包括任何允許停用、規避或更改安全性保護功能的權限，未授與其存取權就是不具權限的使用者。*

- 遠端存取 [NIST 特刊 800-53 控制項 AC-17]

  > AC-17 (1) 範例控制項實作說明︰*使用 Azure Active Directory 來監視和控制所有遠端存取。Azure Active Directory 包括目錄的安全性、活動及稽核報告。*

- 保護稽核資訊 [NIST 特刊 800-53 控制項 AU-9]

  > AU-9 範例控制項實作說明︰*使用受到嚴密管理的存取控制，保護稽核資訊和工具，防止遭到未經授權的存取、修改和刪除。Azure Active Directory 使用 Active Directory 原則和角色型群組成員資格，採用分層式存取授權和邏輯控制方法，強制使用者只能進行經過核准的邏輯存取。只限需要這些權限的使用者，才能檢視稽核資訊和使用稽核工具。*

  > AU-9 (4) 範例控制項實作說明︰ *Azure Active Directory 限制只有適當安全性群組的成員才能管理稽核功能。只將這些權限授與具特定存取需求的人員，來管理稽核功能。*

- 變更的存取限制 [NIST 特刊 800-53 控制項 CM-5]

  > CM-5 範例控制項實作說明︰ *Azure Active Directory 強制執行的 RBAC 可用來定義、記錄、核准和強制執行與變更相關聯的邏輯存取限制。在系統內建立的所有帳戶都是角色型帳戶。人員要求從帳戶系統管理員，存取和一經核准，都會被置於適當的安全性群組根據其角色。只有在核准後，特定安全性群組的成員才能存取生產環境。*

  > CM-5 (1)範例控制項實作說明︰*Azure Active Directory 會透過安全性群組成員資格強制執行邏輯存取限制。這需要安全性群組擁有者將存取權授與特定的安全性群組。*

- 使用者識別和驗證 [NIST 特刊 800-53 控制項 IA-2]

  > IA-2 範例控制項實作說明︰*存取資訊系統環境的人員可透過其使用者名稱唯一識別身分，並使用 Azure Active Directory 進行驗證。必須向 Azure Active Directory 驗證，才能存取資訊系統。*

  > IA-2 (8) 範例控制項實作說明︰*透過 Azure Active Directory 的內建 Kerberos 功能，保護生產環境的存取抵禦重新執行攻擊。在 Kerberos 驗證中，用戶端傳送的驗證器包含其他資料，例如加密的 IP 清單、用戶端時間戳記以及票證存留期。如果重新執行封包，就會檢查時間戳記。如果時間戳記早於或和先前的驗證器相同，該封包會遭到拒絕。*

  > IA-2 (9) 範例控制項實作說明︰*透過 Azure Active Directory 的內建 Kerberos 功能，保護所有帳戶抵禦重新執行攻擊。在 Kerberos 驗證中，用戶端傳送的驗證器包含其他資料，例如加密的 IP 清單、用戶端時間戳記以及票證存留期。如果重新執行封包，就會檢查時間戳記。如果時間戳記早於或和先前的驗證器相同，該封包會遭到拒絕。*

- 識別碼管理 [NIST 特刊 800-53 控制項 IA-4]

  > IA&4;.b 範例控制項實作說明︰*使用 Azure Active Directory 的帳戶識別碼來識別使用者。這些是不會重複使用的唯一識別碼。*

  > IA-4.c 範例控制項實作說明︰*Azure Active Directory 是中央帳戶存放庫，用來提供服務環境的存取權。在 Azure Active Directory 中建立帳戶時，會將使用者的唯一識別碼指派給該位人員。*

  > IA-4.d 範例控制項實作說明︰*永不重複使用唯一的使用者識別碼。這會由 Azure Active Directory 強制執行。*

  > IA-4.e 範例控制項實作說明︰*設定 Azure Active Directory 內的所有帳戶會在 35 天無活動狀態後自動停用。*

  > IA-4 (4) 範例控制項實作說明︰*Azure Active Directory 使用其唯一 Azure Active Directory 認證套用的命名慣例，來表示約聘人員和廠商。*

- 驗證器管理 [NIST 特刊 800-53 控制項 IA-5]

  > IA-5.b 範例控制項實作說明︰*Azure Active Directory 會在一開始建立帳戶時，指派唯一的識別碼和符合原則需求的隨機暫時密碼。Azure Active Directory 會在帳戶存留期間全程維護與帳戶相關聯的唯一識別碼。Azure Active Directory 永不重複使用帳戶識別碼。*

  > IA-5.c 範例控制項實作說明︰*Azure Active Directory 可確保發出的密碼符合密碼複雜性的原則需求。*

  > IA-5.d 範例控制項實作說明︰*由帳戶系統管理員處理初始驗證器發佈程序。如 AC-2 中所定義，修改使用者帳戶存取權要經過帳戶管理程序。如果驗證器遺失或遭到洩露，帳戶系統管理員必須遵循定義的程序，視需要重設、重新發行或撤銷驗證器。*

  > IA-5 (1).c 範例控制項實作說明︰*使用 Azure Active Directory 來確保在儲存和傳輸時會以密碼編譯方式保護所有密碼。Azure Active Directory 包含自動雜湊處理 Azure Active Directory 所儲存密碼的功能。*

  > IA-5 (1).f 範例控制項實作說明︰*使用 Azure Active Directory 來管理資訊系統的控制存取權。每當一開始建立帳戶或產生暫時密碼時，都會使用 Azure Active Directory 要求使用者變更下次登入時要用的密碼。*

  > IA-5 (4) 範例控制項實作說明︰*採用 Azure Active Directory 這個自動化工具，判斷密碼驗證器強度是否足以滿足 IA-5 (1) 中所建立的密碼長度、複雜度、輪替及存留期限。Azure Active Directory 可確保建立的密碼驗證器強度符合這些標準。*

Azure Active Directory 群組原則組態可以用來部署符合特定安全性需求的客戶安全性原則。 客戶的 Azure 虛擬機器可以透過 Azure Active Directory 來套用這些原則。
Azure Active Directory 群組原則組態包括安全性控制項實作︰

- 最低權限 [NIST 特刊 800-53 控制項 AC-6]

  > AC-6 (9) 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。您可以根據組織需求自訂群組原則物件，包括稽核有特殊權限的函式執行情況。*

- 不成功的登入嘗試 [NIST 特刊 800-53 控制項 AC-7]

  > AC-7 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括在連續的登入嘗試失敗後啟動帳戶鎖定。*

- 並行工作階段控制項 [NIST 特刊 800-53 控制項 AC-10]、工作階段鎖定 [NIST 特刊 800-53 控制項 AC-11]、工作階段終止 [NIST 特刊 800-53 控制項 AC-12]

  > AC-10 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括限制使用者只能存取符合 FedRAMP 和 DoD 需求的單一遠端桌面服務工作階段。*

  > AC-11.a 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括維持 900 秒 (15 分鐘) 無活動狀態後，啟動工作階段鎖定，直到使用者重新認證為止。*

  > AC-12 範例控制項實作說明︰*Windows 會在收到客戶使用者的登出要求時，自動終止遠端桌面工作階段。此外，會將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括在 900 秒 (15 分鐘) 無活動狀態後，終止遠端桌面工作階段。*

- 驗證器管理 [NIST 特刊 800-53 控制項 IA-5]

  > IA-5.f 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括本機帳戶密碼的限制，以強制使用符合 FedRAMP 需求的最短 (1 天) 和最長 (60 天) 存留期限制、重複使用條件 (24 個密碼)、長度 (14 個字元) 及複雜度需求。*

  > IA-5.g 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括本機帳戶密碼的限制，以強制使用符合 FedRAMP 需求的最長 (60 天) 存留期限制。*

  > IA-5 (1).a 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括符合 FedRAMP 需求的複雜度需求。*

  > IA-5 (1).b 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括符合 FedRAMP 需求的複雜度需求。*

  > IA-5 (1).d 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。 群組原則物件是根據組織需求自訂，包括本機帳戶密碼的限制，以強制使用符合 FedRAMP 需求的最短 (1 天) 和最長 (60 天) 存留期限制。

  > IA-5 (1).e 範例控制項實作說明︰*將組態控制群組原則物件 (GPO) 套用至 Azure 內所有客戶控制的 Windows 機器。群組原則物件是根據組織需求自訂，包括符合 FedRAMP 需求的重複使用條件 (24 個密碼)。*

*其他工具和資源*

提供文件、工具、範本和其他資源，引導安全地部署 Azure 服務和功能。 瀏覽 [Microsoft Azure 文件](https://docs.microsoft.com/azure/active-directory/)，開始使用 Azure Active Directory。

### <a name="key-vault"></a>金鑰保存庫

Azure Key Vault 可提供保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 透過使用 Azure Key Vault，客戶可以建立、管理和保護金鑰與密碼。 您可以使用安全的容器 (保存庫)，安全地儲存和管理密碼編譯金鑰與密碼。 使用經 FIPS 140-2 Level 2 驗證的 HSM，Azure Key Vault 可以用來產生密碼編譯金鑰。

Azure Key Vault 容器可協助安全地儲存密碼編譯金鑰，提供高可用性。 這可能包括安全性控制項實作︰

-   保護驗證器 [NIST 特刊 800-53 控制項 IA-5 (7)]

  > IA-5 (7) 範例控制項實作說明︰*明確禁止使用內嵌在應用程式、存取指令碼或函式金鑰的未加密靜態驗證器。使用驗證器的任何指令碼或應用程式，會在每次使用之前呼叫 Azure Key Vault 容器。稽核 Azure Key Vault 容器的存取情況，如果使用服務帳戶存取系統，卻沒有對應的 Azure Key Vault 容器呼叫，就能偵測出這項限制違規。*

- 建立和管理密碼編譯金鑰 [NIST 特刊 800-53 控制項 SC-12 (1)]

  > SC-12 (1) 範例控制項實作說明︰*使用 Azure Key Vault 來儲存密碼編譯金鑰和密碼。此服務會追蹤和監視機密資料的存取情況。使用此服務可確保不會遺失機密資料。*

使用符合 FIPS 140-2 Level 2 驗證的 HSM，Azure Key Vault 可以用來建立密碼編譯金鑰。 Azure Active Directory 群組原則組態包括安全性控制項實作︰

- 建立和管理密碼編譯金鑰 [NIST 特刊 800-53 控制項 SC-12 (2)]

  > SC-12 (2) 範例控制項實作說明︰*使用 Azure Key Vault 來產生、控制和散發密碼編譯金鑰。這些金鑰是使用經 FIPS 140-2 Level 2 驗證的 HSM 來產生。您可以在 Azure Key Vault 內的安全加密容器中儲存和管理這些金鑰。*

*其他工具和資源*

提供文件、工具、範本和其他資源，引導安全地部署 Azure 服務和功能。 瀏覽 [Microsoft Azure 文件](https://docs.microsoft.com/azure/key-vault/)，開始使用 Azure Key Vault。

### <a name="operations-management-suite"></a>Operations Management Suite

Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助管理並保護內部部署和雲端基礎結構。 安全性與法規遵循可協助識別、評定並降低基礎結構的安全性風險。 這些 OMS 功能是透過多個解決方案來實作，包括可分析記錄檔資料和監視安全性組態的 Log Analytics。

啟用安全性和稽核服務的 OMS 可協助實作帳戶監視和記錄。 這可能包括安全性控制項實作︰

- 帳戶管理 [NIST 特刊 800-53 控制項 AC-2]

  > AC-2.g 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 監視系統帳戶的使用情況。OMS 為系統帳戶建立的稽核記錄檔，之後可利用 OMS 的分析功能來分析，並根據一組定義的準則提出警示。*

  > AC-2 (4) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 來產生帳戶管理功能的稽核記錄檔，例如：帳戶建立、修改、啟用、停用以及移除動作。使用 OMS，如果已執行上述任一條件，隨即會向系統擁有者提出警示。*

  > AC-2 (12) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 監視系統帳戶是否有非典型的使用情況。OMS 為系統帳戶建立的稽核記錄檔，之後可使用 OMS 的分析功能來分析，並根據一組定義的準則來向適當的人員提出警示。*

- 最低權限 [NIST 特刊 800-53 控制項 AC-6]

  > AC-6 (9) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 監視有特殊權限的函式執行情況。OMS 為一份指定函式清單建立的稽核記錄檔，之後可利用 OMS 的分析功能來分析，並根據一組定義的準則提出警示。*

 - 遠端存取 [NIST 特刊 800-53 控制項 AC-17]

  > AC-17 (1) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 監視和控制遠端存取。OMS 包括目錄的安全性、活動和稽核報告。*

- 稽核事件 [NIST 特刊 800-53 控制項 AU-2]

  > AU-2 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 稽核︰成功和失敗的帳戶登入事件、帳戶管理事件、物件存取、原則變更、有特殊權限的函式、程序追蹤以及系統事件。對於 Web 應用程式︰所有系統管理員活動、驗證檢查、授權檢查、資料刪除、資料存取、資料變更以及權限變更。還有任何其他客戶定義的事件集合。*

- 稽核記錄內容 [NIST 特刊 800-53 控制 AU-3]

  > AU-3 (2) 範例控制項實作說明︰*使用Microsoft 的 Operation Management Suite (OMS) 當成集中式管理和設定由網路、儲存體和計算設備產生的所有稽核記錄的方式。*

- 稽核儲存體容量 [NIST 特刊 800-53 控制項 AU-4]

  > AU-4 範例控制項實作說明︰*使用Microsoft 的 Operation Management Suite (OMS) 以用於集中式管理和設定由網路、儲存體和計算設備所產生所有稽核記錄。使用集中式管理工具來設定稽核記錄的儲存體容量。*

- 稽核處理失敗時的回應 [NIST 特刊 800-53 控制項 AU-5]

  > AU-5 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，在稽核處理失敗時，向組織定義的人員提出警示。*

  > AU-5 (1) 控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，稽核記錄儲存體若在組織定義的時間內達到組織定義的存放庫磁碟區上限百分比時，隨即向組織定義的人員提出警示。*

  > AU-5 (2) 控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，在組織原則中定義的可稽核事件失敗時，即時向組織定義的人員發出警示。*

- 稽核檢閱、分析和報告 [NIST 特刊 800-53 控制項 AU-6]

  > AU-6 (3) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核方案以及 Log Analytics 解決方案，分析不同的稽核記錄檔存放庫，提供整個組織的局勢意識。使用分析工具報告稽核記錄檔以提供局勢意識。*

  > AU-6 (4) 範例控制項實作說明︰*使用Microsoft 的 Operation Management Suite (OMS) 以用於集中式管理和設定所有稽核記錄。集中式管理工具允許檢閱和分析所有來源的稽核記錄。*

  > AU-6 (5) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核方案以及 Log Analytics 解決方案，分析從弱點掃描、資訊系統監視及效能相關資料所產生的不同安全性相關資料。分析這些不同的資源分析能夠增強找出可疑活動的能力。*

- 縮減稽核和產生報告 [NIST 特刊 800-53 控制項 AU-7]

  > AU-7 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核方案以及 Log Analytics 解決方案，產生人類看得懂的隨選報告，以允許事後調查安全性事件。使用 Microsoft Log Analytics 工具不會永久或無法回復地更改原始的稽核記錄內容或時間排序。*

  > AU-7 (1) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，來查詢組織定義的可稽核事件。*

- 保護稽核資訊 [NIST 特刊 800-53 控制項 AU-9]

  > AU-9 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 與安全性群組用，來管理稽核資訊和工具的存取情況，以防止未經授權存取、修改和刪除稽核記錄。只限需要這些權限的使用者，才能檢視稽核資訊和使用稽核工具。*

  > AU-9 (2) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，以及 Azure 備份來將稽核記錄檔備份至 Azure，然後複寫資料以提供資料可靠性與可用性。Azure 備份可在受到稽核的系統以外的位置，提供可安全儲存稽核記錄檔的位置。* 

  > AU-9 (4) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 與安全性群組用，來管理稽核資訊和工具的存取情況。只將這些權限授與具特定存取需求的人員，來管理稽核功能。*

- 保留稽核記錄 [NIST 特刊 800-53 控制項 AU-11]

  > AU-11 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，來設定稽核記錄檔的保留時間。稽核記錄檔保留期會設定為保留至少 90 天的稽核資料。*

- 產生稽核 [NIST 特刊 800-53 控制項 AU-12]

  > AU-12 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案，從資訊系統元件收集 AU-02 中定義的可稽核事件。由組織定義人員使用 OMS 的安全性和稽核解決方案，定義要從特定裝置收集的可稽核事件。*

  > AU-12 (1) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核方案以及 Log Analytics 解決方案，編譯來自系統元件的稽核記錄，依照時間戳記排序來建立全系統的稽核記錄。*

- 變更的存取限制 [NIST 特刊 800-53 控制項 CM-5]

  > CM-5 (1) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核方案以及 Active Directory，透過安全性群組成員資格強制執行邏輯存取限制。由 OMS 稽核這些安全性群組執行的動作。*

- 清查資訊系統元件 [NIST 特刊 800-53 控制項 CM-8]

  > CM-8 (3) 範例控制項實作說明︰*使用 Microsoft 的 Operation Management Suite (OMS) 搭配安全性和稽核解決方案以及 Antimalware 解決方案，偵測是否有未經授權的軟體存在。偵測時，OMS 會停用受感染的元件，並向組織定義的人員傳送警示。*

提供文件、工具、範本和其他資源，引導安全地部署 Azure 服務和功能。 瀏覽 [Microsoft Azure 文件](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/)，開始使用 Operation Management Suite。

### <a name="additional-implementation-guidance"></a>其他實作指引

除了上述的核心 Azure 服務之外，還有數個內建功能可協助組織符合安全性控制法規遵循需求。 所有 Azure 資源會組織成資源群組。 還可以套用標籤，利用客戶選擇的索引鍵/值組，依據類別或任何其他屬性來識別資源，進一步組織資產。 Azure 資源群組確保會完整識別和追蹤 Azure 內部署的所有客戶資源 [NIST 特刊 800-53 控制項 CM-8]。 

> CM-8 範例控制項實作說明︰*其中部署的所有客戶控制資源都屬於「客戶資源」資源群組。在 Azure 入口網站內，所有部署的資產都會在資源群組刀鋒視窗內識別，確保目前的清查準確。依據追蹤和報告需求，視需要將標籤套用至資源。*

Azure Resource Manager 範本可讓客戶在其 Azure 部署內定義資源組態。 Resource Manager 範本為 JSON 型並使用宣告式語法，記錄 Azure 環境的資源、組態參數以及其他方面。 Resource Manager 範本是在從 Azure 入口網站部署 Azure 解決方案時自動產生，並可依據特定客戶需求手動撰寫和編輯。 這些 Resource Manager 範本可做為資訊系統的基準組態表示法 [NIST 特刊 800-53 控制項 CM-2]。 現有的 Resource Manager 範本像是上面提及的建置組塊，可依據任何部署需求來自訂。

> CM-2 範例控制項實作說明︰*Azure Resource Manager 範本可當成系統基準組態資訊的元件，其會依據組態控制項來維護，代表 Azure 內資訊系統元件部署的客戶控制資源與組態。這些範本會擷取部署的資源，包括虛擬機器、儲存體及網路資源，還有網路資源組態，可控制進入、離開，還有客戶 Azure 環境當中的網路流量。*

## <a name="next-steps"></a>後續步驟

如需查詢有關 Azure Blueprint、FedRAMP、DoD 或機構 ATO，或需要其他法規遵循協助，或想提供 Blueprint 意見反應，請將電子郵件寄至 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

[Microsoft 信任中心](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


