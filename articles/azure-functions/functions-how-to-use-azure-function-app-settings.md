---
title: "設定 Azure 函數應用程式的設定 | Microsoft Docs"
description: "了解如何設定 Azure Functions 應用程式設定。"
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7334f68e4526b716210b28388afcd0ee6e48940
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-configure-azure-function-app-settings"></a>如何設定 Azure Functions 應用程式設定
## <a name="settings-overview"></a>設定概觀
您可以在入口網站左下角按一下 [函式應用程式設定] 連結來管理 Azure Functions 應用程式設定。 Azure 函式應用程式設定會套用到應用程式中的所有函式。

1. 移至 [Azure 入口網站](http://portal.azure.com) ，然後以您的 Azure 帳戶登入。
2. 在入口網站的左下角按一下 [函式應用程式設定]。 這個動作會顯示可供選擇的數個組態選項。 

![Azure 函式應用程式設定](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="develop"></a>開發
### <a name="app-service-editor"></a>App Service 編輯器
「App Service 編輯器」是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 GitHub 整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="application-settings"></a>應用程式設定
管理環境變數、Framework 版本、遠端偵錯、應用程式設定、連接字串、預設文件等。這些設定是您的函式應用程式特有的。 

若要設定應用程式設定，請按一下 [設定應用程式設定] 連結。 

![設定 App 設定](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="dev-console"></a>開發人員主控台
您可以使用 Azure Functions 入口網站內主控台來執行 DOS 樣式命令。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 

> [!NOTE]
> 您可以上傳指令碼，但您必須先在 Azure Functions 的 [進階設定]中設定 FTP 用戶端。
> 
> 

若要開啟入口網站內主控台，請按一下 [開啟開發人員主控台]。

![設定函式應用程式記憶體大小](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> 在具有類似 ASCII 圖形的主控台中工作，讓您看起來更酷。
> 
> 

## <a name="deploy"></a>部署
### <a name="continuous-integration"></a>持續整合
您可以將函式應用程式與 GitHub、Visual Studio Team Services 等等整合。

1. 按一下 [設定連續整合] 連結。 這會開啟具有選項的 [部署] 窗格。
2. 在 [部署] 窗格中按一下 [安裝]，即可顯示 [部署來源] 窗格並具有一個選項︰按一下 [選擇來源] 可顯示可用的來源。 
3. 選擇任何可用的部署來源︰按一下 Visual Studio Team Services、OneDrive、本機 Git 儲存機制、GitHub、Bitbucket、DropBox 或外部儲存機制。 
   
    ![設定函數應用程式的 CI](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. 依照各種部署來源的提示，輸入您的認證和資訊。 視您選擇的來源，要求的認證與資訊可能稍有不同。 

一旦設定 CI，您推送至所設定來源的已連接程式碼會自動部署到此函式應用程式。

### <a name="kudu"></a>Kudu
Kudu 可讓您存取函式應用程式的進階系統管理功能。

若要開啟 Kudu，請按一下 [前往 Kudu]。 這個動作會開啟 Kudu Web 管理的全新瀏覽器視窗。

> [!NOTE]
> 您也可以在函式的 URL 中插入 "scm" 來啟動 **Kudu**，如下所示︰```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

透過 Kudu 網頁，您可以檢視和管理系統資訊、應用程式設定、環境變數、HTTP 標頭、伺服器變數等等。

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="manage-app-service-settings"></a>管理：App Service 設定
以管理任何其他 App Service 執行個體的方式來管理您的函數應用程式。 此選項可讓您存取所有先前所述的設定，還有數個設定。  

若要開啟進階設定，請按一下 [進階設定] 連結。 

![設定 App Service 設定](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

如需有關如何設定每個 App Service 設定的詳細資料，請參閱[設定 Azure App Service 設定](../app-service-web/web-sites-configure.md)。

## <a name="manage-cors"></a>管理：CORS
一般來說，基於安全性理由，不會允許從外部來源對您的主機 (網域) 呼叫，例如來自瀏覽器的 Ajax 呼叫。 否則，可能會將惡意程式碼傳送後端並執行。 那麼，最安全的路由則是將所有程式碼來源加入黑名單，除了少數您自己的受信任的來源。 您可以透過設定跨原始來源資源分享 (CORS)，在 Azure Functions 中設定要接受呼叫的來源。 CORS 可讓您列出可以在 Azure Functions 應用程式中呼叫函式、屬於 JavaScript 來源的網域。 

1. 若要設定 CORS，請按一下 [設定 CORS] 連結。 
2. 輸入您想要列入白名單的網域。

![設定函數應用程式的 CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="manage-authenticationauthorization"></a>管理：驗證/授權
若為使用 HTTP 觸發程序的函式，您可要求驗證呼叫。

1. 若要設定驗證，請按一下 [設定驗證] 連結。
2. 將 [應用程式服務驗證] 按鈕切換為 [開啟]。

![設定函數應用程式的 CI](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

大部分的驗證提供者會要求 API 金鑰/用戶端識別碼和密碼；不過，Microsoft 帳戶、Facebook 選項也可讓您定義範圍 (特定的權認證)。 Active Directory 有數個可以設定的快速或進階組態設定。

如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/app-service-authentication-overview.md)。

## <a name="manage-api-definition"></a>管理：API 定義
讓用戶端能更輕鬆地使用您的 HTTP 觸發函式。

1. 若要設定 API，請按一下 [設定 API 中繼資料]。 
2. 輸入指向 Swagger json 檔案的 URL。

![設定函數應用程式的 API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

如需有關如何使用 Swagger 建立 API 定義的詳細資訊，請瀏覽[在 Azure 中開始使用 API Apps、ASP.NET 和 Swagger](../app-service-api/app-service-api-dotnet-get-started.md)。

## <a name="daily-usage-quota"></a>每日使用量配額

Azure Functions 可讓您藉由設定每日花費配額，以可預測的方式限制平台使用量。 一旦達到每日花費配額，系統就會停止「函數應用程式」。 針對因達到花費配額而停止運作的「函數應用程式」，您可以從與建立每日花費配額相同的內容中予以重新啟用。 花費配額的單位就是計費單位：GB-s (GB-秒)，如需有關計費模型的詳細資料，請參閱 [Azure Functions 定價頁面](http://azure.microsoft.com/pricing/details/functions/)。 

![設定函式應用程式記憶體大小](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-quota.png)

## <a name="next-steps"></a>後續步驟
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


