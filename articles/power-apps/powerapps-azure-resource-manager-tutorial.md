<properties
	pageTitle="教學課程：在 PowerApps 和邏輯流程中使用 Azure Resource Manager 建立自訂 API | Microsoft Azure"
	description="在 PowerApps 和邏輯流程中建立自訂 API 的 Azure Resource Manager 教學課程"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"s
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# 教學課程：針對 PowerApps 和邏輯流程建立自訂 AAD 保護的 ARM API 

本教學課程將逐步引導您完成必要的步驟來註冊描述 [ARM API][6] 的 Swagger 檔案，然後連接到 PowerApps 中的自訂 API。

## 若要開始，您需要：

- Azure 訂用帳戶
- PowerApps 帳戶

## 啟用 Azure Active Directory 中的驗證

首先，我們需要建立 Azure Active Directory (AAD) 應用程式，該應用程式在呼叫 ARM API 端點時會執行驗證。

1. 若要建立 AAD 應用程式，請登入您的 [Azure 訂用帳戶][7]，然後移至 [Active Directory]：![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")  

2. 在此頁面上，選擇您要在其中建立 AAD 應用程式的目錄。選取該目錄，移至 [應用程式] 索引標籤，並選取 [新增]：![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Azure 應用程式")

3. 輸入應用程式名稱，選取 [Web 應用程式和/或 Web API]，然後選取 [下一步]：![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "新增應用程式")

4. 在 [登入 URL] 中，輸入：**http://login.windows.net*。在 [應用程式識別碼 URI] 中，輸入任何唯一的 URI。接著，選取 [完成]：![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "新增第二個應用程式")

5. 建立 AAD 應用程式之後，移至 [設定] 索引標籤。在此索引標籤中，我們設定應用程式的權限。

6. 在 [其他應用程式的權限] 底下，選取 [新增應用程式]，然後輸入下列權限，如下所示：![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "權限")

	請確定您將必要的權限委派給應用程式：![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "委派權限")

7. 在 [金鑰] 底下，選取任一持續時間。**請將金鑰複製並儲存到安全的位置**；我們稍後需要此資訊。也請記下 [用戶端識別碼]：![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "設定金鑰")

8. 在 [單一登入] 底下，將下列 URL 輸入 [回覆 URL]：https://msmanaged-na.consent.azure-apim.net/redirect：![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "重新導向 URL")

9. **儲存**您的變更。**請將金鑰複製並儲存到安全的位置**。

## 在 PowerApps 或邏輯流程中加入連接

既然已經設定好 AAD 應用程式，讓我們來加入自訂 API。

1. 開啟 [PowerApps 入口網站][1]，移至 [連線] 索引標籤，然後選取右上角的 [新增連線]：![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "建立自訂 API")  

2. 選取 [新增自訂 API]：![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "建立自訂 API")

3. 上傳 ARM Swagger 檔案，可透過[下載][8]取得：![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "建立自訂 API")

4. 在下一個畫面中，由於偵測到我們的 Swagger 檔案使用 AAD 驗證，我們必須輸入 AAD 用戶端識別碼、用戶端密碼 (您儲存於安全位置的**金鑰**)，以及其他設定：![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "OAuth 設定")

5. 如果所有項目皆已正確設定，您可以在建立 PowerApp 或邏輯流程時，建立連接然後參照 ARM 自訂 API 來使用它：![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "已建立 CustomAPI")

同樣地，您也可以存取任何使用 RESTful API 和使用 AAD OAuth2 驗證公開的資料。

如需建立 PowerApps 和邏輯流程的詳細經驗，請參閱下列各項：

- [Connect to Office 365, Twitter, and Microsoft Translator (連線到 Office 365、Twitter 和 Microsoft Translator)][5]
- [Show data from Office 365 (顯示 Office 365 中的資料)][4]
- [Create an app from data (從資料建立 app)][3]
- [Get started with logic flows (開始使用邏輯流程)][2]

如有自訂 API 的問題或意見，請傳送電子郵件到[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)。


<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0413_2016-->