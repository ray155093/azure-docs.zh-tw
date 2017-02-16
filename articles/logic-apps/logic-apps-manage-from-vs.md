---
title: "從 Visual Studio Cloud Explorer 管理 Azure Logic Apps |Microsoft 文件"
description: "從 Visual Studio Cloud Explorer 管理 Azure Logic Apps。"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>使用 Visual Studio Cloud Explorer 管理 Azure Logic Apps
雖然 [Azure 入口網站](https://portal.azure.com)可為您提供一種絕佳方式來設計和管理 Logic Apps，但是 Visual Studio Cloud Explorer 讓您能夠從 Visual Studio 管理許多 Azure 資產 (包含 Logic Apps)。  利用 Cloud Explorer，您可以瀏覽已發佈的 Logic Apps，執行像是啟用、停用、編輯和檢視執行歷程記錄等動作。 

## <a name="installation-steps"></a>安裝步驟
以下是適用於 Logic Apps 的 Visual Studio 工具的安裝和設定步驟。

### <a name="prerequisites"></a>必要條件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Web 的存取權 (若使用內嵌的設計工具)

### <a name="install-visual-studio-tools-for-logic-apps"></a>安裝適用於 Logic Apps 的 Visual Studio 工具
在安裝好必要條件之後， 

1. 開啟 Visual Studio 2015 並前往 [工具] 功能表，然後選取 [擴充功能和更新]
2. 選取 [線上]  類別以進行線上搜尋
3. 搜尋 [Logic Apps] 以顯示 [Azure Logic Apps Tools for Visual Studio]
4. 按一下 [下載]  按鈕以下載並安裝擴充功能
5. 在安裝好之後重新啟動 Visual Studio

> [!NOTE]
> 您也可以直接從 [此連結](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>瀏覽 Logic Apps
若要在 Cloud Explorer 中瀏覽 Logic Apps，請在 [檢視] > [Cloud Explorer] 下方開啟 Cloud Explorer，而您可以依資源群組或資源類型進行瀏覽。  如果要依資源類型進行瀏覽，可選取訂用帳戶，接著展開 [Logic Apps] 區段，然後選取邏輯應用程式。  您可以使用滑鼠右鍵按一下您的其中一個邏輯應用程式，或使用 Cloud Explorer 底部的 [動作] 功能表來執行所需的動作。

![瀏覽](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>使用設計工具編輯邏輯應用程式
若要在與 Azure 入口網站上相同的設計工具中開啟目前已部署的邏輯應用程式，請以滑鼠右鍵按一下邏輯應用程式，然後選取 [使用邏輯應用程式編輯器開啟]。  使用設計工具，您可以編輯邏輯應用程式並將它存回雲端，然後使用 [執行觸發程序] 按鈕來啟動新的執行。

![設計工具](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>瀏覽邏輯應用程式執行歷程記錄
若要列出邏輯應用程式的執行歷程記錄，請在邏輯應用程式上按一下滑鼠右鍵，然後選取 [開啟執行歷程記錄]。  在這個檢視中，您可以藉由選取資料行標頭，依任何顯示的屬性來排序。  

![執行](media/logic-apps-manage-from-vs/runs.png)

按兩下其中一個執行的執行個體，即會顯示該執行個體的執行歷程記錄，您可以在其中看見該次執行的結果 (包括每個步驟的輸入和輸出)。

![歷程記錄](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>後續步驟
* 若要開始使用 Logic Apps，請遵循 [建立 Logic Apps](logic-apps-create-a-logic-app.md) 教學課程。  
* [檢視常見的範例和案例](logic-apps-examples-and-scenarios.md)
* [您可以使用 Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694) 
* [了解如何整合您的系統與 Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


