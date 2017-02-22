---
title: "在 Data Lake Tools for Visual Studio 中使用頂點執行檢視 | Microsoft Docs"
description: "了解如何使用頂點執行檢視測驗 Data Lake Analytics 作業。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>在適用於 Visual Studio 的 Data Lake 工具中使用 Vertex 執行檢視
了解如何使用頂點執行檢視測驗 Data Lake Analytics 作業。

## <a name="prerequisites"></a>必要條件
* 使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼的一些基本知識。  請參閱[教學課程：使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="open-the-vertex-execution-view"></a>開啟頂點執行檢視
對於某些作業，您可以按一下左下角的 [頂點執行檢視] 連結。 系統可能會提示您先載入設定檔，可能需要一些時間，視您的網路連線而定。

![Data Lake Analytics 工具頂點執行檢視](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>了解頂點執行檢視
進入頂點執行檢視之後，有三個部分︰

![Data Lake Analytics 工具頂點執行檢視](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* 頂點選取器︰左邊是頂點選取器。  您可以根據功能選取頂點 (例如前 10 個讀取的資料，或根據階段選擇)。
  
    其中一個最常使用的篩選器是關鍵路徑的頂點。 關鍵路徑是 U-SQL 作業的最長路徑。 它可用於最佳化您的作業，方法是檢查哪一個頂點耗用最長時間。
* 正上方窗格︰
  
    ![Data Lake Analytics 工具頂點執行檢視](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    此檢視也會顯示所有頂點的執行狀態。 據以將時間轉換為您的本機電腦，並且以不同的色彩顯示不同的狀態。
* 正下方窗格：
  
    ![Data Lake Analytics 工具頂點執行檢視](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
  * 處理序名稱︰頂點執行個體的名稱。 由 StageName|VertexName|VertexRunInstance 中的不同部分組成。 例如，SV7_Split[62].v1 頂點表示階段 SV7_Split 中第 62 號頂點的第二個執行中執行個體 (.v1，從 0 開始編制索引)。
  * 總資料讀取/寫入︰此頂點讀取/寫入的資料。
  * 狀態/結束狀態︰頂點結束時的最終狀態。
  * 結束代碼/失敗類型︰頂點失敗時的錯誤。
  * 建立原因︰為什麼建立頂點。
  * 資源延遲/處理序延遲/PN 佇列延遲︰頂點等待資源、處理資料以及保持在佇列中所花費的時間。
  * 處理序/建立者 GUID：目前執行的頂點或其建立者的 GUID。
  * 版本︰執行中頂點的第 N 個執行個體 (系統可能會針對許多原因排程頂點的新執行個體，例如容錯移轉、計算備援等等。)
  * 版本建立時間。
  * 處理序建立開始時間/處理序佇列時間/處理序開始時間/處理序完成時間：頂點處理序開始建立時；頂點處理序開始佇列時；特定頂點處理序開始時；特定頂點完成時。

## <a name="next-steps"></a>後續步驟
* 若要取得資料湖分析概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
* 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 若要記錄診斷資訊，請參閱 [為 Azure Data Lake Analytics 存取診斷記錄檔](data-lake-analytics-diagnostic-logs.md)
* 若要了解更複雜的查詢，請參閱 [使用 Azure 資料湖分析來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
* 若要檢視作業詳細資料，請參閱[針對 Azure Data Lake Analytics 作業使用作業瀏覽器和作業檢視](data-lake-analytics-data-lake-tools-view-jobs.md)
* 若要了解 Data Lake Tools for Visual Studio Code，請參閱 [Use the Azure Data Lake Tools for Visual Studio Code (使用 Azure Data Lake Tools for Visual Studio Code)](data-lake-analytics-data-lake-tools-for-vscode.md)。



<!--HONumber=Nov16_HO4-->


