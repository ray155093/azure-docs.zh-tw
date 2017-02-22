---
title: "在 Visual Studio 中建置 Azure Logic Apps | Microsoft Docs"
description: "在 Visual Studio 建立專案，以建立和部署邏輯應用程式。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: db818b2b76d3a8d8c3324c9556237139d319efb9


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>在 Visual Studio 中建置和部署 Logic Apps
雖然 [Azure 入口網站](https://portal.azure.com/)提供了絕佳方法供您設計和管理 Logic Apps，但您也可以改從 Visual Studio 部署邏輯應用程式。  Logic Apps 隨附豐富的 Visual Studio 工具組，可讓您使用設計工具建置邏輯應用程式、設定任何部署和自動化的範本，並部署至任何環境。  

## <a name="installation-steps"></a>安裝步驟
以下是適用於 Logic Apps 的 Visual Studio 工具的安裝和設定步驟。

### <a name="prerequisites"></a>必要條件
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
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

完成安裝之後，您就可以搭配使用 Azure 資源群組專案與邏輯應用程式設計工具。

## <a name="create-a-project"></a>建立專案
1. 移至 [檔案] 功能表，選取 [新增] >  [專案] (或者，移至 [新增]，再選取 [新增專案]，以新增至現有的方案)：[檔案] ![](./media/logic-apps-deploy-from-vs/filemenu.png) 功能表
2. 在對話方塊中，尋找 [雲端]，然後選取 [Azure 資源群組]。 輸入 [名稱]，然後按一下 [確定]。
    ![新增專案](./media/logic-apps-deploy-from-vs/addnewproject.png)
3. 選取 [邏輯應用程式]  範本。 這會建立可供開始使用的空白邏輯應用程式部署範本。
    ![選取 Azure 範本](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)
4. 選取 [範本] 之後，按一下 [確定]。
   
    邏輯應用程式現在會加入至方案。 您在 [方案總管] 中應該會看到部署檔案：  
   
    ![部署](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>使用邏輯應用程式設計工具
擁有包含邏輯應用程式的 Azure 資源群組專案之後，您可以在 Visual Studio 內開啟設計工具，讓它協助您建立工作流程。  設計工具需要網際網路連線，才能查詢連接器以取得可用的屬性和資料 (例如，如果使用 Dynamics CRM Online 連接器，設計工具會查詢 CRM 執行個體以列出可用的自訂和預設屬性)。

1. 以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [使用邏輯應用程式設計工具來開啟] (或 `Ctrl+L`)
2. 選擇部署範本的訂用帳戶、資源群組和位置
   * 請務必注意，在設計邏輯應用程式時將會建立 **API 連線** 資源，以在設計期間查詢屬性。  所選取的資源群組是用來在設計期間建立這些連線的資源群組。  您可以前往 Azure 入口網站並瀏覽 **API 連線**，以檢視或修改任何 API 連線。
   
     ![訂用帳戶選擇器](./media/logic-apps-deploy-from-vs/designer_picker.png)
3. 設計工具應該會根據 `<template>.json` 檔案中的定義來轉譯。
4. 您現在可以建立並設計邏輯應用程式，而部署範本中會更新變更。
    ![Visual Studio 中的設計工具](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

`Microsoft.Web/connections` 資源會新增至資源檔，以供邏輯應用程式所需的所有連線運作。  這些連線屬性可以在您部署時設定，並在您於 Azure 入口網站的 [API 連線] 中部署之後進行管理。

### <a name="switching-to-the-json-code-view"></a>切換到 JSON 程式碼檢視
您可以選取設計工具底部的 [程式碼檢視]  索引標籤，以切換至以 JSON 格式表示的邏輯應用程式。  若要切換回完整資源的 JSON，請以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [開啟]。

### <a name="saving-the-logic-app"></a>儲存邏輯應用程式
您可以在任何時候透過 [儲存] 按鈕或 `Ctrl+S` 儲存邏輯應用程式。  如果邏輯應用程式在儲存時發生任何錯誤，這些錯誤就會顯示在 Visual Studio 的 [輸出] 視窗中。

## <a name="deploying-your-logic-app"></a>部署邏輯應用程式
最後，設定應用程式之後，只要幾個步驟，就能從 Visual Studio 直接部署。 

1. 在方案總管中以滑鼠右鍵按一下專案，移至 [部署]**** > [新增部署...]****
   [新增部署] ![](./media/logic-apps-deploy-from-vs/newdeployment.png)
2. 系統會提示您登入 Azure 訂用帳戶。 
3. 現在，針對邏輯應用程式要部署到的資源群組，您需要選擇其詳細資料。 
    ![部署到資源群組](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > 務必為資源群組選取正確的範本和參數檔 (例如，如果是部署至生產環境，請選擇生產參數檔)。 
   > 
   > 
4. 選取 [部署] 按鈕
5. [輸出] 視窗會顯示部署狀態 (您可能需要選擇 [Azure 佈建]。 
    ![輸出](./media/logic-apps-deploy-from-vs/output.png)

未來，您可以在原始檔控制中修改邏輯應用程式，並利用 Visual Studio 來部署新的版本。 

> [!NOTE]
> 如果您直接在 Azure 入口網站中修改定義，則當您下次從 Visual Studio 部署時，將會覆寫那些變更。
> 
> 

## <a name="adding-a-logic-app-to-an-existing-resource-group-project"></a>將邏輯應用程式新增到現有的資源群組專案
如果您目前具有資源群組專案，則可透過 [JSON 大綱] 視窗，在該專案中新增邏輯應用程式，或是新增另一個邏輯應用程式來與您先前建立的邏輯應用程式並列在一起。
1. 開啟 `<template>.json` 檔案。
2. 開啟 [JSON 大綱] 視窗。  您可以在 [檢視] > [其他視窗] > [JSON 大綱] 中找到 [JSON 大綱] 視窗。
3. 若要將資源新增到範本檔，請按一下 [JSON 大綱] 視窗頂端的 [新增資源] 按鈕，或以滑鼠右鍵按一下 [資源]，然後選取 [加入新資源]。

    ![JSON 大綱](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. 在 [新增資源] 對話方塊中，瀏覽並選取[邏輯應用程式]、為它命名，然後選取 [新增]。

    ![新增資源](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>後續步驟
* 若要開始使用 Logic Apps，請遵循 [建立 Logic Apps](logic-apps-create-a-logic-app.md) 教學課程。  
* [檢視常見的範例和案例](logic-apps-examples-and-scenarios.md)
* [您可以使用 Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694) 
* [了解如何整合您的系統與 Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


