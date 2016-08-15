<properties
	pageTitle="將 Git 構件儲存機制加入實驗室 | Microsoft Azure"
	description="將適用於自訂構件的 GitHub 或 Visual Studio Team Services Git 儲存機制加入至 DevTest Labs"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# 將 Git 構件儲存機制加入實驗室

> [AZURE.NOTE] 檢視本文隨附的影片：[如何在 DevTest Labs 中新增私人構件儲存機制至實驗室](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab)

## 概觀

根據預設，實驗室包含來自官方 Azure DevTest Labs 構件儲存機制的構件。您可以將 Git 構件儲存機制加入您的實驗室，以包含小組建立的構件 。儲存機制可以裝載在 [GitHub](https://github.com) 或 [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上。

- 若要了解如何建立 GitHub 儲存機制，請參閱 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)。
- 若要了解如何使用 Git 儲存機制建立 Team Services 專案，請參閱[連接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下列螢幕擷取畫面顯示包含構件的儲存機制在 GitHub 中的可能外觀範例：![GitHub 構件儲存機制範例](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## 將 GitHub 構件儲存機制加入您的實驗室

若要將 GitHub 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室

### 取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 在包含小組構件的 GitHub 儲存機制首頁上，儲存 **HTTPS 複製 URL** 以供日後使用。

1. 選取右上角的設定檔影像，然後選取 [設定]。

1. 在左側的 [個人設定] 功能表上，選取 [個人存取權杖]。

1. 選取 [產生新的權杖]。

1. 在 [新增個人存取權杖] 頁面上，輸入 [權杖描述]、接受 [選取範圍] 中的預設項目，然後選擇 [產生權杖]。

1. 儲存產生的權杖，因為您稍後需要用到。

1. 您現在可以關閉 GitHub。

###將您的實驗室連接至 GitHub 儲存機制

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [瀏覽]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。

1. 在實驗室的刀鋒視窗上，選取 [設定]。

1. 在實驗室的 [設定] 刀鋒視窗上，選取 [構件儲存機制]。

1. 在 [構件儲存機制] 刀鋒視窗上：

    1. 輸入儲存機制的**名稱**。
    1. 輸入儲存的 **Git 複製 URL**。
    2. 在包含構件的構件儲存機制中輸入**資料夾路徑**。
    3. 在構件儲存機制中輸入儲存的**個人存取權杖**。
    4. 選取 [**儲存**]。

您儲存機制中的構件現在會列在 [新增構件] 刀鋒視窗。

## 將 Visual Studio Git 構件儲存機制加入您的實驗室

若要將 Visual Studio Git 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室。

### 在構件專案的 Visual Studio 網頁上

1. 開啟您的小組集合首頁 (例如 `https://contoso-web-team.visualstudio.com`)，然後選取構件專案。

2. 在專案首頁上，選取 [程式碼]。

1. 若要檢視複製 URL，可在專案 [程式碼] 頁面上，選取 [複製]。

1. 儲存 URL，因為您稍後在本教學課程中將需要此資訊。

1. 若要建立個人存取權杖，可從使用者帳戶下拉式功能表中選取 [我的設定檔]。

1. 在 [設定檔資訊] 頁面上，選取 [安全性]。

1. 在 [安全性] 索引標籤上，選取 [新增]。

1. 在 [建立個人存取權杖] 頁面中：

    1. 輸入權杖的**描述**。
    2. 從 [到期日] 清單中選取 [180 天] 。
    3. 從 [帳戶] 清單中選擇 [所有可存取的帳戶]。
    4. 選擇 [所有範圍] 選項。
    5. 選擇 [建立權杖]。

1. 完成時，新的權杖將會出現在 [個人存取權杖] 清單中。選取 [複製權杖]，然後儲存權杖值，因為短時間內將會用到此資訊。

### 在實驗室

1. 在實驗室的刀鋒視窗上，選取 [設定]。

    ![選擇設定](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. 在 [設定] 刀鋒視窗上，選取 [構件儲存機制]。

1. 在 [構件儲存機制] 刀鋒視窗上

    1. 輸入儲存機制的顯示**名稱**。
    1. 輸入儲存的 **Git 複製 URL**。
    2. 在包含構件的構件儲存機制中輸入**資料夾路徑**。
    3. 在構件儲存機制中輸入儲存的**個人存取權杖**。
    4. 選取 [**儲存**]。

## 相關部落格文章
- [如何在 AzureDevTestLabs 疑難排解失敗的構件](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [在 Azure 研發測試實驗室使用 ARM 範本將 VM 加入至現有 AD 網域](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0803_2016-->