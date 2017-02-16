---
title: "資訊安全中心價格 |Microsoft Docs"
description: "這篇文章提供 Azure 資訊安全中心的價格資訊。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 533fd337ba16a61579b6f322771913cda36d8272


---
# <a name="azure-security-center-pricing"></a>Azure 資訊安全中心價格
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## <a name="pricing-tiers"></a>定價層
資訊安全中心提供兩個層級：

* **免費層**在所有 Azure 訂用帳戶上自動啟用。 免費層提供 Azure 資源安全性狀態的可見度、基本安全性原則、安全性建議以及與合作夥伴的安全性產品和服務整合。
* **標準層**新增進階威脅偵測功能，包括威脅智慧、行為分析、異常偵測、安全性事件，以及威脅評估報告。 標準層可享 **90 天免費試用**。

如需詳細資訊，請參閱資訊安全中心[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 資訊安全中心使用 Azure 儲存體來儲存產生自受保護節點的安全性資料。 與此儲存體相關的費用不包含在服務價格中，而將以一般 [Azure 儲存體費率](https://azure.microsoft.com/pricing/details/storage/blobs/)分別計費。 儲存體費用在試用期間同樣適用。
> 
> 

## <a name="try-standard-free-for-90-days"></a>免費試用 90 天的標準
標準層可享 90 天免費試用。 若要取得免費試用版的「標準」層，請選取 [資訊安全中心] 刀鋒視窗上的 [原則] 圖格。 選取您要升級至「標準」的訂用帳戶。 在 [安全性原則] 刀鋒視窗中，選取 [定價層]。 在 [選擇定價層] 刀鋒視窗中，選取 [標準 – 免費試用]。

![免費試用][1]

在 90 天結束時，如果您選擇繼續使用服務，我們將自動開始針對使用量計費。

## <a name="upgrade-to-standard"></a>升級至標準
升級至「標準」層以新增進階威脅偵測。 若要取得「標準」層，請選取 [資訊安全中心] 刀鋒視窗上的 [原則] 圖格。 選取您要升級至「標準」的訂用帳戶。 在 [安全性原則] 刀鋒視窗中，選取 [定價層]。 在 [選擇定價層] 刀鋒視窗中，選取 [標準]。

![標準層][2]

## <a name="why-upgrade-to-standard"></a>為何要升級至標準？
「標準」層的資訊安全中心提供「免費」層的所有功能，再加上進階威脅偵測。 進階威脅偵測可協助識別以您的 Azure 資源為目標的作用中威脅，並提供您迅速回應所需的深入資訊。

資訊安全中心會運用進階安全性分析，其遠勝於以簽章為基礎的方法。 巨量資料和機器學習技術突破可用來評估整個雲端網狀架構的事件 – 使用手動方式來偵測無法識別的威脅，以及預測攻擊的演化。

「標準」層隨附的安全性分析為︰

* **威脅情報** - 利用 Microsoft 產品和服務、Microsoft 數位犯罪防治中心、Microsoft Security Response Center 以及外部摘要的全域威脅情報，尋找已知的不良執行者
* **行為分析** - 套用已知模式來探索惡意行為
* **異常偵測** - 使用統計剖析來建置歷程基準。 它會對偏離已確立基準 (符合潛在攻擊向量) 的情況提出警示

在以下的 [安全性警示] 刀鋒視窗中，資訊安全中心偵測到安全性**事件**。 安全性事件是符合攻擊鏈模式之資源的所有警示彙總。 選取安全性事件會顯示關於該事件的更多詳細資料，並列出相關警示。 選取警示提供有關該出現次數的詳細資訊。

![安全性事件][3]

以下的**網路通訊**警示提供有關警示的詳細資料。 詳細資料包括其完整的描述、其嚴重性、其目前狀態 (在此案例中可關閉，表示使用者採取動作來關閉它)，受到攻擊的資源，以及修復步驟。 另外還有一個 Microsoft 威脅情報報告連結的清單。 這些報告可以用於安全性補救和防禦。

![安全性警示詳細資料][4]

## <a name="enable-data-collection"></a>啟用資料收集
若要啟用虛擬機器行為分析，則必須開啟資料收集。 您可以在第一次存取資訊安全中心，或當您建立安全性原則時啟用資料收集。

若要驗證資料收集是否已啟用，請選取 [原則] 圖格。 [安全性原則] 刀鋒視窗隨即開啟，列出您的 Azure 訂用帳戶。 選取一個訂用帳戶。 如果 [資料收集] 關閉，請將它變更為開啟並儲存變更。 請參閱[在 Azure 資訊安全中心啟用資料收集](security-center-enable-data-collection.md)。

## <a name="next-steps"></a>後續步驟
* 在本文件中，已向您介紹資訊安全中心的價格。 如需價格的詳細資訊，請參閱資訊安全中心[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。
* 若要深入了解資訊安全中心的進階偵測功能，請參閱 [Azure 資訊安全中心的偵測功能](security-center-detection-capabilities.md)。
* 如果您對使用資訊安全中心有問題，請參閱 [Azure 資訊安全中心常見問題集](security-center-faq.md)。
* 如果您仍然有關於使用資訊安全中心或 Azure 方面的問題，請造訪 [Azure 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc)。

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png



<!--HONumber=Dec16_HO2-->


