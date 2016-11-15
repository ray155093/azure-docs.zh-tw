---
title: "開始使用儲存體 Explorer (預覽) | Microsoft Docs"
description: "使用儲存體總管管理 Azure 儲存體資源 (預覽)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5c003c9240d8625d2921666e1361ce0512138bb


---
# <a name="getting-started-with-storage-explorer-preview"></a>開始使用儲存體 Explorer (預覽)
## <a name="overview"></a>Overview
Microsoft Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、OS X 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將學習連接及管理 Azure 儲存體帳戶的各種方式。

![Microsoft Azure 儲存體 Explorer (預覽)][15]

## <a name="prerequisites"></a>必要條件
* [下載並安裝儲存體 Explorer (預覽)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務
儲存體 Explorer (預覽) 提供無數種方式來連接至儲存體帳戶。 其中包括連接到與您的 Azure 訂用帳戶相關聯的儲存體帳戶、連接到從其他 Azure 訂用帳戶共用的儲存體帳戶和服務，甚至是使用 Azure 儲存體模擬器連接及管理本機儲存體：

* [連接到 Azure 訂用帳戶](#connect-to-an-azure-subscription) - 管理屬於您的 Azure 訂用帳戶的儲存體資源。
* [使用本機開發儲存體](#work-with-local-development-storage) - 使用 Azure 儲存體模擬器管理本機儲存體。 
* [附加到外部儲存體](#attach-or-detach-an-external-storage-account) - 使用儲存體帳戶的帳戶名稱與金鑰，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
* [使用 SAS 附加儲存體帳戶](#attach-storage-account-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
* [使用 SAS 附加服務](#attach-service-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的特定儲存體服務 (Blob 容器、佇列或資料表)。

## <a name="connect-to-an-azure-subscription"></a>連接到 Azure 訂用帳戶
> [!NOTE]
> 如果您沒有 Azure 帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
> 
> 

1. 在儲存體 Explorer (預覽) 中，選取 [Azure 帳戶設定] 。 
   
    ![Azure 帳戶設定][0]
2. 左窗格現在會顯示您已登入的所有 Microsoft 帳戶。 若要連接到其他帳戶，請選取 [新增帳戶] ，並依照對話方塊使用與至少一個作用中 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。
   
    ![新增帳戶][1]
3. 成功使用 Microsoft 帳戶登入後，左窗格會填入與該帳戶相關聯的 Azure 訂用帳戶。 選取您想要使用的訂用帳戶，然後選取 [套用]。 (選取 [所有訂用帳戶] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶。)
   
    ![選取 Azure 訂用帳戶][3]
4. 左窗格現在會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。
   
    ![已選取的 Azure 訂用帳戶][4]

## <a name="work-with-local-development-storage"></a>使用本機開發儲存體
儲存體 Explorer (預覽) 可讓您使用 Azure 儲存體模擬器處理本機儲存體。 這可讓您撰寫程式碼並測試儲存體，而不需在 Azure 上部署儲存體帳戶 (因為 Azure 儲存體模擬器正在模擬儲存體帳戶)。

> [!NOTE]
> Azure 儲存體模擬器目前只適用於 Windows。 
> 
> 

1. 在儲存體 Explorer (預覽) 的左窗格中，依序展開 [(本機連接)] > [儲存體帳戶] > [(開發)] 節點。
   
    ![本機開發節點][21]
2. 如果您尚未安裝 Azure 儲存體模擬器，系統將透過資訊列提示您這麼做。 若已顯示資訊列，請選取 [下載最新版本] ，並安裝模擬器。 
   
    ![下載 Azure 儲存體模擬器提示字元][22]
3. 安裝模擬器後，您就能夠建立並使用本機 Blob、佇列和資料表。 若要了解如何使用每個儲存體帳戶類型，請選取以下適當的連結：
   
   * [管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
   * 管理 Azure 檔案共用儲存體資源 - 敬請期待 
   * 管理 Azure 佇列儲存體資源 - 敬請期待 
   * 管理 Azure 表格儲存體資源 - 敬請期待 

## <a name="attach-or-detach-an-external-storage-account"></a>附加或卸離外部儲存體帳戶
儲存體 Explorer (預覽) 提供附加到外部儲存體帳戶的能力，因此可以輕鬆地共用儲存體帳戶。 本節說明如何附加至 (及卸離) 外部儲存體帳戶。

### <a name="get-the-storage-account-credentials"></a>取得儲存體帳戶認證
若要共用外部儲存體帳戶，該帳戶的擁有者必須先取得帳戶的認證 (帳戶名稱和金鑰)，然後與想要附加至該 (外部) 帳戶的人員分享該資訊。 遵循下列步驟，即可透過 Azure 入口網站取得儲存體帳戶認證： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [瀏覽] 。
3. 選取 [儲存體帳戶] 。
4. 在 [儲存體帳戶]  刀鋒視窗中，選取所需的儲存體帳戶。
5. 在所選儲存體帳戶的 [設定] 刀鋒視窗中，選取 [存取金鑰]。
   
   ![存取金鑰選項][5]
6. 在 [存取金鑰] 刀鋒視窗中，複製 [儲存體帳戶名稱] 和 [金鑰 1] 值，以便在附加至儲存體帳戶時使用。 
   
   ![存取金鑰][6]

### <a name="attach-to-an-external-storage-account"></a>附加至外部儲存體帳戶
若要附加至外部儲存體帳戶，您會需要帳戶的名稱和金鑰。 「取得儲存體帳戶認證」  一節說明如何從 Azure 入口網站取得這些值。 不過，請注意在入口網站中，帳戶金鑰稱為「金鑰 1」，因此儲存體總管 (預覽) 會要求帳戶金鑰，您將會輸入 (或貼上)「金鑰 1」的值。 

1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。
   
   ![連接至 Azure 儲存體選項][23]
2. 在 [連接到 Azure 儲存體] 對話方塊中，指定帳戶金鑰 (Azure 入口網站中的「金鑰 1」值)，然後選取 [下一步]。
   
   ![連接至 Azure 儲存體對話方塊][24] 
3. 在 [附加外部儲存體] 對話方塊中，請在 [帳戶名稱] 方塊中輸入儲存體帳戶名稱，指定任何其他所需的設定，然後在完成時選取 [下一步]。 
   
   ![附加外部儲存體對話方塊][8]
4. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁]  並重新輸入所需的設定。 完成後，請選取 [連接] 。
5. 連接後，外部儲存體帳戶將會與附加至儲存體帳戶名稱的文字 **(外部)** 一起顯示。 
   
   ![連接至外部儲存體帳戶的結果][9]

### <a name="detach-from-an-external-storage-account"></a>從外部儲存體帳戶卸離
1. 以滑鼠右鍵按一下您要卸離的外部儲存體帳戶，並從內容功能表中選取 [卸離] 。
   
   ![中斷與儲存體選項的連結][10]
2. 當確認訊息方塊出現時，請選取 [是]  已確認從外部儲存體帳戶卸離。

## <a name="attach-storage-account-using-sas"></a>使用 SAS 附加儲存體帳戶
[SAS (共用存取簽章)](storage/storage-dotnet-shared-access-signature-part-1.md) 讓 Azure 訂用帳戶的系統管理員能夠暫時授與儲存體帳戶的存取權，而不需提供其 Azure 訂用帳戶認證。 

為了說明這點，我們假設 UserA 是 Azure 訂用帳戶的系統管理員，而且 UserA 想要允許 UserB 在有限的時間內使用特定權限存取儲存體帳戶：

1. UserA 可在特定期間內使用所需的權限產生 SAS (由儲存體帳戶的連接字串所組成)。
2. UserA 可與想要存取儲存體帳戶的人員共用 SAS - 在本例中為 UserB。  
3. UserB 可透過儲存體 Explorer (預覽)，使用所提供的 SAS 來附加至屬於 UserA 的帳戶。 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>針對您要共用的帳戶取得 SAS
1. 在儲存體 Explorer (預覽) 中，以滑鼠右鍵按一下您要共用的儲存體帳戶，並從內容功能表中選取 [取得共用存取簽章] 。
   
   ![取得 SAS 內容功能表選項][13]
2. 在 [共用存取簽章] 對話方塊中，指定您要用於此帳戶的時間範圍和權限，再選取 [建立]。
   
    ![取得 SAS 對話方塊][14]
3. 第二個 [共用存取簽章]  對話方塊將會顯示，其中顯示 SAS。 選取 [連接字串] 旁的 [複製]，將它複製到剪貼簿。 選取 [關閉]  以關閉對話方塊。

### <a name="attach-to-the-shared-account-using-the-sas"></a>使用 SAS 附加至共用帳戶
1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。
   
   ![連接至 Azure 儲存體選項][23]
2. 在 [連接到 Azure 儲存體] 對話方塊中，指定連接字串，然後選取 [下一步]。
   
   ![連接至 Azure 儲存體對話方塊][24] 
3. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁]  並重新輸入所需的設定。 完成後，請選取 [連接] 。
4. 附加後，儲存體帳戶將會與附加至您提供之帳戶名稱的文字 (SAS) 一起顯示。
   
   ![使用 SAS 附加至帳戶的結果][17]

## <a name="attach-service-using-sas"></a>使用 SAS 附加服務
[使用 SAS 附加儲存體帳戶](#attach-storage-account-using-sas) 一節說明 Azure 訂用帳戶系統管理員如何藉由產生 (及共用) 儲存體帳戶的 SAS 來授與儲存體帳戶的暫存存取權。 同樣地，可以針對儲存體帳戶內的特定服務 (Blob 容器、佇列或資料表) 產生 SAS。  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>針對您要共用的服務產生 SAS
在此情況下，服務可以是 Blob 容器、佇列或資料表。 下列各節說明如何針對所列的服務產生 SAS：

* [取得 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 取得檔案共用的 SAS - 敬請期待 
* 取得佇列的 SAS - 敬請期待 
* 取得資料表的 SAS - 敬請期待 

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>使用 SAS 附加至共用帳戶服務
1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。
   
   ![連接至 Azure 儲存體選項][23]
2. 在 [連接到 Azure 儲存體] 對話方塊中，指定 SAS URI，然後選取 [下一步]。
   
   ![連接至 Azure 儲存體對話方塊][24] 
3. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁]  並重新輸入所需的設定。 完成後，請選取 [連接] 。
4. 附加後，新附加的服務會顯示在 [(服務 SAS)]  節點之下。
   
   ![使用 SAS 附加至共用服務的結果][20]

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶
如果您有一長串的儲存體帳戶，使用左窗格頂端的 [搜尋] 方塊，可讓您快速尋找特定的儲存體帳戶。 

當您在搜尋方塊中輸入資料時，左窗格只會顯示與您當時輸入的搜尋值相符的儲存體帳戶。 以下螢幕擷取畫面說明當我搜尋到儲存體帳戶名稱包含 "tarcher" 文字的所有儲存體帳戶時的範例。

![儲存體帳戶搜尋][11]

若要清除搜尋，請在搜尋方塊中選取 **x** 按鈕。

## <a name="next-steps"></a>後續步驟
* [使用儲存體 Explorer 來管理 Azure Blob 儲存體資源 (預覽)](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png



<!--HONumber=Nov16_HO2-->


