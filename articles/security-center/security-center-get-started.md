---
title: "Azure 資訊安全中心快速入門指南 | Microsoft Docs"
description: "此文章帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/11/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: fcc43e5d98f75b34f2d65c9e1ce8eeba7762caaf
ms.lasthandoff: 04/12/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Azure 資訊安全中心快速入門指南
此文章帶領您認識資訊安全中心的安全性監視和原則管理元件，協助您快速開始使用 Azure 資訊安全中心。

> [!NOTE]
> 此文章將使用範例部署來介紹服務。 此文章不是逐步指南。
>
>

## <a name="prerequisites"></a>必要條件
若要開始使用資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

資訊安全中心的免費層會在您訂用帳戶自動啟用，並提供 Azure 資源安全性狀態的可見度。 它提供基本的安全性原則管理、安全性建議並整合 Azure 合作夥伴的安全性產品和服務。

您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。 若要深入了解 Azure 入口網站，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

## <a name="permissions"></a>權限
在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與 Azure 資源相關的項目。 請參閱[Azure 資訊安全中心的權限](security-center-permissions.md)以深入了解角色與資訊安全中心允許的動作。

## <a name="data-collection"></a>資料收集
資訊安全中心會收集虛擬機器 (VM) 的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。 當您第一次存取資訊安全中心時，訂用帳戶中的所有 VM 都會啟用資料收集。 建議啟用資料收集，但您可以在資訊安全中心原則中關閉資料收集來選擇退出。

下列步驟說明如何存取和使用安全中心的元件。 這些步驟會說明選擇退出時如何關閉資料收集。

## <a name="access-security-center"></a>存取資訊安全中心
在入口網站中，執行下列步驟來存取資訊安全中心：

1. 在 [Microsoft Azure] 功能表中，選取 [資訊安全中心]。

   ![Azure 功能表][1]
2. 如果您是第一次存取資訊安全中心，[歡迎] 刀鋒視窗隨即開啟。 選取 [啟動資訊安全中心]，開啟 [資訊安全中心]刀鋒視窗並啟用資料收集。
   ![歡迎使用畫面][10]
3. 從 [歡迎使用] 刀鋒視窗啟動資訊安全中心，或從 [Microsoft Azure] 功能表中選取 [資訊安全中心] 之後，[資訊安全中心] 刀鋒視窗隨即開啟。 為方便日後快速存取 [資訊安全中心] 刀鋒視窗，請選取 [將刀鋒視窗釘選到儀表板] 選項 (右上方)。
   ![將刀鋒視窗釘選到儀表板選項][2]

## <a name="use-security-center"></a>使用資訊安全中心
您可以設定 Azure 訂用帳戶和資源群組的安全性原則。 一同設定您訂用帳戶的安全性原則：

1. 選取 [資訊安全中心] 刀鋒視窗上的 [原則] 圖格。
   安全性原則![][3]
2. 在 [安全性原則 – 定義每個訂用帳戶或資源群組的原則] 刀鋒視窗上，選取訂用帳戶。
3. 在 [安全性原則] 刀鋒視窗上，[資料收集] 已啟用以自動收集記錄檔。 監視擴充功能會佈建在訂用帳戶的所有目前 VM 和新 VM 上。 (您可以透過將 [資料收集] 設定為 [關閉] 以選擇退出資料收集，但這會讓資訊安全中心無法為您提供安全性警示與建議。)
4. 選取 [安全性原則] 刀鋒視窗上的 [選擇每個區域的儲存體帳戶]。 針對每個有 VM 在其中執行的區域，您需選擇儲存體帳戶，以儲存從這些 VM 收集到的資料。 如果您沒有選擇每個區域的儲存體帳戶，則會為您建立儲存體帳戶，並置於 securitydata 資源群組。 基於安全性考量，收集到的資料在邏輯上會與其他客戶的資料隔離。

   > [!NOTE]
   > 建議您啟用資料收集，並先在訂用帳戶層級選擇儲存體帳戶。 安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但資料收集和儲存體帳戶只可在訂用帳戶層級進行設定。
   >
   >
5. 選取 [安全性原則] 刀鋒視窗上的 [預防原則]。 這會開啟 [預防原則] 刀鋒視窗。
   ![預防原則][4]
6. 在 [預防原則] 刀鋒視窗上，開啟您想要作為安全性原則一部分的建議。 範例：

   * 將 [系統更新] 設定為 [開啟]，會掃描所有支援的虛擬機器，尋找遺漏的 OS 更新。
   * 將 [OS 弱點] 設定為 [開啟]，會掃描所有支援的虛擬機器，識別任何可能讓虛擬機器更容易受到攻擊的 OS 設定。

### <a name="view-recommendations"></a>檢視建議
1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [建議] 圖格。 資訊安全中心會定期分析 Azure 資源的安全性狀態。 當資訊安全中心識別潛在的安全性弱點時，它會在 [建議] 刀鋒視窗上顯示建議。
   ![Azure 資訊安全中心的建議][5]
2. 選取 [建議] 刀鋒視窗上的建議，檢視詳細資訊及/或採取行動以解決問題。

### <a name="view-the-health-and-security-state-of-your-resources"></a>檢視資源的健康狀態及安全性狀態
1. 返回 [資訊安全中心]  刀鋒視窗。 [資源安全性健康狀態] 圖格包含虛擬機器、網路、資料和應用程式的安全性狀態指標。
2. 選取 [計算] 以檢視更多資訊。 [計算] 刀鋒視窗隨即開啟，並顯示三個索引標籤：

  - **概觀** - 包含監視和虛擬機器的建議。
  - **虛擬機器** - 列出所有虛擬機器及其目前的安全性狀態。
  - **雲端服務** - 列出資訊安全中心監視的 Web 和背景工作角色清單。

    ![Azure 資訊安全中心的 [資源健康狀態] 磚][6]

3. 在 [概觀] 索引標籤的 [虛擬機器建議] 下選取建議，以檢視詳細資訊及/或採取行動以設定必要的控制項。
4. 在 [虛擬機器] 索引標籤中選取 VM，以檢視其他詳細資料。

### <a name="view-security-alerts"></a>檢視安全性警示
1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [安全性警示] 圖格。 [安全性警示] 刀鋒視窗會開啟並顯示警示清單。 資訊安全中心透過對安全性記錄檔和網路活動的分析，產生警示。 包含來自整合式合作夥伴解決方案的警示。
   ![Azure 資訊安全中心的安全性警示][7]

   > [!NOTE]
   > 如果已啟用資訊安全中心的標準層，才會出現安全性警示。 標準層有 60 天的免費試用可供使用。 如需有關如何取得標準層級資訊，請參閱[後續步驟](#next-steps)。
   >
   >
2. 選取警示以檢視其他資訊。 在這個例子中，請選取 [探索到修改過的系統二進位檔]。 這會開啟刀鋒視窗，提供有關警示的其他詳細資料。
   ![Azure 資訊安全中心的安全性警示詳細資料][8]

### <a name="view-the-health-of-your-partner-solutions"></a>檢視合作夥伴解決方案的健全狀況
1. 返回 [資訊安全中心]  刀鋒視窗。 [合作夥伴解決方案] 圖格可讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。
2. 選取 [合作夥伴解決方案]  圖格。 隨即開啟一個刀鋒視窗，並顯示已連接到資訊安全中心的合作夥伴解決方案清單。
   ![][9]
3. 選取合作夥伴解決方案。 在本範例中，我們會選取 **QualysVa1** 解決方案。  隨即開啟一個刀鋒視窗，並顯示合作夥伴解決方案的狀態和解決方案相關聯的資源。 選取 [解決方案主控台]  以開啟此解決方案的合作夥伴管理體驗。

## <a name="next-steps"></a>後續步驟
此文章介紹了資訊安全中心的安全性監視和原則管理元件。 現在，您已熟悉資訊安全中心，請嘗試下列步驟︰

* 為您的 Azure 訂用帳戶設定安全性原則。 若要深入了解，請參閱[在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)。
* 使用資訊安全中心的建議來協助您保護 Azure 資源。 若要深入了解，請參閱[管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。
* 檢閱並管理目前的安全性警示。 若要深入了解，請參閱[管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。
* 深入了解 資訊安全中心[標準層](security-center-pricing.md)隨附的[進階威脅偵測功能](security-center-detection-capabilities.md)。 標準層的前 60 天免費。
* 如果您對使用資訊安全中心有問題，請參閱 [Azure 資訊安全中心常見問題集](security-center-faq.md)。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

