---
title: "對 U-SQL 作業進行偵錯 | Microsoft Docs"
description: "了解如何使用 Visual Studio 對失敗的 U-SQL Vertex 進行偵錯。 "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a
ms.lasthandoff: 01/05/2017


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>對 U-SQL 失敗作業的使用者定義 C# 程式碼進行偵錯

了解如何使用 Azure Data Lake Tools for Visual Studio，對具有使用者定義程式碼錯誤的 U-SQL 失敗作業進行偵錯。

## <a name="background"></a>背景

U-SQL 可透過 C# 提供擴充性模型，使用者可以撰寫使用者定義的 C# 程式碼，像是使用者定義的擷取器、歸納器等，來實現更多的擴充性 (深入了解 [U-SQL 使用者定義程式碼](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf))。 不過，每一位開發人員在撰寫程式碼時都難免會出錯，而且因為許多系統僅提供有限的執行階段偵錯資訊 (如記錄檔等)，要在巨量資料系統中進行偵錯並不容易。 

ADL Tools for Visual Studio 提供稱為**頂點失敗偵錯**的功能，透過此功能，您可以輕鬆地將失敗環境 (包括中繼輸入資料和使用者程式碼等) 從雲端複製到本機電腦，以便使用和雲端相同的執行階段和確切輸入資料，來追蹤和偵錯失敗的作業。

下列影片示範 ADL Tools for Visual Studio 中的**頂點失敗偵錯**。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> 如果您沒有下列兩個 Windows 升級，Visual Studio 可能會停止回應或當機：[Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682)、[Universal C Runtime for Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)。
> 
> 

## <a name="prerequisites"></a>必要條件
* 已經閱讀 [開始使用](data-lake-analytics-data-lake-tools-get-started.md) 一文。

## <a name="download-failed-vertex-to-local"></a>將失敗的頂點下載至本機

在 ADL Tools for Visual Studio 中開啟失敗的作業時，您會收到警示。 詳細的錯誤訊息會顯示在 [錯誤] 索引標籤和視窗頂端的黃色警示列。

1. 按一下 [下載]  以下載所有必要的資源和輸入資料流。 如果下載失敗，請按一下 [重試]  。 

2. 在下載完成後按一下 [開啟]，以產生本機偵錯環境。 隨即會自動建立並開啟具有偵錯方案的新 Visual Studio 執行個體。 

3. 失敗的作業與程式碼後置和組件的偵錯步驟略有不同。

    - [偵錯程式碼後置失敗的作業](#debug-job-failed-with-code-behind)
    - [偵錯組件失敗的作業](#debug-job-failed-with-assemblies)

![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 下載 Vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>偵錯程式碼後置失敗的作業

如果您在程式碼後置檔案 (在 U-SQL 專案中通常稱為 「Script.usql.cs」) 中撰寫使用者定義程式碼，之後 U-SQL 作業失敗了，系統會自動將原始程式碼匯入到產生的偵錯方案，您可以逕自使用以 Visual Studio 為基礎的偵錯體驗 (監看式、變數等) 對問題進行疑難排解。 

在進行偵錯之前，請確定您已核取 [例外狀況設定] 視窗中的 [Common Language Runtime 例外狀況] \(**Ctrl + Alt + E**)。

![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. 按 **F5**，程式碼後置程式碼會自動執行，直到例外狀況將其停止。

2. 在專案中開啟 **ADLTool_Codebehind.usql.cs**，設定中斷點，然後按 F5 以逐步偵錯程式碼。

    ![Azure Data Lake Analytics U-SQL 偵錯例外狀況](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>偵錯組件失敗的作業

如果您在 U-SQL 指令碼中使用已註冊的組件，系統將無法自動取得原始程式碼，您必須先進行一些組態設定才能偵錯使用者定義程式碼，也就是說，您必須在自動產生的方案中新增組件的原始程式碼。

### <a name="configure-the-solution"></a>設定方案

1. 以滑鼠右鍵按一下 [方案 'VertexDebug'] > [新增] > [現有專案...]，以尋找組件的原始程式碼，並將專案新增至偵錯方案。

    ![Azure Data Lake Analytics U-SQL 偵錯新增專案](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 在方案中，以滑鼠右鍵按一下 [LocalVertexHost] > [屬性]，並複製**工作目錄**路徑。

3. 以滑鼠右鍵按一下 組件原始程式碼專案 > 屬性，選取位於左側的 建置 索引標籤，將步驟 2 中所複製的路徑貼到 輸出 > 輸出路徑。  

    ![Azure Data Lake Analytics U-SQL 偵錯設定 pdb 路徑](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. 按 **Ctrl + Alt + E**，核取 [例外狀況設定] 視窗中的 [Common Language Runtime 例外狀況]。

### <a name="start-debug"></a>開始偵錯

1. 以滑鼠右鍵按一下 [組件原始程式碼專案] > [重建]，將 pdb 檔案輸出至 LocalVertexHost 工作目錄。

2. 按 **F5**，專案將會自動執行，直到例外狀況將其停止。 第一次您可能會看到下列可忽略的訊息。 移至 [偵錯] 畫面最多可能需要一分鐘的時間。

    ![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 開啟您的原始程式碼並設定中斷點，然後按 **F5** 逐步偵錯程式碼。

您也可以使用其他以 Visual Studio 為基礎的偵錯體驗 (監看式、變數等) 對問題進行偵錯。 

**請注意**，每次修改程式碼時都必須重建組件來源程式碼專案，以便讓新的 pdb 檔案生效。

在順利完成偵錯後，輸出視窗會顯示下列訊息：

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL 偵錯成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>重新提交作業
完成偵錯之後，您可以重新提交失敗的作業。

1. 將新的 .dll 組件註冊到您的 ADLA 資料庫。

    1. 從 [伺服器總管/Cloud Explorer] 展開 [ADLA 帳戶] > [資料庫] 節點。
    2. 以滑鼠右鍵按一下 [組件] 以註冊組件。 
    3. 將新的 .dll 組件註冊到 ADLA 資料庫。
    ![Azure Data Lake Analytics U-SQL 偵錯註冊組件](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. 或將 C# 程式碼複製回到 script.usql.cs-C# 程式碼後置檔案。
3. 重新提交您的作業。

## <a name="next-steps"></a>後續步驟

* [U-SQL 可程式性指南](data-lake-analytics-u-sql-programmability-guide.md)
* [針對 Azure 資料湖分析工作開發 U-SQL 使用者定義運算子](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [教學課程：開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)
* [教學課程：使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)



