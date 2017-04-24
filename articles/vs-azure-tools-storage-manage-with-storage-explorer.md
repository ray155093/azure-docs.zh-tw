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
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>開始使用儲存體 Explorer (預覽)
## <a name="overview"></a>概觀
Microsoft Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將學習連接及管理 Azure 儲存體帳戶的各種方式。

![Microsoft Azure 儲存體 Explorer (預覽)][15]

## <a name="prerequisites"></a>必要條件
* [下載並安裝儲存體 Explorer (預覽)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務
儲存體總管 (預覽) 提供許多方式來連接至儲存體帳戶。 其中包括連接到與您的 Azure 訂用帳戶相關聯的儲存體帳戶、連接到從其他 Azure 訂用帳戶共用的儲存體帳戶和服務，甚至是使用 Azure 儲存體模擬器連接及管理本機儲存體。 此外，您可以使用全球和國家 Azure 中的儲存體帳戶：

* [連接到 Azure 訂用帳戶](#connect-to-an-azure-subscription) - 管理屬於您的 Azure 訂用帳戶的儲存體資源。
* [使用本機開發儲存體](#work-with-local-development-storage) - 使用 Azure 儲存體模擬器管理本機儲存體。
* [連接至外部儲存體](#attach-or-detach-an-external-storage-account) - 使用儲存體帳戶的名稱、金鑰和端點，管理屬於另一個 Azure 訂用帳戶或在國家 Azure 雲端下的儲存體資源。
* [使用 SAS 附加儲存體帳戶](#attach-storage-account-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
* [使用 SAS 附加服務](#attach-service-using-sas) - 使用 SAS，管理屬於另一個 Azure 訂用帳戶的特定儲存體服務 (Blob 容器、佇列或資料表)。

## <a name="connect-to-an-azure-subscription"></a>連接到 Azure 訂用帳戶
> [!NOTE]
> 如果您沒有 Azure 帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
>
>

1. 在儲存體 Explorer (預覽) 中，選取 [Azure 帳戶設定] 。

    ![Azure 帳戶設定][0]
2. 左窗格會顯示您已登入的所有 Microsoft 帳戶。 若要連接到其他帳戶，請選取 [新增帳戶] ，並依照對話方塊使用與至少一個作用中 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。
> [!NOTE]
>目前不支援透過登入連接至國家 Azure (例如 Black Forest Azure、Fairfax Azure 和 Mooncake Azure)。 請參閱**附加或卸離外部儲存體帳戶**區段以了解如何連接至國家 Azure 儲存體帳戶。

3. 成功使用 Microsoft 帳戶登入後，左窗格會填入與該帳戶相關聯的 Azure 訂用帳戶。 選取您想要使用的訂用帳戶，然後選取 [套用]。 (選取 [所有訂用帳戶] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶。)

    ![選取 Azure 訂用帳戶][3]
4. 左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

## <a name="connect-to-an-azure-stack-subscription"></a>連線到 Azure Stack 訂用帳戶

1. 儲存體總管需要有 VPN 連線才能從遠端存取 Azure Stack 訂用帳戶。 若要深入了解如何設定 Azure Stack 的 VPN 連線，請參閱[使用 VPN 連線到 Azure Stack](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn)

2. 對於 Azure Stack POC，您必須匯出 Azure Stack 授權單位根憑證。 在 MAS-CON01、Azure Stack 主機電腦或具有 Azure Stack 之 VPN 連線的本機電腦上開啟 `mmc.exe`。 在 [檔案] 中，選取 [新增/移除嵌入式管理單元]，新增 [憑證] 以管理 [本機電腦]的 [電腦帳戶]。

   ![透過 mmc.exe 載入 Azure Stack 根憑證][25]   

   在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 之下尋找 **AzureStackCertificationAuthority**。 在項目上按一下滑鼠右鍵，選取 [所有工作] -> [匯出]。 依照對話方塊執行，使用 **Base-64 編碼的 X.509 (.CER)** 匯出憑證。 下一個步驟中會使用所匯出的憑證。   

   ![匯出根 Azure Stack 授權單位的根憑證][26]   

3. 在儲存體總管 (預覽) 中，依序選取 [編輯] 功能表、[SSL 憑證] 和 [匯入憑證]。 使用檔案選擇器對話方塊來尋找和開啟您在上一個步驟中瀏覽的憑證。 匯入之後，系統會提示您重新啟動儲存體總管。

   ![將憑證匯入儲存體總管 (預覽) 中][27]

4. 一旦儲存體總管 (預覽) 重新啟動，請選取 [編輯] 功能表，並確保已核取 [目標 Azure Stack]。 若未核取，請加以核取，然後重新啟動儲存體總管，變更才會生效。 不需要進行此設定，即可相容於 Azure Stack 環境。

   ![確保已選取目標 Azure Stack][28]

5. 在左側邊欄上選取 [管理帳戶]。 左窗格會顯示您已登入的所有 Microsoft 帳戶。 若要連線到 Azure Stack 帳戶，請選取 [新增帳戶]。

   ![新增 Azure Stack 帳戶][29]

6. 在 [新增帳戶] 對話方塊中選擇 [Azure 環境] 之下的 [建立自訂環境]，然後按 [下一步]。

7. 輸入 Azure Stack 自訂環境的所有必要資訊，然後按一下 [登入]。  填寫 [登入自訂雲端環境] 對話方塊，以使用至少與一個作用中 Azure Stack 訂用帳戶相關聯的 Azure Stack 帳戶進行登入。 此對話方塊上每個欄位的詳細資料如下所示︰

    * **環境名稱** – 使用者可以自訂此欄位。
    * **授權單位** – 此值應該是 https://login.windows.net。 若為 Azure 中國 (Mooncake)，請使用 https://login.chinacloudapi.cn。
    * **登入資源識別碼** – 藉由執行下列 PowerShell 來擷取此值︰

    如果您是雲端系統管理員︰

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    如果您是租用戶︰

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **圖表端點** – 此值應該是 https://login.windows.net。 若為 Azure 中國 (Mooncake)，請使用 https://graph.chinacloudapi.cn。
    * **ARM 資源識別碼** – 使用相同的值作為登入資源識別碼。
    * **ARM 資源端點** – ARM 資源端點的範例︰

    若為雲端系統管理員：https://adminmanagement.local.azurestack.external   
    若為租用戶：https://management.local.azurestack.external
 
    * **租用戶識別碼** – 選擇性。 只有在必須指定目錄時，才會提供此值。

8. 成功使用 Azure Stack 帳戶登入後，左窗格會填入與該帳戶相關聯的 Azure Stack 訂用帳戶。 選取您想要使用的 Azure Stack 訂用帳戶，然後選取 [套用]。 (選取 [所有訂用帳戶] 切換方塊，可選取全部或不選取任何列出的 Azure Stack 訂用帳戶。)

   ![在填妥 [自訂雲端環境] 對話方塊後選取 Azure Stack 訂用帳戶][30]

9. 左窗格會顯示與所選 Azure Stack 訂用帳戶相關聯的儲存體帳戶。

   ![包含 Azure Stack 訂用帳戶的儲存體帳戶清單][31]

## <a name="work-with-local-development-storage"></a>使用本機開發儲存體
儲存體 Explorer (預覽) 可讓您使用 Azure 儲存體模擬器處理本機儲存體。 這可讓您撰寫程式碼並測試儲存體，而不需在 Azure 上部署儲存體帳戶 (因為 Azure 儲存體模擬器正在模擬儲存體帳戶)。

> [!NOTE]
> Azure 儲存體模擬器目前只適用於 Windows。
>
>

1. 在儲存體 Explorer (預覽) 的左窗格中，依序展開 [(本機連接)] > [儲存體帳戶] > [(開發)] 節點。

    ![本機開發節點][21]
2. 如果您尚未安裝 Azure 儲存體模擬器，系統會透過資訊列提示您這麼做。 若已顯示資訊列，請選取 [下載最新版本] ，並安裝模擬器。

    ![下載 Azure 儲存體模擬器提示字元][22]
3. 安裝模擬器後，您就能夠建立並使用本機 blob、佇列和資料表。 若要了解如何使用每個儲存體帳戶類型，請選取下列其中一個連結：

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
若要附加至外部儲存體帳戶，您需要帳戶的名稱和金鑰。 「取得儲存體帳戶認證」  一節說明如何從 Azure 入口網站取得這些值。 不過，請注意在入口網站中，帳戶金鑰稱為「金鑰 1」，因此儲存體總管 (預覽) 會要求帳戶金鑰，您將會輸入 (或貼上)「金鑰 1」的值。

1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。

   ![連接至 Azure 儲存體選項][23]
2. 在 [連接到 Azure 儲存體] 對話方塊中，指定帳戶金鑰 (Azure 入口網站中的「金鑰 1」值)，然後選取 [下一步]。
> [!NOTE]
> 您可以透過國家 Azure 中的儲存體帳戶，來輸入儲存體連接字串。 例如，輸入與下列類似的連接字串，來連接至 Azure Black Forest 儲存體帳戶：DefaultEndpointsProtocol=https;AccountName=cawatest03;AccountKey=<storage_account_key>;EndpointSuffix=core.cloudapi.de；您可以透過如**取得儲存體帳戶認證**一節中所述的相同方式，從 Azure 入口網站取得連接字串

   ![連接至 Azure 儲存體對話方塊][24]

3. 在 [附加外部儲存體] 對話方塊中，請在 [帳戶名稱] 方塊中輸入儲存體帳戶名稱，指定任何其他所需的設定，然後在完成時選取 [下一步]。

   ![附加外部儲存體對話方塊][8]
4. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁] ，並重新輸入所需的設定。 完成後，請選取 [連接] 。
5. 連接後，顯示的外部儲存體帳戶會在儲存體帳戶名稱後面附加文字 **(外部)**。

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
3. 第二個 [共用存取簽章] 對話方塊會顯示 SAS。 選取 [連接字串] 旁的 [複製]，將它複製到剪貼簿。 選取 [關閉]  以關閉對話方塊。

### <a name="attach-to-the-shared-account-using-the-sas"></a>使用 SAS 附加至共用帳戶
1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。

   ![連接至 Azure 儲存體選項][23]
2. 在 [連接到 Azure 儲存體] 對話方塊中，指定連接字串，然後選取 [下一步]。

   ![連接至 Azure 儲存體對話方塊][24]
3. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁] ，並重新輸入所需的設定。 完成後，請選取 [連接] 。
4. 附加後，顯示的儲存體帳戶會在您提供的帳戶名稱後面附加文字 (SAS)。

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
3. 在 [連線摘要]  對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁] ，並重新輸入所需的設定。 完成後，請選取 [連接] 。
4. 附加後，新附加的服務會顯示在 [(服務 SAS)] 節點之下。

   ![使用 SAS 附加至共用服務的結果][20]

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶
如果您有一長串的儲存體帳戶，使用左窗格頂端的 [搜尋] 方塊，可讓您快速尋找特定的儲存體帳戶。

在搜尋方塊中輸入時，左窗格只會顯示與您在當時為止已輸入的搜尋值相符的儲存體帳戶。 以下螢幕擷取畫面說明當我搜尋到儲存體帳戶名稱包含 "tarcher" 文字的所有儲存體帳戶時的範例。

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
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png

