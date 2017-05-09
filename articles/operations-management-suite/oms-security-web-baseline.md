---
title: "Operations Management Suite 安全性和稽核解決方案 Web 基準 | Microsoft Docs"
description: "本文件說明如何針對合規性和安全性目的，使用 OMS 安全性和稽核解決方案來執行所有受監視 Web 的 Web 基準評估。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 安全性和稽核解決方案中的 Web 基準評估
本文件協助您使用 [Operations Management Suite (OMS) 安全性和稽核解決方案](operations-management-suite-overview.md)的 Web 基準評估功能，存取受監視資源的安全狀態。

## <a name="what-is-web-baseline-assessment"></a>什麼是 Web 基準評估？
OMS 安全性目前會提供作業系統的安全性基準評估。 它會每隔 24 小時掃描您伺服器的作業系統設定，並可讓您檢視可能有弱點的設定。 如需詳細資訊，請參閱 [Operations Management Suite 安全性和稽核解決方案中的基準評估](oms-security-baseline.md)。

Web 基準評估的目的是尋找可能有弱點的 Web 伺服器設定。 Web 基準組態的三個主要來源︰.NET、ASP.NET 和 IIS 組態。  如同作業系統基準評估，OMS 安全性將會每隔 24 小時掃描您的 Web 伺服器，並可讓您檢視其安全性狀態。  在 Internet Information Service (IIS) 中，可以高度自訂組態，以便覆寫各種網站和應用程式層級。 除了預設的根層級以外，掃描程式會檢查每個應用程式/網站層級的設定。 這可協助您找出可能有弱點的設定和位置，並且快速修補。


## <a name="web-security-baseline-assessment"></a>Web 安全性基準評估
在此預覽版本中，使用 OMS 搜尋選項將可存取這項功能。 請遵循下列步驟來執行適當的查詢︰

1. 在 [Microsoft Operations Management Suite] 主儀表板內按一下 [安全性和稽核] 圖格。
2. 在 [安全性和稽核] 儀表板中，按一下 [記錄搜尋] 按鈕。
3. 您可以使用的第一個查詢是 **Web 基準評估摘要**。 在 [從這裡開始搜尋] 欄位中，輸入此查詢︰Type=SecurityBaselineSummary BaselineType=web。 以下畫面有輸出範例︰

![Web 基準評估摘要](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> 在此查詢中，每筆記錄都會指出在單一伺服器上的評估摘要。

當您在 [記錄搜尋] 中，您可以輸入不同的查詢，以取得有關 Web 基準評估的詳細資訊。 除了上述查詢，您也可以在此預覽版本中使用下列查詢。

**Web 基準規則評估**︰每筆記錄均代表單一伺服器上的單一 Web 基準規則評估。 它包含規則、位置、預期結果和實際結果的所有資料。

**查詢**：Type=SecurityBaseline BaselineType=web

![Web 基準規則評估](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**顯示特定伺服器的所有結果**︰此查詢會顯示如何查看特定伺服器的結果。

**查詢**：Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1

![所有結果](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

您也可以使用這些記錄/查詢來建立自己的儀表板、報告或警示。 下列畫面有您可以新增儀表板的範例 UI 控制項。 您可以了解如何使用 [這裡](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)的 OMS 檢視表設計工具將您的資料視覺化。 下列畫面是您進行此自訂後，圖格的外觀範例。

![範例 UI](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> 如果您想要知道已針對基準評估進行檢查的設定，您可以下載[這份 Excel 試算表](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690)，其中包含這些設定。

## <a name="see-also"></a>另請參閱
在本文件中，您已了解 OMS 安全性和稽核 Web 基準評估。 若要深入了解 OMS 安全性，請參閱下列文章：

* [Operations Management Suite (OMS) 概觀](operations-management-suite-overview.md)
* [在 Operations Management Suite 安全性和稽核內監視及回應安全性警示](oms-security-responding-alerts.md)
* [在 Operations Management Suite 安全性和稽核解決方案內監視資源](oms-security-monitoring-resources.md)


