<properties 
	pageTitle="在 Visual Studio 建置 Logic Apps | Microsoft Azure" 
	description="在 Visual Studio 建立專案，以建立和部署邏輯應用程式。" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="jehollan"/>
	
# 在 Visual Studio 中建置和部署 Logic Apps

雖然 [Azure 入口網站](https://portal.azure.com/)提供了絕佳方法供您設計和管理 Logic Apps，但您也可以改從 Visual Studio 部署邏輯應用程式。Logic Apps 隨附豐富的 Visual Studio 工具組，可讓您使用設計工具建置邏輯應用程式、設定任何部署和自動化的範本，並部署至任何環境。

## 安裝步驟

以下是適用於 Logic Apps 的 Visual Studio 工具的安裝和設定步驟。

### 必要條件

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
- Web 的存取權 (若使用內嵌的設計工具)

### 安裝適用於 Logic Apps 的 Visual Studio 工具

在安裝好必要條件之後，

1. 開啟 Visual Studio 2015 並前往 [工具] 功能表，然後選取 [擴充功能和更新]
1. 選取 [線上] 類別以進行線上搜尋
1. 搜尋 [Logic Apps] 以顯示 [Azure Logic Apps Tools for Visual Studio]
1. 按一下 [下載] 按鈕以下載並安裝擴充功能
1. 在安裝好之後重新啟動 Visual Studio

> [AZURE.NOTE] 您也可以直接從[此連結](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)下載擴充功能

在安裝好之後，您就可以搭配使用 Azure 資源群組專案與邏輯應用程式設計工具。

## 建立專案

1. 移至 [檔案] 功能表，選取 [新增] > [專案] (或者，移至 [加入]，再選取 [新增專案]，以加入至現有的方案)：![[檔案] 功能表](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. 在對話方塊中，尋找 [雲端]，然後選取 [Azure 資源群組]。輸入 [**名稱**]，然後按一下 [**確定**]。![加入新的專案](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. 選取 [邏輯應用程式] 範本。這會建立可供開始使用的空白邏輯應用程式部署範本。![選取 Azure 範本](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. 選取 [範本] 之後，按一下 [確定]。

	邏輯應用程式現在會加入至方案。您在 [方案總管] 中應該會看到部署檔案：

	![部署](./media/app-service-logic-deploy-from-vs/deployment.png)

## 使用邏輯應用程式設計工具

擁有包含邏輯應用程式的 Azure 資源群組專案之後，您可以在 Visual Studio 內開啟設計工具，讓它協助您建立工作流程。設計工具需要網際網路連線，才能查詢連接器以取得可用的屬性和資料 (例如，如果使用 Dynamics CRM Online 連接器，設計工具會查詢 CRM 執行個體以列出可用的自訂和預設屬性)。

1. 以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [使用邏輯應用程式設計工具來開啟] (或 `Ctrl+L`)
1. 選擇部署範本的訂用帳戶、資源群組和位置
	- 請務必注意，在設計邏輯應用程式時將會建立 **API 連線**資源，以在設計期間查詢屬性。所選取的資源群組將會是用來在設計期間建立這些連線的資源群組。您可以前往 Azure 入口網站並瀏覽 **API 連線**，以檢視或修改任何 API 連線。![訂用帳戶選擇器](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. 設計工具應該會根據 `<template>.json` 檔案中的定義來轉譯。
1. 您現在可以建立並設計邏輯應用程式，而部署範本中將會更新變更。![Visual Studio 中的設計工具](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

您也會看到 `Microsoft.Web/connections` 資源將會新增至資源檔，以供邏輯應用程式所需的連線運作。這些連線屬性可以在您部署時設定，並在您於 Azure 入口網站的 [API 連線] 中部署之後進行管理。

### 切換到 JSON 程式碼檢視

您可以選取設計工具底部的 [程式碼檢視] 索引標籤，以切換至以 JSON 格式表示的邏輯應用程式。若要切換回完整資源的 JSON，請以滑鼠右鍵按一下 `<template>.json` 檔案，然後選取 [開啟]。

### 儲存邏輯應用程式

您可以在任何時候透過 [儲存] 按鈕或 `Ctrl+S` 儲存邏輯應用程式。如果在儲存時邏輯應用程式有任何錯誤，這些錯誤會顯示在 Visual Studio 的 [輸出] 視窗。

## 部署邏輯應用程式

最後，設定應用程式之後，只要幾個步驟，就能從 Visual Studio 直接部署。

1. 在方案總管中以滑鼠右鍵按一下專案，移至 [部署] > [新增部署...] ![新增部署](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. 系統會提示您登入 Azure 訂用帳戶。

3. 現在，針對邏輯應用程式要部署到的資源群組，您需要選擇其詳細資料。![部署到資源群組](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    務必為資源群組選取正確的範本和參數檔 (例如，如果是部署至生產環境，請選擇生產參數檔)。
4. 選取 [部署] 按鈕
 
    
6. [輸出] 視窗會顯示部署狀態 (您可能需要選擇 [Azure 佈建]。![輸出](./media/app-service-logic-deploy-from-vs/output.png)

未來，您可以在原始檔控制中修改邏輯應用程式，並利用 Visual Studio 來部署新的版本。

> [AZURE.NOTE] 請注意，如果您直接在 Azure 入口網站中修改定義，則下次從 Visual Studio 部署時會覆寫那些變更。

## 後續步驟

- 若要開始使用 Logic Apps，請遵循[建立 Logic Apps ](app-service-logic-create-a-logic-app.md)教學課程。
- [檢視常見的範例和案例](app-service-logic-examples-and-scenarios.md)
- [您可以使用 Logic Apps 自動化商務程序](http://channel9.msdn.com/Events/Build/2016/T694)
- [了解如何整合您的系統與 Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0727_2016-->