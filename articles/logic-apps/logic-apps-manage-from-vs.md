---
title: "在 Visual Studio 中管理邏輯應用程式 - Azure Logic Apps | Microsoft Docs"
description: "使用 Visual Studio Cloud Explorer 管理邏輯應用程式和其他 Azure 資產"
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
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.contentlocale: zh-tw
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>使用 Visual Studio Cloud Explorer 管理邏輯應用程式

雖然 [Azure 入口網站](https://portal.azure.com/)提供一種絕佳的方式讓您能夠設計和管理 Azure Logic Apps，但是，當您使用 Visual Studio Cloud Explorer 時，可以在 Visual Studio 中管理許多 Azure 資產 (包含邏輯應用程式)。 您可以瀏覽已發佈的邏輯應用程式，以及執行像是啟用和停用邏輯應用程式，或是編輯和檢視執行歷程記錄等工作。 

## <a name="installation-steps"></a>安裝步驟

若要安裝和設定適用於 Azure Logic Apps 的 Visual Studio 工具，請遵循下列步驟。

### <a name="prerequisites"></a>必要條件

* [Visual Studio 2015 或 Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Web 的存取權 (若使用內嵌的設計工具)

### <a name="install-visual-studio-tools-for-logic-apps"></a>安裝適用於 Logic Apps 的 Visual Studio 工具

在您安裝必要條件之後︰

1. 開啟 Visual Studio。 在 [工具] 功能表上，選取 [擴充功能和更新]。
2. 展開 [線上] 類別讓您可以在線上搜尋。
3. 瀏覽或搜尋 [Logic Apps]，直到您找出 [Azure Logic Apps Tools for Visual Studio]。
4. 若要下載並安裝擴充功能，按一下 [下載] 按鈕。
5. 在安裝好之後重新啟動 Visual Studio。

> [!NOTE]
> 您也可以直接從 [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9) 下載 Azure Logic Apps Tools for Visual Studio。

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>在 Cloud Explorer 中瀏覽邏輯應用程式

1.    若要開啟 Cloud Explorer，在 [檢視] 功能表上，選擇 [Cloud Explorer]。
2.    依資源群組或資源類型瀏覽邏輯應用程式。 

    如果您要依資源類型進行瀏覽，可選取您的 Azure 訂用帳戶、展開 Logic Apps 區段，然後選取邏輯應用程式。 
    您可以使用滑鼠右鍵按一下邏輯應用程式，從 Cloud Explorer 底部的 [動作] 功能表中進行選擇。

    ![瀏覽邏輯應用程式](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>使用邏輯應用程式設計工具編輯邏輯應用程式

若要在與 Azure 入口網站上使用的相同設計工具中開啟目前已部署的邏輯應用程式，請以滑鼠右鍵按一下您的邏輯應用程式，然後選取 [使用邏輯應用程式編輯器開啟]。 

在設計工具中，您可以編輯邏輯應用程式、將更新儲存到雲端，然後選擇 [執行觸發程序] 來啟動新的執行。

![邏輯應用程式設計工具](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>瀏覽邏輯應用程式執行歷程記錄

若要檢視邏輯應用程式的執行歷程記錄，在邏輯應用程式上按一下滑鼠右鍵，然後選取 [開啟執行歷程記錄]。 若要根據任何顯示的屬性重新排列您的執行歷程記錄，請選取該欄標頭。

![執行記錄](media/logic-apps-manage-from-vs/runs.png)

若要顯示執行個體的執行歷程記錄，讓您可以看見該次執行的結果 (包括每個步驟的輸入和輸出)，請按兩下其中一個執行的執行個體。

![步驟所產生的執行歷程記錄結果、輸入和輸出](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>後續步驟

*    若要開始使用 Azure Logic Apps，請先了解[如何在 Azure 入口網站中建立第一個邏輯應用程式](logic-apps-create-a-logic-app.md)
* [在 Visual Studio 中設計、建置和部署 Logic Apps](logic-apps-deploy-from-vs.md)
* [檢視常見的範例和案例](logic-apps-examples-and-scenarios.md)
* [了解如何使用 Azure Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694)
* [了解如何整合您的系統與 Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

