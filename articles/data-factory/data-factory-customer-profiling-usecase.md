---
title: "使用案例 - 客戶分析"
description: "了解如何使用 Azure Data Factory 建立資料驅動的工作流程 (管線) 以分析遊戲客戶。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a3eb126aa508204becbebb3dd763bdfde5890c8a


---
# <a name="use-case---customer-profiling"></a>使用案例 - 客戶分析
Azure Data Factory 是許多服務之一，可用來實作解決方案加速器的 Cortana Intelligence 套件。  如需 Cortana Intelligence 的詳細資訊，請瀏覽 [Cortana Intelligence 套件](http://www.microsoft.com/cortanaanalytics)。 在本文中，我們會說明簡單的使用案例，以幫助您開始著手了解 Azure Data Factory 如何解決常見的分析問題。

若要存取並嘗試這個簡單的使用案例，您只需要 [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。  您可以藉由遵循 [範例](data-factory-samples.md) 一文所述的步驟，部署實作此使用案例的範例。

## <a name="scenario"></a>案例
Contoso 是為多個平台建立遊戲的遊戲公司，包含遊戲主機、手持裝置與個人電腦 (PC)。 當玩家玩這些遊戲時，會產生大量追蹤使用模式、遊戲風格與使用者喜好設定的記錄檔資料。  結合人口統計、區域和產品資料時，Contoso 可以執行分析，引導他們如何增強每位玩家的體驗，並使他們達到升級及在遊戲中購買的目標。 

Contoso 的目標是要根據其玩家的遊戲歷程記錄識別向上銷售/交叉銷售機會，並新增強大功能來推動業務成長以及為客戶提供更好的體驗。 在此使用案例中，我們使用遊戲公司做為企業範例。 該公司想要根據玩家的行為最佳化其遊戲。 這些原則適用於所有想要建立客戶及產品和服務之關聯，並強化其客戶體驗的所有企業。

## <a name="challenges"></a>挑戰
## <a name="solution-overview"></a>解決方案概觀
這個簡單的使用案例可以當做您如何使用 Azure Data Factory 擷取、準備、轉換、分析和發佈資料的範例。

![端對端工作流程](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

此圖說明部署資料管線之後，其會如何出現在 Azure 入口網站中。

1. **PartitionGameLogsPipeline** 從 Blob 儲存體讀取原始遊戲事件，並根據年、月和日建立分割區。
2. **EnrichGameLogsPipeline** 聯結分割的遊戲事件與地區代碼參考資料，並將 IP 位址對應到相對應的地理位置來充實資料。
3. **AnalyzeMarketingCampaignPipeline** 管線使用已充實的資料並加上廣告資料來處理，以建立包含行銷活動成效的最終輸出。

在此範例中，Data Factory 可用來協調數個活動，包括複製輸入資料、轉換和處理資料，以及將最後的資料輸出至 Azure SQL Database。  您也可以視覺化資料管線的網路、管理它們，並從 UI 監視其狀態。

## <a name="benefits"></a>優點
藉由最佳化其使用者設定檔分析並將其與企業目標對齊，遊戲公司可以快速收集使用模式，並分析其行銷活動的效益。




<!--HONumber=Nov16_HO3-->


