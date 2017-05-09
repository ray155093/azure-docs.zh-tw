--- 
title: "開始使用 Azure 自動化 | Microsoft Docs"
description: "本文藉由回顧準備從 Auzre Marketplace 將供應項目上架的核心概念和實作詳細資料，提供 Azure 自動化服務的概觀。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 8a04fda8eaf6e14a278941e7bb55b23012f67850
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---

## <a name="getting-started-with-azure-automation"></a>開始使用 Azure 自動化

本入門指南會介紹 Azure 自動化的部署相關核心概念。 如果您不熟悉 Azure 中的自動化，或具備使用自動化工作流程軟體 (例如 System Center Orchestrator) 的經驗，本指南可協助您初步了解相關概念和部署細節。 

## <a name="key-concepts"></a>重要概念

### <a name="automation-service"></a>自動化服務
自動化是一項 Azure 服務，其使用 Windows PowerShell 和 Azure 技術來協助您控制 Azure、雲端及內部部署基礎結構和應用程式的管理。  Azure 自動化可透過使用程序自動化的自動化部署、使用 PowerShell 期望狀態組態的作業系統組態、使用更新管理與變更追蹤的持續性更新和監視，以及自動進行疑難排解和修復等方式，協助您管理服務與應用程式的完整生命週期。

### <a name="automation-account"></a>自動化帳戶
自動化帳戶是您所建立的 Azure 資源。  您可以使用單一自動化帳戶來管理所有的 Azure、雲端和內部部署資源。  自動化帳戶是要讓自動化運作之項目的容器︰其中包含 Runbook、模組、資產 (如認證和排程) 以及組態。 您可以使用多個自動化帳戶，將資源分成個別的邏輯環境，像是開發、測試和生產或跨越地理區域。  

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
若要在本機資料中心、Azure 或其他雲端提供者的實體或虛擬系統上執行 Runbook，「混合式 Runbook 背景工作」功能可讓您存取和管理這些本機資源。     

### <a name="automation-desired-state-configuration"></a>自動化期望狀態組態
以 PowerShell DSC 為建置基礎的自動化期望狀態組態 (DSC) 可設定、監視及自動更新裝載於 Azure、內部部署或其他雲端中之作業系統的期望狀態。  

### <a name="management-solutions"></a>管理解決方案
管理解決方案 (如「更新管理」和「變更追蹤」) 可擴充 Azure 自動化的功能並搭配 Log Analytics 使用。  它們可以包含多個支援特定管理案例的資源，例如自動化 Runbook、預先定義的 Log Analytics 搜尋查詢和警示以及視覺效果。  

## <a name="architecture-overview"></a>架構概觀

Azure 自動化是一個軟體即服務 (SaaS) 應用程式，主要提供可調整且可靠的多租用戶環境，以便透過 Runbook 自動執行程序，並使用 Azure、其他雲端服務或內部部署中的期望狀態組態 (DSC) 來管理 Windows 和 Linux 系統的組態變更。 您自動化帳戶中的實體 (如 Runbook、資產、執行身分帳戶) 會與您的訂用帳戶和其他訂閱中的其他自動化帳戶隔離。  

您在 Azure 中執行的 Runbook 會在自動化沙箱上執行，而這些沙箱裝載於 Azure 平台即服務 (PaaS) 虛擬機器中。  自動化沙箱可為 Runbook 執行的下列所有層面提供租用戶隔離 – 模組、儲存體、 憶體、網路通訊、作業串流等。此角色是由服務管理，且無法從 Azure 或 Azure 自動化帳戶存取供您控制。         

若要讓本機資料中心或其他雲端服務中的資源部署和管理自動化，您可以指定一或多部機器來執行混合式 Runbook 背景工作 (HRW) 角色。  每個 HRW 都需要 Microsoft 管理代理程式 (MMA) 連線至 Log Analytics 工作區和自動化帳戶。  Log Analytics 用來啟動安裝程序、維護 MMA 代理程式及監視 HRW 的功能。  Runbook 的傳遞和執行的指示都是透過 Azure 自動化執行。

您可以部署多個 HRW 來為您的 Runbook 提供高可用性、平衡 Runbook 作業的負載，以及在某些情況下將它們專用於特定工作負載或環境。  HRW 會透過 TCP 輸出連接埠 443 來與自動化服務通訊。  一旦在您資料中心內的 HRW 上執行 Runbook，而且您希望 Runbook 對資料中心內的其他電腦或服務執行管理工作，則 Runbook 可能需要存取其他連接埠。  如果 IT 安全性原則不允許您網路上的電腦連線至網際網路，請檢閱 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)一文，其可作為 Proxy，以便 HRW 收集作業狀態及接收來自自動化帳戶的組態資訊。

在 HRW 上執行的 Runbook 會在電腦上的本機系統帳戶環境中執行，這是在本機 Windows 電腦上執行系統管理動作時的建議安全性環境。 如果您希望 Runbook 對本機電腦外部的資源執行工作，您可能需要在自動化帳戶中定義可以從 Runbook 存取並用來驗證外部資源的安全認證資產。 您可以在您的 Runbook 中使用[認證](automation-credentials.md)、[憑證](automation-certificates.md)和[連線](automation-connections.md)資產，搭配可讓您指定認證的 Cmdlet，以便驗證這些資產。

儲存在 Azure 自動化中的 DSC 組態可以直接套用到 Azure 虛擬機器。 其他實體和虛擬機器可以從 Azure 自動化 DSC 提取伺服器中要求組態。  若要管理內部部署實體或虛擬 Windows 和 Linux 系統的組態，您不需要部署任何基礎結構，即可支援自動化 DSC 提取伺服器，而自動化 DSC 只會管理各系統的輸出網際網路存取，並透過 TCP 通訊埠 443 與 OMS 服務進行通訊。   

![Azure 自動化概觀](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

## <a name="prerequisites"></a>必要條件

### <a name="automation-dsc"></a>自動化 DSC
Azure 自動化 DSC 可以用來管理各種機器：

* 執行 Windows 或 Linux 的 Azure 虛擬機器 (傳統)
* 執行 Windows 或 Linux 的 Azure 虛擬機器
* 執行 Windows 或 Linux 的 Amazon Web Services (AWS) 虛擬機器
* 位於內部部署或 Azure 或 AWS 以外之雲端中的實體/虛擬 Windows 電腦
* 位於內部部署或 Azure 或 AWS 以外之雲端中的實體/虛擬 Linux 電腦

必須安裝 WMF 5 的最新版本，Windows 適用的 PowerShell DSC 代理程式才能與 Azure 自動化通訊。 必須安裝 [Linux 適用之 PowerShell DSC 代理程式](https://www.microsoft.com/en-us/download/details.aspx?id=49150)的最新版本，Linux 才能與 Azure 自動化通訊。

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
指定電腦來執行混合 Runbook 作業時，這台電腦必須具備下列條件︰

* Windows Server 2012 或更新版本
* Windows PowerShell 4.0 或更新版本。  我們建議在電腦上安裝 Windows PowerShell 5.0，以提升可靠性。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=50395)下載新版本
* 至少雙核心
* 至少 4 GB 的 RAM

## <a name="security"></a>安全性
Azure 自動化可讓您針對 Azure、內部部署以及其他雲端提供者的資源自動執行工作。  為了讓 Runbook 執行其必要動作，其必須有權能以訂用帳戶內的最少必要權限，安全地存取資源。  

### <a name="automation-account"></a>自動化帳戶 
在 Azure 自動化中使用 Azure Cmdlet 針對資源執行的所有自動化工作，均使用 Azure Active Directory 組織身分識別的認證型驗證向 Azure 進行驗證。  自動化帳戶與您用來登入入口網站以設定和使用 Azure 資源的帳戶不同。  

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但自動化帳戶可管理訂用帳戶中的所有資源。 如果您擁有需要將資料和資源區隔到特定區域的原則，請在不同的區域中建立自動化帳戶。

> [!NOTE]
> 使用 Azure 入口網站所建立的自動化帳戶以及其所包含的資源，無法在 Azure 傳統入口網站中存取。 如果您想使用 Windows PowerShell 來管理這些帳戶或它們的資源，您必須使用 「Azure 資源管理員」模組。
> 

當您在 Azure 入口網站中建立自動化帳戶時，您會自動建立兩個驗證實體：

* 執行身分帳戶。 此帳戶會在 Azure Active Directory (Azure AD) 中建立服務主體，並建立一個憑證。 此外，也會指定參與者角色型存取控制 (RBAC)，此控制可使用 Runbook 來管理 Resource Manager 資源。
* 傳統執行身分帳戶。 此帳戶會上傳管理憑證，利用此憑證就能使用 Runbook 來管理傳統資源。

Azure Resource Manager 提供了角色型存取控制來對 Azure AD 使用者帳戶和執行身分帳戶授與允許的動作，並驗證該服務主體。  若要取得有助於開發自動化權限管理模型的進一步資訊，請閱讀 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文。  

#### <a name="authentication-methods"></a>驗證方法
下表摘要說明 Azure 自動化支援的每個環境所適用的不同驗證方法。

| 方法 | 環境 
| --- | --- | 
| Azure 執行身分和傳統執行身分帳戶 |Azure Resource Manager 和 Azure 傳統部署 |  
| Azure AD 使用者帳戶 |Azure Resource Manager 和 Azure 傳統部署 |  
| Windows 驗證 |使用混合式 Runbook 背景工作的本機資料中心或其他雲端提供者 |  
| AWS 認證 |Amazon Web Services |  

[如何\驗證和安全性] 區段之下有一些支援文章，提供如何針對這些環境設定驗證的概觀和實作步驟 (使用您專用於該環境的現有或新的帳戶)。  對於 Azure 執行身分和傳統執行身分帳戶，[使用 PowerShell 更新自動化執行身分帳戶](automation-update-account-powershell.md)主題會說明如何使用 PowerShell 以執行身分帳戶更新現有的自動化帳戶 (如果原先並未以執行身分或傳統執行身分帳戶設定現有的自動化帳戶)。   
 
## <a name="network"></a>網路
若要讓混合式 Runbook 背景工作連線至 Microsoft Operations Management Suite (OMS) 並向其註冊，它必須能夠存取下述連接埠號碼和 URL。  此外，這是 [Microsoft Monitoring Agent 連線至 OMS 所需的連接埠和 URL](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent)。 如果您使用 Proxy 伺服器在代理程式和 OMS 服務之間進行通訊，您必須確保可以存取適當的資源。 如果您使用防火牆來限制網際網路存取，您需要設定防火牆以允許存取。

以下資訊列出要讓 Hybrid Runbook Worker 與自動化進行通訊所需的連接埠和 URL。

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取
* 全域 URL：*.azure-automation.net

如果您已定義特定區域的自動化帳戶，而且想要限制與該區域資料中心的通訊，下表提供每個區域的 DNS 記錄。

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 美國政府維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us |

如需 IP 位址 (而非名稱) 的清單，請從 Microsoft 下載中心下載並檢閱 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) xml 檔案。 

> [!NOTE]
> 這個檔案包含 Microsoft Azure 資料中心使用的 IP 位址範圍 (包括計算、SQL 和儲存體範圍)。 每週會公佈已更新的檔案，以反映目前部署的範圍和 IP 範圍的任何後續變更。 資料中心至少有一週不會使用檔案中顯示的新範圍。 請每週下載新的 xml 檔案，並且您的網站上執行所需的變更，以正確識別在 Azure 中執行的服務。 Express Route 使用者可能注意到，此檔案在每個月的第一週用來更新 Azure 空間的 BGP 公告。 
> 


## <a name="implementation"></a>實作

### <a name="creating-an-automation-account"></a>建立自動化帳戶

在 Azure 入口網站中建立自動化帳戶的方法有很多種：  下表介紹每種部署經驗和其間的差異。  

|方法 | 說明 |
|-------|-------------|
| 從 Marketplace 選取 [自動化與控制] | 一個供應項目，可在相同的資源群組與區域中建立自動化帳戶和 OMS 工作區。  它也可部署「變更追蹤」和「更新管理」解決方案 (預設會啟用)。 |
| 從 Marketplace 選取自動化 | 在新的或現有資源群組中建立自動化帳戶，該帳戶並未連結至 OMS 工作區，而且不包含來自 [自動化與控制] 供應項目的任何可用解決方案。 這是基本組態，可向您介紹自動化並協助您了解如何撰寫 Runbook、DSC 組態，以及使用此服務的各項功能。 |
| 選取的管理解決方案 | 如果您選取一個解決方案 – **[更新管理](../operations-management-suite/oms-solution-update-management.md)**、**[在下班期間啟動/停止 VM](automation-solution-vm-management.md)**或**[變更追蹤](../log-analytics/log-analytics-change-tracking.md)**，它們會提示您選取現有的自動化和 OMS 工作區，或提供給您建立兩者的選項 (以便在您的訂用帳戶中部署解決方案)。 |

本主題藉由將 [自動化與控制] 供應項目上架，逐步引導您建立自動化帳戶和 OMS 工作區。  若要建立用於測試的獨立自動化帳戶或預覽此服務，請檢閱[建立獨立自動化帳戶](automation-create-standalone-account.md)一文。  

### <a name="create-automation-account-integrated-with-log-analytics"></a>建立與 Log Analytics 整合的自動化帳戶
將自動化上架的建議方法是從 Marketplace 選取 [自動化與控制] 供應項目。  這會建立自動化帳戶及建立與 OMS 工作區的整合，包括用來安裝供應項目隨附之管理解決方案的選項。  

>[!NOTE]
>若要建立自動化帳戶，您必須是服務管理員角色的成員，或是獲得訂用帳戶存取權的訂用帳戶共同管理員。 您必須已新增成為該訂用帳戶之預設 Active Directory 執行個體的使用者。 此帳戶不需要獲派特殊權限角色。
>
>若您在新增至訂用帳戶的共同管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統會將您以來賓身分新增至 Active Directory。 在此情況下，您會在 [新增自動化帳戶] 刀鋒視窗中看到 「您沒有權限建立...」的警告。
>
>新增至共同管理員角色的使用者可以先從訂用帳戶的 Active Directory 執行個體中移除並重新新增，使其成為 Active Directory 中的完整使用者。 若要確認這種情況，請從 Azure 入口網站的 [Azure Active Directory] 窗格，選取 [使用者和群組]、選取 [所有使用者]，然後在選取特定使用者之後選取 [設定檔]。 使用者設定檔之下 [使用者類型] 屬性的值不得等於 [來賓]。

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。

2. 按一下 [新增] 。<br><br> ![在 Azure 入口網站中選取 [新增] 選項](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. 搜尋**自動化**，然後在搜尋結果中選取 [自動化與控制]*。<br><br> ![從 Marketplace 搜尋並選取 [自動化與控制]](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   

4. 在閱讀供應項目的描述之後，請按一下 [建立] 。  

5. 在 [自動化與控制] 設定刀鋒視窗上，選取 [OMS 工作區]。  在 [OMS 工作區] 刀鋒視窗上，選取連結到與自動化帳戶所在相同 Azure 訂用帳戶的 OMS 工作區，或建立新的 OMS 工作區。  如果您沒有 OMS 工作區，請選取 [建立新工作區]，然後在 [OMS 工作區] 刀鋒視窗上執行下列動作︰ 
   - 指定新 [OMS 工作區] 的名稱。
   - 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   - 對於 [資源群組]，您可以建立資源群組，或選取現有的資源群組。  
   - 選取 [位置] 。  目前可用的位置只有 [澳大利亞東南部]、[美國東部]、[東南亞]、[美國中西部] 和 [西歐]。
   - 選取 **定價層**。  此解決方案以兩種定價層提供︰免費和每個節點 (OMS) 層。  免費層會限制每日可收集的資料量、保留期和 Runbook 作業執行階段分鐘數。  每個節點 (OMS) 層不會限制每日可收集的資料量。  
   - 選取 [自動化帳戶]。  如果您要建立新的 OMS 工作區，您也必須建立會與先前指定的新 OMS 工作區產生關聯的自動化帳戶，包括您的 Azure 訂用帳戶、資源群組和區域。  您可以選取 [建立自動化帳戶]，然後在 [自動化帳戶] 刀鋒視窗上提供下列資訊︰ 
  - 在 [名稱] 欄位中輸入自動化帳戶的名稱。

    系統會根據所選的 OMS 工作區自動填入所有其他選項，您無法修改這些選項。  Azure 執行身分帳戶是供應項目的預設驗證方法。  一旦您按一下 [確定]，就會驗證組態選項並建立自動化帳戶。  您可以在功能表的 [通知] 底下追蹤其進度。 

    不然，選取現有的自動化執行身分帳戶。  您選取的帳戶不能已經連結到另一個 OMS 工作區，否則刀鋒視窗中會顯示一則通知訊息。  如果帳戶已經連結，您必須選取不同的自動化執行身分帳戶或建立一個帳戶。

    完成所需的資訊之後，請按一下 [建立]。  系統會驗證此資訊，並建立自動化帳戶和執行身分帳戶。  您會自動回到 [OMS 工作區] 刀鋒視窗。  

6. 在 [OMS 工作區] 刀鋒視窗上提供必要資訊之後，按一下 [建立]。  在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。  您會回到 [新增方案] 刀鋒視窗。  

7. 在 [自動化與控制] 設定刀鋒視窗上，確認您要安裝建議的預先選取解決方案。 如果您取消選取任何項目，可以稍後再個別安裝。  

8. 按一下 [建立] 繼續將自動化和 OMS 工作區上架。 系統會驗證所有的設定，然後嘗試在您的訂用帳戶中部署此供應項目。  此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。 

在供應項目上架後，您可以開始建立 Runbook、使用您啟用的管理解決方案，或開始使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics)，以收集您的雲端或內部部署環境中的資源所產生的資料。   

## <a name="next-steps"></a>後續步驟
* 您可藉由檢閱[測試 Azure 自動化執行身分帳戶驗證](automation-verify-runas-authentication.md)，確認新的自動化帳戶可以驗證 Azure 資源。
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。


