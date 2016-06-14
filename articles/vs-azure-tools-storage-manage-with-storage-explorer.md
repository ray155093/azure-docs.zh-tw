<properties
	pageTitle="開始使用儲存體 Explorer (預覽) | Microsoft Azure"
	description="使用儲存體總管管理 Azure 儲存體資源 (預覽)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# 開始使用儲存體 Explorer (預覽)

## 概觀 

Microsoft Azure 儲存體 Explorer (預覽) 是一個獨立應用程式，可讓您在 Windows、OSX 和 Linux 上輕鬆使用 Azure 儲存體資料。在本文中，您將學習連接及管理 Azure 儲存體帳戶的各種方式。

## 必要條件

- [下載並安裝儲存體 Explorer (預覽)](http://go.microsoft.com/fwlink/?LinkId=708343)

## 連接到儲存體帳戶或服務

儲存體 Explorer (預覽) 提供無數種方式來連接至儲存體帳戶。其中包括連接到與您的 Azure 訂用帳戶相關聯的儲存體帳戶、連接到從其他 Azure 訂用帳戶共用的儲存體帳戶和服務，甚至是使用 Azure 儲存體模擬器連接及管理本機儲存體：

- [連接到 Azure 訂用帳戶](#connect-to-an-azure-subscription) - 管理屬於您的 Azure 訂用帳戶的儲存體資源。
- [連接到本機儲存體](#connect-to-local-storage) - 使用 Azure 儲存體模擬器管理本機儲存體。 
- [附加到外部儲存體](#attach-or-detach-an-external-storage-account) - 使用儲存體帳戶的帳戶名稱與金鑰，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
- [使用 SAS 附加帳戶](#attach-account-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
- [使用 SAS 附加服務](#attach-service-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的特定儲存體服務 (Blob 容器、佇列或資料表)。

## 連線到 Azure 訂用帳戶

> [AZURE.NOTE] 如果您沒有 Azure 帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

1. 啟動儲存體 Explorer (預覽)。 

1. 如果您第一次執行儲存體 Explorer (預覽)，或如果您已執行儲存體 Explorer (預覽)，但尚未連接到 Azure 帳戶，您會看到可讓您連接到 Azure 帳戶的資訊列。

	![][0]
	
1. 選取 [連接到 Microsoft Azure]，並依照對話方塊使用與至少一個作用中 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。

成功使用 Microsoft 帳戶登入後，儲存體 Explorer (預覽) 的左窗格會填入與 Microsoft 帳戶相關聯之所有 Azure 訂用帳戶的所有相關聯儲存體帳戶。

### 篩選 Azure 訂用帳戶

儲存體 Explorer (預覽) 可讓您篩選哪一個與已登入 Microsoft 帳戶相關聯的 Azure 訂用帳戶將使其儲存體帳戶列在左窗格中。

1. 選取 [設定] (齒輪) 圖示。

	![][1]

1. 	在左窗格頂端，您會看到一個下拉式清單，其中包含所有您已登入的 Microsoft 帳戶的。

	![][3]
	 
1.	選取下拉式清單旁邊的向下箭號，以查看所有已登入的 Microsoft 帳戶，以及用於新增 (登入) 其他 Microsoft 帳戶的連結。

	![][4]

1.	從下拉式清單中選取想要的 Microsoft 帳戶。

1. 	左窗格會顯示與所選 Microsoft 帳戶相關聯的所有 Azure 訂用帳戶。每個 Azure 訂用帳戶左邊的核取方塊可讓您指定是否要儲存體 Explorer (預覽) 列出與該 Azure 訂用帳戶相關聯的所有儲存體帳戶。核取/取消核取 [所有訂用帳戶] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶。

	![][2]

1.	當您選好您要管理的 Azure 訂用帳戶，，請選取 [套用]。左窗格會更新以列出目前 Microsoft 帳戶的每個已選取 Azure 訂用帳戶的所有儲存體帳戶。

### 新增其他 Microsoft 帳戶

下列步驟會引導您連接到其他 Microsoft 帳戶，以檢視每個帳戶的 Azure 訂用帳戶和儲存體帳戶。

1.	選取 [設定] (齒輪) 圖示。

	![][1]

1. 	在左窗格頂端，您會看到一個下拉式清單，其中包含目前連接的所有 Microsoft 帳戶。

	![][3]
	 
1.	選取下拉式清單旁邊的向下箭號，以查看所有已登入的 Microsoft 帳戶，以及用於新增 (登入) 其他 Microsoft 帳戶的連結。

	![][4]

1.	選取 [新增帳戶]，並依照對話方塊來登入與至少一個作用中 Azure 訂用帳戶相關聯的帳戶。

1.	選取您想要瀏覽之 Azure 訂用帳戶的核取方塊。

	![][2]

1.	選取 [套用]。

### 在 Microsoft 帳戶之間切換

雖然您可以連接到多個 Microsoft 帳戶，左窗格只會顯示與單一 (目前) Microsoft 帳戶的訂用帳戶相關聯的儲存體帳戶。如果您連接到多個 Microsoft 帳戶，您可以執行下列步驟以在帳戶之間切換：

1.	選取 [設定] (齒輪) 圖示。

	![][1]

1. 	在左窗格頂端，您會看到一個下拉式清單，其中包含目前連接的所有 Microsoft 帳戶。

	![][3]
	 
1.	選取下拉式清單旁邊的向下箭號，以查看所有已登入的 Microsoft 帳戶，以及用於新增 (登入) 其他 Microsoft 帳戶的連結。

	![][4]

1.	選取想要的 Microsoft 帳戶。

1.	選取您想要瀏覽之 Azure 訂用帳戶的核取方塊。

	![][2]

1.	選取 [套用]。
  
## 連接到本機儲存體

儲存體 Explorer (預覽) 可讓您使用 Azure 儲存體模擬器處理本機儲存體。這可讓您撰寫程式碼並測試儲存體，而不需在 Azure 上部署儲存體帳戶 (因為 Azure 儲存體模擬器正在模擬儲存體帳戶)。

>[AZURE.NOTE] Azure 儲存體模擬器目前只適用於 Windows。

1. 啟動儲存體 Explorer (預覽)。 

1. 在左側窗格中展開 [(開發)] 節點。

	![][21]

1. 如果您尚未安裝 Azure 儲存體模擬器，系統將透過資訊列提示您這麼做。若已顯示資訊列，請選取 [下載最新版本]，並安裝模擬器。

	![][22]

1. 安裝模擬器後，您就能夠建立並使用本機 Blob、佇列和資料表。若要了解如何使用每個儲存體帳戶類型，請選取以下適當的連結：

	- [管理 Azure Blob 儲存體資源](./vs-azure-tools-storage-explorer-blobs.md)
	- 管理 Azure 佇列儲存體資源 - 敬請期待
	- 管理 Azure 表格儲存體資源 - 敬請期待

## 附加或卸離外部儲存體帳戶

儲存體 Explorer (預覽) 提供附加到外部儲存體帳戶的能力，因此可以輕鬆地共用儲存體帳戶。本節說明如何附加至 (及卸離) 外部儲存體帳戶。

### 取得儲存體帳戶認證

若要共用外部儲存體帳戶，該帳戶的擁有者必須先取得帳戶的認證 (帳戶名稱和金鑰)，然後與想要附加至該 (外部) 帳戶的人員分享該資訊。遵循下列步驟，即可透過 Azure 入口網站取得儲存體帳戶認證：

1.	登入 [Azure 入口網站](https://portal.azure.com)。
1.	選取 [瀏覽]。
1.	讀取 [儲存體帳戶]。
1.	在 [儲存體帳戶] 刀鋒視窗中，選取所需的儲存體帳戶。
1.	在所選儲存體帳戶的 [設定] 刀鋒視窗中，選取 [存取金鑰]。

	![][5]
	
1.	在 [存取金鑰] 刀鋒視窗中，複製 [儲存體帳戶名稱] 和 [金鑰 1] 值，以便在附加至儲存體帳戶時使用。

	![][6]

### 附加至外部儲存體帳戶

1.	在儲存體 Explorer (預覽) 中，以滑鼠右鍵按一下 [儲存體帳戶]，並從內容功能表中選取 [附加外部儲存體]。

	![][7]
	
1.	「取得儲存體帳戶認證」一節說明如何取得儲存體帳戶名稱和金鑰 1 值。這些值將用於這個步驟。在 [附加外部儲存體] 對話方塊中，請在 [帳戶金鑰] 方塊中輸入儲存體帳戶名稱，並在 [帳戶金鑰] 方塊中輸入金鑰 1 值。完成時選取 [確定]。

	![][8]

	附加後，外部儲存體帳戶將會與附加至儲存體帳戶名稱的文字 **(外部)** 一起顯示。

	![][9]

### 從外部儲存體帳戶卸離

1. 	以滑鼠右鍵按一下您要卸離的外部儲存體帳戶，並從內容功能表中選取 [卸離]。

	![][10]

1.	當確認訊息方塊出現時，請選取 [是] 已確認從外部儲存體帳戶卸離。

	![][12]

## 使用 SAS 附加帳戶

SAS (共用存取簽章) 讓 Azure 共用存取簽章的系統管理員能夠暫時授與儲存體帳戶的存取權，而不需提供其 Azure 訂用帳戶認證。

為了說明這點，我們假設 UserA 是 Azure 訂用帳戶的系統管理員，而且 UserA 想要允許 UserB 在有限的時間內使用特定權限存取儲存體帳戶：

1. UserA 可在特定期間內使用所需的權限產生 SAS (由儲存體帳戶的連接字串所組成)。
1. UserA 可與想要存取儲存體帳戶的人員共用 SAS - 在本例中為 UserB。  
1. UserB 可透過儲存體 Explorer (預覽)，使用所提供的 SAS 來附加至屬於 UserA 的帳戶。 

### 針對您要共用的帳戶取得 SAS

1.	開啟儲存體 Explorer (預覽)。
1.	在左窗格中，以滑鼠右鍵按一下您要共用的儲存體帳戶，並從內容功能表中選取 [取得共用存取簽章]。

	![][13]

1. 在 [共用存取簽章] 對話方塊中，指定您要用於此帳戶的時間範圍和權限，再選取 [建立]。

	![][14]
 
1. 第二個 [共用存取簽章] 對話方塊將會顯示，其中顯示 SAS。選取 [連接字串] 旁的 [複製]，將它複製到剪貼簿。選取 [關閉] 以關閉對話方塊。

### 使用 SAS 附加至共用帳戶

1.	開啟儲存體 Explorer (預覽)。
1.	在左窗格中，以滑鼠右鍵按一下 [儲存體帳戶]，並從內容功能表中選取 [使用 SAS 附加帳戶]。![][15]

1. 在 [使用 SAS 附加帳戶] 對話方塊上：

	- **帳戶名稱** - 輸入您想要與此帳戶產生關聯的名稱。**附註︰**帳戶名稱不必符合用於產生 SAS 的原始儲存體帳戶名稱。 
 	- **連接字串** - 貼上您先前複製的連接字串。
 	- 完成時選取 [確定]。
	
	![][16]

附加後，儲存體帳戶將會與附加至您提供之帳戶名稱的文字 (SAS) 一起顯示。

![][17]

## 使用 SAS 附加服務

[使用 SAS 附加帳戶](#attach-account-using-sas)一節說明 Azure 訂用帳戶系統管理員如何藉由產生 (及共用) 儲存體帳戶的 SAS 來授與儲存體帳戶的暫存存取權。同樣地，可以針對儲存體帳戶內的特定服務 (Blob 容器、佇列或資料表) 產生 SAS。

### 針對您要共用的服務產生 SAS

在此情況下，服務可以是 Blob 容器、佇列或資料表。下列各節說明如何針對所列的服務產生 SAS：

- [取得 Blob 容器的 SAS](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- 取得佇列的 SAS - 敬請期待
- 取得資料表的 SAS - 敬請期待

### 使用 SAS 附加至共用帳戶服務

1.	開啟儲存體 Explorer (預覽)。
1.	在左窗格中，以滑鼠右鍵按一下 [儲存體帳戶]，並從內容功能表中選取 [使用 SAS 附加服務]。![][18]

1. 在 [使用 SAS 附加帳戶] 對話方塊上，貼上您先前複製的 SAS URI，然後選取 [確定]。

	![][19]

附加後，新附加的服務會顯示在 [(服務 SAS)] 節點之下。

![][20]

## 搜尋儲存體帳戶

如果您有一長串的儲存體帳戶，使用左窗格頂端的 [搜尋] 方塊，可讓您快速尋找特定的儲存體帳戶。

當您在搜尋方塊中輸入資料時，左窗格只會顯示與您當時輸入的搜尋值相符的儲存體帳戶。以下螢幕擷取畫面說明當我搜尋到儲存體帳戶名稱包含 "tarcher" 文字的所有儲存體帳戶時的範例。

![][11]
	
若要清除搜尋，請在 [搜尋] 方塊中選取 **x** 按鈕。

## 後續步驟
- [使用儲存體 Explorer 來管理 Azure Blob 儲存體資源 (預覽)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->