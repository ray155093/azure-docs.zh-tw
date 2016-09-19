<properties
	pageTitle="將 Git 構件儲存機制加入 Azure DevTest Labs 中的實驗室 | Microsoft Azure"
	description="將適用於自訂構件來源的 GitHub 或 Visual Studio Team Services Git 儲存機制加入 Azure DevTest Labs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# 將 Git 構件儲存機制加入 Azure DevTest Labs 中的實驗室

> [AZURE.VIDEO how-to-add-your-private-artifacts-repository-in-a-devtest-lab]

在 Azure DevTest Labs 中，構件是「動作」 - 例如安裝軟體或執行指令碼和命令 (VM 建立時)。根據預設，實驗室包含來自官方 Azure DevTest Labs 構件儲存機制的構件。您可以將 Git 構件儲存機制加入您的實驗室，以包含小組建立的構件 。儲存機制可以裝載在 [GitHub](https://github.com) 或 [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上。

- 若要了解如何建立 GitHub 儲存機制，請參閱 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)。
- 若要了解如何使用 Git 儲存機制建立 Team Services 專案，請參閱[連接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下列螢幕擷取畫面顯示包含構件的儲存機制在 GitHub 中的可能外觀範例：![GitHub 構件儲存機制範例](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)


## 取得儲存機制資訊和認證

若要將構件儲存機制加入您的實驗室，您必須先從儲存機制取得特定資訊。下列各節會引導您取得 GitHub 與 Visual Studio Team Services 上裝載的構件儲存機制的這項資訊。

### 取得 GitHub 儲存機制複製 URL 和個人存取權杖

若要取得 GitHub 儲存機制複製 URL 和個人存取權杖，請遵循下列步驟：

1. 瀏覽至包含構件定義的 GitHub 儲存機制首頁。

1. 選取 [複製或下載]。

1. 選取將 **HTTPS 複製 URL** 複製到剪貼簿的按鈕，並儲存該 URL 供稍後使用。

1. 選取 GitHub 右上角的設定檔影像，然後選取 [設定]。

1. 在左側的 [個人設定] 功能表上，選取 [個人存取權杖]。

1. 選取 [產生新的權杖]。

1. 在 [新增個人存取權杖] 頁面上，輸入 [權杖描述]、接受 [選取範圍] 中的預設項目，然後選擇 [產生權杖]。

1. 儲存產生的權杖，因為您稍後需要用到。

1. 您現在可以關閉 GitHub。

1. 繼續[將您的實驗室連接至構件儲存機制](#connect-your-lab-to-the-artifact-repository)一節。

### 取得 Visual Studio Team Services 儲存機制複製 URL 和個人存取權杖

若要取得 Visual Studio Team Services 儲存機制複製 URL 和個人存取權杖，請遵循下列步驟：

1. 開啟您的小組集合首頁 (例如 `https://contoso-web-team.visualstudio.com`)，然後選取構件專案。

1. 在專案首頁上，選取 [程式碼]。

1. 若要檢視複製 URL，可在專案 [程式碼] 頁面上，選取 [複製]。

1. 儲存 URL，因為在本教學課程稍後將需要此資訊。

1. 若要建立個人存取權杖，可從使用者帳戶下拉式功能表中選取 [我的設定檔]。

1. 在 [設定檔資訊] 頁面上，選取 [安全性]。

1. 在 [安全性] 索引標籤上，選取 [新增]。

1. 在 [建立個人存取權杖] 頁面中：

    - 輸入權杖的**描述**。
    - 從 [到期日] 清單中選取 [180 天] 。
    - 從 [帳戶] 清單中選擇 [所有可存取的帳戶]。
    - 選擇 [所有範圍] 選項。
    - 選擇 [建立權杖]。

1. 完成時，新的權杖會出現在 [個人存取權杖] 清單中。選取 [複製權杖]，然後儲存權杖值供稍後使用。

1. 繼續[將您的實驗室連接至構件儲存機制](#connect-your-lab-to-the-artifact-repository)一節。

##將您的實驗室連接至構件儲存機制

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 在實驗室的刀鋒視窗上，選取 [組態]。

1. 在實驗室的 [組態] 刀鋒視窗上，選取 [構件儲存機制]。

1. 在 [構件儲存機制] 刀鋒視窗上，選取 [+ 加入]。

	![加入構件儲存機制按鈕](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
 
1. 在第二個 [構件儲存機制] 刀鋒視窗上，指定下列各項︰

    - **名稱** - 輸入儲存機制的名稱。
    - **Git 複製 URL** - 輸入您先前從 GitHub 或 Visual Studio Team Services 複製的 Git HTTPS 複製 URL。
    - **資料夾路徑** - 輸入相對於包含構件定義的複製 URL 的資料夾路徑。
    - **分支** - 輸入取得構件定義的分支。
    - **個人存取權杖** - 輸入您先前從 GitHub 或 Visual Studio Team Services 取得的個人存取權杖。
     
	![構件儲存機制刀鋒視窗](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)

1. 選取 [**儲存**]。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 相關部落格文章
- [如何在 AzureDevTestLabs 疑難排解失敗的構件](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [在 Azure 研發測試實驗室使用 ARM 範本將 VM 加入至現有 AD 網域](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0907_2016-->