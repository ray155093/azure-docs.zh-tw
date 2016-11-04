---
title: 教學課程：在 PowerApps 和邏輯流程中使用 ASP.Net Web 應用程式建立自訂 API | Microsoft Docs
description: 在 PowerApps 和邏輯流程中建立自訂 API 的 ASP.Net Web 應用程式教學課程
services: ''
suite: powerapps
documentationcenter: ''
author: sunaysv
manager: erikre
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: mandia

---
# 教學課程：為 PowerApps 和邏輯流程建立自訂 AAD 保護的 Web API
本教學課程示範如何建立 ASP.Net Web API、在 Azure Webapps 上裝載、啟用 AAD 驗證 (透過簡單驗證)，然後將 Web API 註冊於 PowerApps 和邏輯流程中。

> [!IMPORTANT]
> 本主題已移至 powerapps.microsoft.com，位於：[教學課程：針對 PowerApps 和流程建立自訂 AAD 保護的 Web API](https://powerapps.microsoft.com/tutorials/customapi-web-api-tutorial/)。請移至 PowerApps 以取得最新版本。此 Azure 連結會被封存。
> 
> 

## 若要開始，您需要：
* Azure 訂用帳戶
* PowerApps 帳戶
* Visual Studio 2013 或更高版本

## 步驟 1：建立 WebAPI 並部署在 Azure 上
1. 開啟 Visual Studio，然後建立新的 C# ASP.NET Web 應用程式：![](./media/powerapps-web-api-tutorial/newwebapp.png "新增 WebApp")
2. 在下一個畫面上，選取 Web API 範本，然後選取 [沒有驗證]：![](./media/powerapps-web-api-tutorial/noauth.png "沒有授權")
   
   > [!IMPORTANT]
   > 請確定您將驗證設為 [沒有驗證]。
   > 
   > 
3. 專案建立之後，您必須為您的資源建置 Web API。本教學課程中，我們不會深入探究建置 Web API 的相關資訊。
4. 接下來，為 Web API 產生 Swagger 檔案。您可以開啟 [套件管理器主控台]，然後安裝 **Swashbuckle** 來輕鬆地進行此動作：![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Swashbuckle 主控台")
5. 一旦安裝並啟用之後，分別瀏覽下列的 Swagger 文件和 UI 端點：**<your-root-url>/swagger/docs/v1**
   
     **<your-root-url>/swagger**
6. 當您滿意您的 Web API 之後，就可發佈至 Azure。若要在 Visual Studio 中發佈，請移至 [建置]，然後選取 [發佈]。
7. 瀏覽至 ***https://\<azure-webapp-url>/swagger/docs/v1***，解壓縮 Swagger 的 json。
   
   > [!IMPORTANT]
   > 具有重複之 operationid 的 Swagger 文件是無效的。如果您使用範例 C# 範本，作業識別碼 "Values\_Get" 會重複兩次。將一個執行個體變更為 "Value\_Get"。
   > 
   > 

您可以在[這裡][6]下載本教學課程所使用的 Swagger 檔案。使用之前，請務必取代/移除註解。從 `//` 開始的註解。

## 步驟 2︰設定 AAD 驗證
本教學課程假設您知道如何在 Azure 中建立 AAD 應用程式。如需如何建立 AAD 應用程式的詳細資訊，請閱讀 [Azure Resource Manager 教學課程](powerapps-azure-resource-manager-tutorial.md)。此教學課程我們需要兩個 AAD 應用程式。

1. 第一個 AAD 應用程式用於保護 Web API。它命名為 **webAPI**。
2. 第二個 AAD 應用程式用於保護自訂 API 註冊，和取得由第一個應用程式保護之 web API 的委派存取權限。這個應用程式命名為 **webAPI-customAPI**。
3. 針對 **webAPI**，請使用下列設定：
   
   1. 登入 URL：***https://login.windows.net***
   2. 應用程式識別碼 Uri：**https://\<your-root-url>** (通常為您部署在 Azure 上的網站 URL)
   3. 回覆 url：**https://\<your-root-url>/.auth/login/aad/callback**
      
      > [!IMPORTANT]
      > 在稍後您需要此應用程式的用戶端識別碼，因此請記下它。
      > 
      > 
4. 針對 **webAPI-customAPI**，請使用下列設定：
   
   1. 登入 URL：**https://login.windows.net**
   2. 應用程式識別碼 Uri：**可以是任何唯一的 URL**
   3. 回覆 URL：***https://msmanaged-na.consent.azure-apim.net/redirect***
   4. 新增權限至具有委派存取權限的 webAPI。
   5. 稍後您也需要此應用程式的用戶端識別碼，因此請記下它。
   6. 產生金鑰並儲存在安全的位置。稍後我們需要此金鑰。

> [!IMPORTANT]
> 這兩個應用程式必須在相同目錄中。
> 
> 

## 步驟 3：在 Web 應用程式上設定簡單驗證
1. 登入 [Azure 入口網站](https://portal.azure.com)，然後移至您在**步驟 1** (本主題中) 部署的 Web 應用程式。
2. 在 [設定] 中，選取 [驗證/授權]。
3. 開啟 [App Service 驗證]，然後選取 [Azure Active Directory]。在下一個刀鋒視窗，選取 [快速]。
4. 按一下 [選取現有的 AD 應用程式]，然後選取您在步驟二中建立的第一個 AAD 應用程式。在此案例中，選取 [webAPI]。

這應該會設定您 Web 應用程式的 AAD 驗證。

## 步驟 4：設定自訂 API
1. 我們需要將我們的 Swagger 檔案做一些修改，以輸入 `securityDefintions` 物件及用於 Web 應用程式的 AAD 驗證。新增下列幾行程式碼：
   
    ```javascript
   "host": "<your-root-url>",
   "schemes": [
    "https"                        //Change scheme to https 
   ],
   "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
   },
    ```
2. 移至 PowerApps [入口網站][1]，然後加入自訂 API。[Use Custom APIs in Logic Flows and PowerApps (在邏輯流程和 PowerApps 中使用自訂 API)](powerapps-register-custom-api.md) 有列出步驟。
3. 一旦您已上傳 Swagger，精靈會自動偵測到您正在針對 webAPI 使用 AAD 驗證。
4. 針對您的自訂 API 設定 AAD 驗證：
   
   1. 用戶端識別碼：**步驟 2** (本主題中) 4.5 的 **webAPI-CustomAPI 用戶端識別碼**
   2. 密碼：**步驟 2** (本主題中) 4.6 的 **webAPI-CustomAPI 金鑰**
   3. 登入 url：***https://login.windows.net***
   4. ResourceUri：**步驟 2** (本主題中) 3.4 的**webAPI 用戶端識別碼**
5. 選取 [建立]，並嘗試在自訂 API 上建立連接。如果所有項目皆已正確設定，您應該能夠登入成功。

如需建立 PowerApps 和邏輯流程的詳細經驗，請參閱下列各項：

* [Connect to Office 365, Twitter, and Microsoft Translator (連線到 Office 365、Twitter 和 Microsoft Translator)][5]
* [Show data from Office 365 (顯示 Office 365 中的資料)][4]
* [Create an app from data (從資料建立 app)][3]
* [Get started with logic flows (開始使用邏輯流程)][2]

如有問題或意見，請傳送電子郵件到[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)。

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0713_2016-->