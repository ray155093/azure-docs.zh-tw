<properties
   pageTitle="Azure Functions 的持續部署 | Microsoft Azure"
   description="使用 Azure App Service 的持續部署工具來發佈 Azure Functions。"
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="glenga"/>

# Azure Functions 的持續部署 

Azure Functions 可讓您輕鬆地為函式應用程式設定持續部署。Functions 利用 Azure App Service 與 BitBucket、Dropbox、GitHub 及 Visual Studio Team Services (VSTS) 的整合來實現持續部署工作流程，在此工作流程中，Azure 會在函式程式碼發佈到上述其中一個服務時提取函式程式碼的更新。如果您不熟悉 Azure Functions，請從 [Azure Functions 概觀](functions-overview.md)來開始。

持續部署對於整合了多個經常參與的專案而言是一個絕佳選項。它也可讓您維護函式程式碼的原始檔控制。目前支援的部署來源如下：

+ [Bitbucket](https://bitbucket.org/)
+ [Dropbox](https://bitbucket.org/)
+ [Git 本機儲存機制](../app-service-web/app-service-deploy-local-git.md)
+ Git 外部儲存機制
+ [GitHub]
+ Mercurial 外部儲存機制
+ [OneDrive](https://onedrive.live.com/)
+ Visual Studio Team Services

在設定部署時是以每個函式應用程式為基礎來進行。當持續部署啟用後，在入口網站中對函式程式碼的存取權會設定為「唯讀」。

## 持續部署需求

您必須先設定部署來源，並將函式程式碼放入部署來源中，才能設定持續部署。在指定的函式應用程式部署中，每個函式會存在於具名子目錄中，而其目錄名稱則為函式的名稱。此資料夾結構基本上就是您的網站程式碼。

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## 設定持續部署

您可以使用下列程序來為現有函式應用程式設定持續部署︰

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)的函式應用程式中，按一下 [函式應用程式設定] > [設定持續整合] > [設定]。

	![設定持續部署](./media/functions-continuous-deployment/setup-deployment.png)

	您也可以按一下 [從原始檔控制開始] 以從 Functions 快速入門進入 [部署] 刀鋒視窗。

2. 在 [部署] 刀鋒視窗中按一下 [選擇來源]、填入所選部署來源的資訊，然後按一下 [確定]。

	![選擇部署來源](./media/functions-continuous-deployment/choose-deployment-source.png)

當持續部署設定好之後，部署來源中的所有變更檔案都會複製到函式應用程式，並觸發完整的網站部署。當來源中的檔案更新時，便會重新部署網站。


##部署選項

以下是一些典型的部署案例︰

###建立預備部署

函式應用程式尚未支援部署位置。不過，您仍可使用持續整合來管理個別的預備部署和生產部署。

設定及使用預備部署的程序通常應該會像下面這樣︰

1. 在訂用帳戶中建立兩個函式應用程式，一個用於生產程式碼，一個用於預備。

2. 建立部署來源 (如果還未擁有)。我們將使用 [GitHub]。
 
3. 在生產函式應用程式中，完成上述在**設定持續部署**中所述的步驟，並將部署分支設為 GitHub 儲存機制的主要分支。

	![選擇部署分支](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. 針對預備函式應用程式重複此步驟，但這次請在 GitHub 儲存機制中選擇預備分支。如果部署來源不支援分支功能，請使用不同的資料夾。
 
5. 對預備分支或資料夾中的程式碼進行更新，然後確認預備部署中已反映這些變更。

6. 經過測試之後，將預備分支的變更合併到主要分支。這會觸發部署至生產函式應用程式的作業。如果部署來源不支援分支，請以預備資料夾中的檔案覆寫生產資料夾中的檔案。

###將現有函式移至持續部署

當您擁有在入口網站中建立並維護的現有函式，您必須先使用 FTP 或本機 Git 儲存機制下載現有函式的程式碼檔案，再如上所述設定持續部署。您可以在函式應用程式的 App Service 設定中進行此操作。下載了檔案之後，可以將其上傳至所選的持續部署來源。

>[AZURE.NOTE]在設定持續整合之後，您就再也無法於 Functions 入口網站編輯原始程式檔。

####作法：設定部署認證
在從函式應用程式下載檔案之前，您必須先設定用來存取網站的認證，而從入口網站即可進行此操作。認證是在函式應用程式層級進行設定。

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)的函式應用程式中，按一下 [函式應用程式設定] > [移至 App Service 設定] > [部署認證]。

	![設定本機部署認證](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. 輸入使用者名稱和密碼，然後按一下 [儲存]。您現在可以使用這些認證從 FTP 或內建的 Git 儲存機制存取函式應用程式。

####作法︰使用 FTP 下載檔案

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)的函式應用程式中，按一下 [函式應用程式設定] > [移至 App Service 設定] > [屬性]，然後複製 [FTP/部署使用者]、[FTP 主機名稱] 和 [FTPS 主機名稱] 的值。[FTP/部署使用者] 必須輸入入口網站所顯示的值 (包括應用程式名稱)，以針對 FTP 伺服器提供適當的內容。

	![取得部署資訊](./media/functions-continuous-deployment/get-deployment-credentials.png)
    
2. 從您的 FTP 用戶端，使用您所蒐集的連接資訊連接到您的應用程式，並下載函式的原始程式檔。

####作法︰使用本機 Git 儲存機制下載檔案

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)的函式應用程式中，按一下 [函式應用程式設定] > [設定持續整合] > [設定]。

2. 在 [部署] 刀鋒視窗中，按一下 [選擇來源] > [本機 Git 儲存機制]，然後按一下 [確定]。
 
3. 按一下 [移至 App Service 設定] > [屬性]，並記下 Git URL 的值。
    
	![設定持續部署](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. 使用 Git 感知命令列或您慣用的 Git 工具複製本機電腦上的儲存機制。Git clone 命令看起來如下所示︰

		git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. 將函式應用程式的檔案擷取到本機電腦上的複製，如下列範例所示︰

		git pull origin master

	如有要求，請提供函式應用程式部署的使用者名稱和密碼。


[GitHub]: https://github.com/

<!---HONumber=AcomDC_0824_2016-->