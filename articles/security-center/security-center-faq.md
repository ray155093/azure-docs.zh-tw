---
title: "Azure 資訊安全中心常見問題集 (FAQ) | Microsoft Docs"
description: "這個常見問題集回答「Azure 資訊安全中心」的相關問題。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b8a69d89f335c00c5ddc3c201e33a66e1dea1da5
ms.openlocfilehash: fc45634baec1fe5af6cf8f718fb78025dff88a7e


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure 資訊安全中心常見問題集 (FAQ)
這個常見問題集回答「Azure 資訊安全中心」的相關問題，此資訊安全中心是一項針對 Microsoft Azure 資源的安全性提供更深入的洞悉和更佳控制的服務，可協助您預防、偵測及回應威脅。

## <a name="general-questions"></a>一般問題
### <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

### <a name="how-do-i-get-azure-security-center"></a>我要如何取得 Azure 資訊安全中心？
「Azure 資訊安全中心」是藉由您的 Microsoft Azure 訂用帳戶啟用，並可從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取。 ([登入入口網站](https://portal.azure.com)，選取 [瀏覽]，然後捲動至 [資訊安全中心])。  

## <a name="billing"></a>計費
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure 資訊安全中心如何計費？
資訊安全中心提供兩個層級：免費及標準。

免費層可讓您設定安全性原則，並接收安全性警示、事件及建議，可引導您完成設定必要控制項目的程序。 您也可以使用免費層來監視與您的 Azure 訂用帳戶整合的 Azure 資源與合作夥伴解決方案的安全性狀態。

標準層提供免費層的功能，加上進階偵測功能：威脅情報、行為分析、當機分析，和異常偵測。 90 天的免費標準層試用已可使用。 若要升級，請選取 [安全性原則](security-center-policies.md#set-security-policies-for-subscriptions)中的 [定價層]。 若要深入了解，請參閱[資訊安全中心價格](security-center-pricing.md)。

## <a name="data-collection"></a>資料收集
資訊安全中心會收集虛擬機器的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。 當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。 建議啟用資料收集，但您可以在資訊安全中心原則中 [停用資料收集](#how-do-i-disable-data-collection) 來選擇退出。

### <a name="how-do-i-disable-data-collection"></a>我要如何停用資料收集？
您可以隨時在安全性原則中停用訂用帳戶的 [資料收集]  。 ([登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [原則]。)選取訂用帳戶時，會開啟新的刀鋒視窗，並提供您關閉 [資料收集] 的選項。 請選取上方功能區中的 [刪除代理程式]  選項，以從現有的虛擬機器移除代理程式。

> [!NOTE]
> 安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但您必須選取訂用帳戶以關閉資料收集。
>
>

### <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料收集？
您可以在安全性原則中為您的 Azure 訂用帳戶啟用資料收集。 若要啟用資料收集，請[登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [原則]。 將 [資料收集] 設定為 [開啟]，並設定要將資料收集至該處的儲存體帳戶 (請參閱[我的資料會儲存在何處？](#where-is-my-data-stored))。 啟用 [資料收集]  時，它會從訂用帳戶中所有支援的虛擬機器自動收集安全性組態和事件資訊。

> [!NOTE]
> 安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但資料收集只可在訂用帳戶層級進行設定。
>
>

### <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料收集時會發生什麼情況？
資料收集是透過「Azure 監視代理程式」與「Azure 安全性監視」擴充功能來啟用的。 「Azure 安全性監視」擴充功能會掃描各種安全性相關組態，並將其傳送至 [Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 追蹤。 此外，作業系統會建立事件記錄項目。  「Azure 監視代理程式」會讀取事件記錄項目和 ETW 追蹤，並將它們複製到您的儲存體帳戶進行分析。  這是您在安全性原則中設定的儲存體帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱[我的資料會儲存在何處？](#where-is-my-data-stored)問題

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>監視代理程式與安全性監視擴充功能會影響我的伺服器效能嗎？
代理程式與擴充功能只耗用少量的系統資源，對效能的影響應該很小。 如需有關效能影響和代理程式與擴充的詳細資訊，請參閱[規劃和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。

### <a name="where-is-my-data-stored"></a>我的資料會儲存在何處？
針對每個有虛擬機器在其中執行的區域，您需選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。 這可讓您針對隱私權和資料主權的考量，輕鬆將資料保留在相同的地理區域。 您可以在安全性原則中為訂用帳戶選擇儲存體帳戶。 ([登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [原則]。)按一下訂用帳戶時，會開啟新的刀鋒視窗。 選取 [選擇儲存體帳戶]  以選取區域。

> [!NOTE]
> 安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但您的儲存體帳戶區域只可在訂用帳戶層級選取。
>
>

若要深入了解 Azure 儲存體與儲存體帳戶，請參閱[儲存體文件](https://azure.microsoft.com/documentation/services/storage/)和[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

## <a name="using-azure-security-center"></a>使用 Azure 資訊安全中心
### <a name="what-is-a-security-policy"></a>什麼是安全性原則？
安全性原則定義了一組控制項，這組控制項是針對指定之訂用帳戶或資源群組內的資源所建議的。 在「Azure 資訊安全中心」中，您可以根據公司的安全性需求，以及每個訂用帳戶中應用程式的類型或資料的敏感性，為您的 Azure 訂用帳戶和資源群組定義原則。

例如，用於開發或測試的資源與用於實際執行應用程式的資源，兩者的安全性需求可能不同。 同樣地，具有 PII (個人識別資訊) 這類規範資料的應用程式可能會需要較高層級的安全性。 「Azure 資訊安全中心」中啟用的安全性原則將會決定安全性建議與監視。 若要深入了解安全性原則，請參閱 [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。

> [!NOTE]
> 如果訂用帳戶層級原則與資源群組層級原則之間有衝突，將會優先採用資源群組層級原則。
>
>

### <a name="who-can-modify-a-security-policy"></a>誰可以修改安全性原則？
安全性原則是針對每個訂用帳戶或資源群組進行設定。 若要修改訂用帳戶層級或資源群組層級的安全性原則，您必須是該訂用帳戶的「擁有者」或「參與者」。

若要了解如何設定安全性原則，請參閱 [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)。

### <a name="what-is-a-security-recommendation"></a>什麼是安全性建議？
「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。 當發現潛在的安全性弱點時，就會產生相關建議。 這些建議會引導您完成設定所需控制項的程序。 範例包括：

* 佈建反惡意程式碼，以協助識別及移除惡意軟體
* 設定 [網路安全性群組](../virtual-network/virtual-networks-nsg.md) 與規則，以控制對虛擬機器的流量
* 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊
* 部署遺漏的系統更新
* 處理不符合建議基準的作業系統組態

這裡只會顯示 [安全性原則] 中已啟用的建議。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>我要如何查看 Azure 資源目前的安全性狀態？
[資訊安全中心] 刀鋒視窗上的 [資源健康情況] 磚會依虛擬機器、Web 應用程式及其他資源分類，顯示您環境的整體安全性狀態。 每項資源都有一個指標，顯示是否已識別出任何安全性弱點。 按一下 [資源健康情況] 磚會顯示您的資源，並指出需要注意或可能有問題的地方。

### <a name="what-triggers-a-security-alert"></a>什麼會觸發安全性警示？
「Azure 資訊安全中心」會自動收集、分析及整合下列來源的記錄檔資料：Azure 資源、網路，以及反惡意程式碼和防火牆等合作夥伴解決方案。 偵測到威脅時，會建立安全性警示。 偵測範例包括：

* 已受到危害的虛擬機器與已知的惡意 IP 位址進行通訊
* 使用 Windows 錯誤報告偵測到進階的惡意程式碼
* 針對虛擬機器的暴力密碼破解攻擊
* 來自已整合的合作夥伴安全性解決方案 (例如「反惡意程式碼」或「Web 應用程式防火牆」) 的安全性警示

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>在 Microsoft Security Response Center 與 Azure 資訊安全中心，偵測到威脅和收到警示有何差異？
Microsoft Security Response Center (MSRC) 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。 當 MSRC 察覺到有非法或未經授權的合作對象存取客戶資料，或有客戶不遵守可接受用途的條款使用 Azure，安全性事件管理員就會通知客戶。 通知方式通常是傳送電子郵件給 Azure 資訊安全中心中指定的安全性連絡人，如未指定安全性連絡人，則通知 Azure 訂用帳戶擁有者。

資訊安全中心是一項 Azure 服務，它會持續監視客戶的 Azure 環境，以及套用分析自動偵測廣泛的潛在惡意活動。 這些偵測會顯示為資訊安全中心儀表板中的安全性警示。

### <a name="how-are-permissions-handled-in-azure-security-center"></a>在 Azure 資訊安全中心如何處理權限？
「Azure 資訊安全中心」支援角色型存取。 若要深入了解 Azure 中的角色型存取控制 (RBAC)，請參閱 [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

當使用者開啟資訊安全中心時，只會看見與他可存取的資源相關的建議和警示。 這表示使用者只會看到與資源相關的項目，而該資源屬於使用者被指派為「擁有者」、「參與者」或「讀取者」角色的訂用帳戶或資源群組。

如果您需要︰

* **編輯安全性原則**，您必須是訂用帳戶的擁有者或參與者。
* **套用建議**，您必須是訂用帳戶的擁有者或參與者。
* **能看見所有訂用帳戶的安全性狀態**，您必須是每個訂用帳戶的擁有者、參與者或讀取者 (IT 系統管理員、安全性小組)。
* **能看見資源的安全性狀態**，您必須是資源群組的擁有者、參與者或讀取者 (DevOps)。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure 資訊安全中心會監視哪些 Azure 資源？
Azure 資訊安全中心會監視下列 Azure 資源：

* 虛擬機器 (VM) (包括 [雲端服務](../cloud-services/cloud-services-choose-me.md))
* Azure 虛擬網路
* Azure SQL 服務
* 與您的 Azure 訂用帳戶整合的合作夥伴解決方案，例如 VM 和 [App Service 環境](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>虛擬機器
### <a name="what-types-of-virtual-machines-will-be-supported"></a>將會支援哪些類型的虛擬機器？
對於使用 [傳統與 Resource Manager 部署模型](../azure-classic-rm.md)建立的虛擬機器 (VM)，提供安全性健康情況監視和建議。

支援的 Windows VM：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

支援的 Linux VM：

* Ubuntu 版本 12.04、14.04、16.04、16.10
* Debian 版本 7、8
* CentOS 版本 6.\*、7.*
* Red Hat Enterprise Linux (RHEL) 版本 6.\*、7.*
* SUSE Linux Enterprise Server (SLES) 版本 11.\*、12.*
* Oracle Linux 版本 6.\*、7.*

也支援雲端服務中執行的 VM。 只監視生產位置中執行的雲端服務 Web 角色和背景工作角色。 若要深入了解雲端服務，請參閱 [雲端服務概觀](../cloud-services/cloud-services-choose-me.md)。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>為什麼 Azure 資訊安全中心無法辨識我的 Azure VM 上所執行的反惡意程式碼軟體解決方案？
Azure 資訊安全中心僅能辨識透過 Azure 擴充功能安裝的反惡意程式碼軟體。 例如，資訊安全中心無法偵測您在提供的映像中預先安裝的反惡意程式碼軟體，或是您使用自己的程序 (例如組態管理系統) 在虛擬機器上安裝的反惡意程式碼軟體。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>為什麼我會從 VM 收到「遺失掃描資料」訊息？
在 Azure 資訊安全中心中啟用資料收集之後，可能需要花費一些時間 (通常少於一小時) 才能填入掃描資料。 若 VM 處於停止狀態，將不會填入掃描資料。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>為什麼我會收到「VM 代理程式已遺失」訊息？
VM 代理程式必須安裝在 VM 上，才能啟用資料收集。 預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。 如需如何在其他 VM 上安裝 VM 代理程式的資訊，請參閱部落格文章 [VM Agent and Extensions (VM 代理程式和擴充功能)](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。



<!--HONumber=Nov16_HO4-->


