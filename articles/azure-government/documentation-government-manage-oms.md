---
title: Azure Government Operations Management Suite | Microsoft Docs
description: "本文說明 Operations Management Suite 中的 Log Analytics 如何適用於美國政府機構和解決方案提供者"
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Azure Government 網路安全性︰使用 Operations Management Suite 監視和保護資產 

## <a name="cybersecurity-in-the-cloud"></a>雲端的網路安全性
我們的客戶在轉移到雲端時的一個重要考量是，仍然可以為其部署至雲端的 Azure Government 服務保有資產管理和安全性。 虛擬機器防火牆必須正確設定。 虛擬網路必須套用正確的網路安全性群組。 資產的存取權必須在正確的時間鎖定。 您必須規劃、設計和佈建所有這些必要工作，讓您的機構能夠使用安全的基礎結構。

設定這類環境可能十分困難。 讓您的伺服器團隊上線運作任何監視服務，是一項很難調整的作業，而且要更新監視服務也並非易事。 在不同雲端提供者，以及跨越雲端和內部部署上監視基礎結構並不容易。 最後，要讓監視隨時保持最新狀態，並啟用 Application Insights 以對網路安全性威脅進行監視、偵測、警示和反擊，則需要時間、資源和計算能力。

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
現已可供 Azure Government 使用的 Microsoft Operations Management Suite 是一組 Azure 服務，可讓您快速且輕鬆地執行這些工作。 本文著重在使用 Log Analytics，其使用超大規模的記錄搜尋來快速分析資料，並揭露環境中的威脅。

Azure Log Analytics 可以︰

* 在 Azure、其他雲端提供者和內部部署將代理程式部署到個別 VMS (Linux 和 Windows)。
* 透過 Azure Government 儲存體帳戶或 System Center Operations Manager 端點，將您現有的記錄檔與現有的記錄資料連線。

讓我們來探究要如何將 Log Analytics 整合至伺服器團隊，並看看一些現成可解決我們在此所述之考量的解決方案。

## <a name="onboarding-servers-to-log-analytics"></a>將伺服器上架到 Log Analytics
在整合雲端資產與 Log Analytics 時的第一步，是在記錄來源安裝 Log Analytics 代理程式。 如果是虛擬機器，此程序非常簡單，因為您可以手動從 Log Analytics 入口網站下載該代理程式。

![圖 1：Windows 伺服器連線到 Operations Management Suite](./media/documentation-government-oms-figure1.png)
<p align="center">圖 1：Windows 伺服器連線到 Log Analytics</p>

您可以透過 Azure 入口網站直接將 Azure VM 連線到 Log Analytics。 如需指示，請參閱[在 Azure VM 上啟用 Log Analytics 的新方法](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/)。

您也可以程式設計方式將它們連線，或直接在您的 Azure Resource Manager 範本中設定 Log Analytics 虛擬機器擴充功能。 Windows 架構機器的指示請參閱[將 Windows 電腦連接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents)，Linux 架構機器的指示則請參閱[將 Linux 電腦連接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents)。

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>將儲存體帳戶和 Operations Manager 上架到 Log Analytics
Log Analytics 也可以連線到您的儲存體帳戶和/或現有的 System Center Operations Manager 部署，提供您在混合案例 (跨雲端提供者，或在雲端/內部部署基礎結構) 的操作管理。

![圖 2︰將 Azure 儲存體和 Operations Manager 連線到 Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">圖 2︰將 Azure 儲存體和 Operations Manager 連線到 Log Analytics</p>

Log Analytics 也支援從其他監視服務 (例如 Chef 或 Puppet) 收集記錄資訊。 此外，對於 Azure 部署，我們擁有的 VM 具有已啟用 Log Analytics 的 Azure Resource Manager 範本，因此您可以同時部署計算並上架至 Log Analytics 工作區。

![圖 3：有 Log Analytics VM 擴充之 Azure VM 的 Azure Resource Manager 範本](./media/documentation-government-oms-figure3a.png)
![圖 3：有 Log Analytics VM 擴充之 Azure VM 的 Azure Resource Manager 範本](./media/documentation-government-oms-figure3b.png)
<p align="center">圖 3：有 Log Analytics VM 擴充之 Azure VM 的 Azure Resource Manager 範本</p>

如需使用現有 Operations Manager 實作內部部署設定 Log Analytics 的相關資訊，請參閱[將 Operations Manager 連接到 Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents)。

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>透過 Operations Management Suite 解決方案套件運用情報
在擁有了各種不同的資料記錄來源後，您接下來必須讓這些資料全都發揮作用。

Log Analytics 的核心是一種記錄搜尋服務，可讓您撰寫功能強大的查詢，快速地搜尋數千或甚至數百萬的記錄檔。 不過，找出您要撰寫查詢的問題則很困難。

輸入 Operations Management Suite 解決方案。 這些解決方案是查詢套件，與 Log Analytics 原生整合在一起，以主動讓您深入了解 Log Analytics 管理的伺服器團隊。

關於網路安全性的主題，我會簡短介紹 Log Analytics 可立即為您解決的三種網路安全性案例。

### <a name="antimalware-assessment"></a>反惡意程式碼評估
反惡意程式碼評估提供您一組定義好的查詢、通知和監視儀表板，讓您可以一覽伺服器團隊防範惡意程式碼的成效。

此儀表板會提供含有四項資訊的清單︰
* 具有作用中和/或已修復威脅的任何伺服器。
* 目前偵測到的威脅。
* 沒有完全受到保護的電腦。 Log Analytics 會耙梳電腦的記錄檔來找出此資訊，以尋找是否有任何正開啟的 FW 站台或一般的網頁瀏覽器中是否有設定不正確的規則。
* 分析伺服器目前如何受到保護，例如是由原生 Windows OS 病毒防護或 System Center Endpoint Protection 之類的解決方案所保護。

例如，您可以看到下列威脅已由 Systems Center 攔截到並自動分級︰

![圖 4：Operations Management Suite 反惡意程式碼評估解決方案](./media/documentation-government-oms-figure4.png)
<p align="center">圖 4：Operations Management Suite 反惡意程式碼評估解決方案</p>

如需反惡意程式碼評估的詳細資訊，請參閱 [Log Analytics 中的惡意程式碼評估方案](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)一文。

### <a name="identity-and-access"></a>身分識別與存取
雲端中另一個常見的網路安全性案例是關於認證遭到洩漏。 不只您的雲端訂用帳戶有認證，每個個別的 VM 也有相關聯的使用者和/或密碼 (通常是憑證或密碼)。

Log Analytics 會組織伺服器團隊中所有的登入嘗試，並且根據類型 (遠端、本機、使用者名稱等) 來分類儲存。 例如在下列範例中，我可以看到有大量的登入失敗嘗試，以使用者名稱做為大量的隨機字串。 這表示我的電腦很可能已被公開且未正確受到防火牆和存取控制清單保護。

![圖 5：過去 24 小時內有 97.3% 的登入失敗](./media/documentation-government-oms-figure5.png)
<p align="center">圖 5：過去 24 小時內有 97.3% 的登入失敗</p>

### <a name="threat-intelligence"></a>威脅情報
Log Analytics 也能夠防範惡意的內部人員案例，例如當組織內有安全性危害，並且有惡意的使用者正嘗試洩漏資料時。

Log Analytics 威脅情報會查看您電腦上的所有網路記錄檔並自動搜尋和通知您已知惡意 IP (例如未編製索引之黑暗網路的 IP 位址) 的輸入/輸出網路連線。

例如在下列螢幕擷取畫面中，我可以看到連線到中華人民共和國的輸入和輸出兩種網路連線。

按兩次輸入標記，我發現某個受 Log Analytics 管理的 Linux VM 正對中國已知的黑暗網路 IP 位址進行輸出連線。

您也可以對威脅情報之類的 Operations Management Suite 解決方案設定警示。 在下列螢幕擷取畫面中，我設定了警示，所以只要 Log Analytics 偵測到超過 10 個對已知惡意 IP 位址的輸出連線，就會透過電子郵件寄送警示給我。 然後我設定該警示引發 Azure 自動化作業，也就是設定自動關閉該 VM。

![圖 6：Operations Management Suite 警示和自動化](./media/documentation-government-oms-figure6.png)
<p align="center">圖 6：Operations Management Suite 警示和自動化</p>

無論解決方案是在 Azure、另一個雲端服務提供者或在內部部署中執行，這只是一個可套用到您團隊的內建 Operations Management Suite 解決方案範例。

Operations Management Suite 會繼續更新其機器學習服務，來自動為您對抗最新的威脅，我們也會繼續在 Azure Marketplace 推出新的解決方案。

如需 Operations Management Suite 的詳細資訊，請參閱[我們的文件頁面](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/)。



<!--HONumber=Jan17_HO1-->


