---
title: 如何在邏輯流程和 PowerApps 中使用自訂 API | Microsoft Docs
description: 何謂自訂 API、使用 OAuth 提供者，以及使用 Swagger 在 PowerApps 和邏輯流程中新增自訂 API
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
# 何謂自訂 API
自訂 API 是您可納入並與 PowerApps 和邏輯流程搭配使用的任何 RESTful API。只要詳細記錄的規格符合 [OpenAPI][1] 標準，就可以在任何位置裝載這些 API。

> [!IMPORTANT]
> 本主題已移至 powerapps.microsoft.com，位於：[什麼是自訂 API](https://powerapps.microsoft.com/tutorials/register-custom-api/)。請移至 PowerApps 以取得最新版本。此 Azure 連結會被封存。
> 
> 

## 若要開始，您需要：
* 使用 PowerApps 的任何人都可以使用自訂 API。不需要 PowerApps Enterprise。
* 需要 Swagger 檔案 (.json) 和自訂 API 的圖示。本主題提供數個選項來建立 Swagger 檔案。針對圖示，您可以使用任何您想要的影像。

## 驗證
您可以使用下列其中一種驗證機制︰

* 基本驗證
* OAuth 2.0：下面列出您可以與自訂 API 搭配使用之所有支援的 OAuth 2.0 提供者 (未來會提供更多支援)：
  
  * Azure Active Directory
  * Box
  * Dropbox
  * Facebook
  * Google
  * Instagram
  * OneDrive
  * SalesForce
  * Slack
  * Yammer

> [!NOTE]
> 即將推出 API 金鑰驗證支援。
> 
> 

您可以在 OpenAPI (Swagger) 文件的 [OpenAPI 規格][1]中深入了解如何指定驗證類型。

如果您的 API 端點允許未經驗證的存取，則應該從 OpenAPI (Swagger) 檔案中移除 ```securityDefintions``` 物件。在下列範例中，移除下列所有 ```securityDefintions``` 物件︰

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [!TIP]
> 在 .json 檔案中，您無法加入註解；所有文字都會視為資料的一部分。
> 
> 

### 驗證範例
* 使用 AAD 驗證的 [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md)
* 使用 AAD 驗證的 [Azure WebApp](powerapps-web-api-tutorial.md)

## 註冊自訂 API
### 步驟 1：建立 Swagger 檔案
您可以從**任何** API 端點建立 Swagger 檔案，包括：

* 任何已發佈且公開可用的 API。部分範例包括 [Spotify][2]、[Uber][3]、[Slack][4]、[Rackspace][5] 等等。
* 建立和部署到任何可部署 Web 應用程式之雲端服務的 API，包括 Amazon Web Services (AWS)、Heroku、Azure Web 應用程式、Google Cloud 等等。
* 部署在網路中或電腦上的 API；只要 API 在網際網路上公開可用。

建立 Swagger 檔案時，會建立 .json 檔案。將此 .json 檔案保存在隨手可及的位置。

#### 取得建立 Swagger 檔案的說明
* 如果您是第一次建立 Swagger 檔案或從未建立過 Swagger 檔案，則[開始使用 Swagger][6] 是不錯的資源。
* 若要建立您自己的 API，請將它部署至 Azure，並根據這個新 API 來建立 Swagger 檔案，然後考慮使用 [Web API 教學課程](powerapps-web-api-tutorial.md)。本教學課程提供可運作的 Swagger 檔案。GitHub 上也提供 [Hello World 範例][7]。
* 若要驗證您的 Swagger 檔案，請使用 [Swagger 編輯器][8]。您可以貼上您的 .json 資料，而驗證會自動進行。
* 若要自訂 Swagger 文件，以使用 PowerApps 和邏輯流程，請參閱[自訂 Swagger 定義](powerapps-how-to-swagger.md)。

### 步驟 2︰新增自訂 API 連線
現在，已產生自訂 API 的 Swagger 檔案 (.json 檔案)，請新增 PowerApps 連線。您也需要您自訂 API 的圖示。

1. 移至 PowerApps [Web 入口網站][9]中，並使用您的工作帳戶登入。
   
   > [!NOTE]
   > 目前，自訂 API 只能在 PowerApps Web 入口網站中使用。它們不能在 PowerApps 用戶端中使用。
   > 
   > 
2. 選取 [連線]，然後選取 [加入連線]：  
   ![](./media/powerapps-register-custom-api/createnewconnection.png "建立自訂 API")  
3. 選取 [加入自訂 API]：  
    ![](./media/powerapps-register-custom-api/connecttocustomapi.png "建立自訂 API")  
    加入 API 的內容 (包括 .json 和圖示檔案)。然後，選取 [下一步]：
   
   | 屬性 | 說明 |
   | --- | --- |
   | 名稱 |輸入您自訂 API 的名稱。如果這是範例 Web API，您可以將它命名為 **MySampleWebAPI**。 |
   | Swagger API 定義 |瀏覽至從 Swagger 建立的 .json 檔案。 |
   | 上傳 API 圖示 |瀏覽至圖示檔案。 |
   | 說明 |輸入您自訂 API 的描述。如果這是範例 Web API，您可以輸入 **Sample Web API Tutorial**。 |
4. 輸入任何驗證屬性。如果 .json 檔案在 ```securityDefintions``` 物件中使用 OAuth2 驗證，則系統會提示您輸入下列值︰
   
   | 屬性 | 說明 |
   | --- | --- |
   | 用戶端識別碼 |使用其中一個支援的 OAuth 識別提供者 (列於本主題中)，提供用戶端識別碼。輸入此用戶端識別碼。 |
   | 用戶端密碼 |輸入您選擇之識別提供者的用戶端密碼。 |
   
    本主題中的**驗證範例**提供這些 OAuth 屬性的範例。
   
    如果 .json 檔案未使用 ```securityDefintions``` 物件，則可能不需要任何其他值。
5. 選取 [**建立**]。您的自訂 API 現在會顯示在 [可用連線] 中：
   
    ![](./media/powerapps-register-custom-api/mycustomapi.png "可用連線")

> [!TIP]
> 如果無法驗證 Swagger 檔案，則可能有額外字元。例如，幾乎所有資料都應該用引號括住 (包括網站)。因此，如果您的 `https://mywebapi.mywebsite.com` 在引號外面，則無法驗證檔案。
> 
> 

### 步驟 3︰將自訂 API 加入邏輯流程和 PowerApp
現在，您已準備好搭配使用自訂 API 與 PowerApp 或邏輯流程。在本節中，我們使用自訂天氣 API。

#### 將自訂 API 加入邏輯流程中
在此步驟中，我們建立非常簡單的邏輯流程，示範如何加入您的自訂 API。如需更深入的體驗，請參閱[開始建立邏輯流程][10]。

1. 在 PowerApps [Web 入口網站][9]中，選取 [首頁] 索引標籤。
2. 在 [建立邏輯流程] 下，選取 [開始使用]。
3. 在此視窗中，已建立數個邏輯流程範本來使用一些常見案例。您可以使用所有這些項目，並在其中加入您的自訂 API。或者，您可以選擇 [從空白建立] 從頭開始建立邏輯流程。
   
    加入您自訂 API 的最快速方式是選取 [從空白建立]。這樣會開啟下列邏輯流程︰  
    ![](./media/powerapps-register-custom-api/createfromblank.png "開始邏輯流程")   
4. 選取 [週期]，並設為 1 分鐘的頻率︰  
    ![](./media/powerapps-register-custom-api/logicrecurrence.png "選取 \[週期\]")      
5. 選取加號 (![](./media/powerapps-register-custom-api/flowplussign.png))，然後選取 [加入動作]。在清單中，會列出您的自訂 API：  
   ![](./media/powerapps-register-custom-api/logicflow.png "自訂 API")

後續步驟取決於您的 API 可執行的作業。在天氣範例中，您的 API 可能會取得目前的溫度，然後使用 Office 365 傳送電子郵件：

![](./media/powerapps-register-custom-api/logicflowexample.png "天氣範例")

#### 將自訂 API 加入 PowerApp 中
在此步驟中，我們建立非常簡單的 PowerApp，示範如何加入您的自訂 API。如需更深入的體驗，請參閱[從資料建立應用程式][11]。

> [!NOTE]
> 目前，自訂 API 只能在 PowerApps Web 入口網站中使用。它們不能在 PowerApps 用戶端中使用。
> 
> 

1. 在 PowerApps [Web 入口網站][9]中，選取 [新增 PowerApp]：  
    ![](./media/powerapps-register-custom-api/newpowerapp.png "選取 \[新增 PowerApp\]")  
2. 即會在瀏覽器中開啟新的索引標籤。在這個新的索引標籤中，會自動建立空白 PowerApp。選取 [連線到資料]：  
   ![](./media/powerapps-register-custom-api/blankpowerapp.png "選取 \[連線到資料\]")  
3. 在 [內容] 索引標籤中，選取 [資料來源]：  
   ![](./media/powerapps-register-custom-api/datasources.png "選取 \[連線到資料\]")  
4. 在新的畫面中，於 [我的連線] 下選取您的自訂 API：  
   ![](./media/powerapps-register-custom-api/screencustomapi.png "選取您的自訂 API")  
5. 選取 [新增資料來源]。

新增之後，您可以在函式列、文字方塊等項目內使用您的自訂 API。例如，在函式列中，您可以開始輸入 **MySampleWebAPI** 以查看可用的函式。[顯示 Office 365 中的資料][12]是使用 Office 365 API 的範例。

## 共用自訂 API
使用者也可以彼此共用自訂 API。新增自訂 API 之後，請依序選取 [連線] 索引標籤、[自訂 API] 和共用圖示︰

![](./media/powerapps-register-custom-api/sharecustomapi.png "共用自訂 API")

> [!NOTE]
> 您只能與組織中的其他使用者共用自訂 API。
> 
> 

## 配額和節流
* 您最多可以在 PowerApps 帳戶中建立五個自訂 API。與您共用的自訂 API 不會計入此配額。
* 針對在自訂 API 上建立的每個連線，使用者每分鐘最多可以提出 500 個要求。
* 請記住，刪除自訂 API 會刪除針對 API 所建立的所有連線。

如有自訂 API 的問題或意見，請傳送電子郵件到[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)。

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!----HONumber=AcomDC_0713_2016-->