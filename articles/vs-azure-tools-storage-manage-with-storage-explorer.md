---
title: "開始使用儲存體總管 (預覽) | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: fbcd35529c5d2360f5b0c9de4d3c9c4a08a0cc8f
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017


---
# <a name="get-started-with-storage-explorer-preview"></a>開始使用儲存體總管 (預覽)
## <a name="overview"></a>概觀
Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將學習連接及管理 Azure 儲存體帳戶的各種方式。

![Microsoft Azure 儲存體 Explorer (預覽)][15]

## <a name="prerequisites"></a>必要條件
* [下載並安裝儲存體總管 (預覽)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務
儲存體總管 (預覽) 提供數種方式來連線至儲存體帳戶。 例如，您可以：
* 連線至與您的 Azure 訂用帳戶相關聯的儲存體帳戶。
* 連線至從其他 Azure 訂用帳戶共用的儲存體帳戶和服務。
* 使用 Azure 儲存體模擬器連線到本機儲存體並加以管理。 

此外，您可以使用全球和國家 Azure 中的儲存體帳戶：

* [連線至 Azure 訂用帳戶](#connect-to-an-azure-subscription)：管理屬於您的 Azure 訂用帳戶的儲存體資源。
* [使用本機開發儲存體](#work-with-local-development-storage)：使用 Azure 儲存體模擬器管理本機儲存體。
* [連接至外部儲存體](#attach-or-detach-an-external-storage-account)：使用儲存體帳戶的名稱、金鑰和端點，管理屬於另一個 Azure 訂用帳戶或在國家 Azure 雲端下的儲存體資源。
* [使用 SAS 連結儲存體帳戶](#attach-storage-account-using-sas)：使用共用存取簽章 (SAS)，管理屬於另一個 Azure 訂用帳戶的儲存體資源。
* [使用 SAS 連結服務](#attach-service-using-sas)：使用 SAS，管理屬於另一個 Azure 訂用帳戶的特定儲存體服務 (Blob 容器、佇列或資料表)。

## <a name="connect-to-an-azure-subscription"></a>連接到 Azure 訂用帳戶
> [!NOTE]
> 如果您沒有 Azure 帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
>
>

1. 在儲存體 Explorer (預覽) 中，選取 [Azure 帳戶設定] 。

    ![Azure 帳戶設定][0]

2. 左窗格會顯示您已登入的所有 Microsoft 帳戶。 若要連線至其他帳戶，請選取 [新增帳戶]，然後依照指示使用與至少一個作用中 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。

    >[!NOTE]
    >目前不支援連線至國家 Azure (例如透過登入連線至 Azure Germany、Azure Government 和 Azure China)。 請參閱「連結或中斷連結外部儲存體帳戶」一節，以了解如何連線至國家 Azure 儲存體帳戶。

3. 使用 Microsoft 帳戶成功登入後，左窗格會填入與該帳戶相關聯的 Azure 訂用帳戶。 選取您想要使用的 Azure 訂用帳戶，然後選取 [套用]。 (選取 [所有訂用帳戶] 切換方塊，可選取全部或不選取任何列出的 Azure 訂用帳戶。)

    ![選取 Azure 訂用帳戶][3]  
    左窗格會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

## <a name="connect-to-an-azure-stack-subscription"></a>連線到 Azure Stack 訂用帳戶

您需要有 VPN 連線，儲存體總管才能從遠端存取 Azure Stack 訂用帳戶。 若要深入了解如何設定 Azure Stack 的 VPN 連線，請參閱[使用 VPN 連線到 Azure Stack](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn)。

對於 Azure Stack 概念證明 (POC)，您必須匯出 Azure Stack 授權單位根憑證。 若要這樣做：

1. 在 MAS-CON01、Azure Stack 主機電腦或具有 Azure Stack 之 VPN 連線的本機電腦上開啟 `mmc.exe`。 

2. 在 [檔案] 中，選取 [新增/移除嵌入式管理單元]，然後新增 [憑證] 以管理 [本機電腦] 的 [電腦帳戶]。

    ![透過 mmc.exe 載入 Azure Stack 根憑證][25]   

3. 在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 之下尋找 **AzureStackCertificationAuthority**。 

4. 以滑鼠右鍵按一下此項目，選取 [所有工作] > [匯出]，然後依照指示，匯出 **Base-64 encoded X.509 (.CER)** 憑證。  

    下一個步驟中會使用所匯出的憑證。   

    ![匯出根 Azure Stack 授權單位的根憑證][26]   

5. 在儲存體總管 (預覽) 中，在 [編輯] 功能表上，指向 [SSL 憑證]，然後選取 [匯入憑證]。 使用檔案選擇器對話方塊來尋找和開啟您在上一個步驟中瀏覽的憑證。  

    匯入之後，系統會提示您重新啟動儲存體總管。

    ![將憑證匯入儲存體總管 (預覽) 中][27]

6. 儲存體總管 (預覽) 重新啟動後，請選取 [編輯] 功能表，然後確保已選取 [目標 Azure Stack]。 若未選取，請加以選取，然後重新啟動儲存體總管，變更才會生效。 不需要進行此設定，即可相容於 Azure Stack 環境。

    ![確保已選取目標 Azure Stack][28]

7. 在左側窗格中，選取 [管理帳戶]。  
    隨即顯示您已登入的所有 Microsoft 帳戶。

8. 若要連線到 Azure Stack 帳戶，請選取 [新增帳戶]。

    ![新增 Azure Stack 帳戶][29]

9. 在 [新增帳戶] 對話方塊中，選取 [Azure 環境] 之下的 [建立自訂環境]，然後按 [下一步]。

10. 輸入 Azure Stack 自訂環境的所有必要資訊，然後按一下 [登入]。 

11. 若要使用至少與一個作用中 Azure Stack 訂用帳戶相關聯的 Azure Stack 帳戶進行登入，請填寫 [登入自訂雲端環境] 對話方塊。  

    每個欄位的詳細資料如下所示︰

    * **環境名稱**：使用者可以自訂此欄位。
    * **授權單位**：此值應該是 https://login.windows.net。 若為 Azure China，請使用 https://login.chinacloudapi.cn。
    * **登入資源識別碼**：藉由執行下列 PowerShell 指令碼來擷取此值︰

        如果您是雲端系統管理員︰

        ```powershell
        PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
        ```

        如果您是租用戶︰

        ```powershell
        PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
        ```

    * **圖表端點**：此值應該是 https://login.windows.net。 若為 Azure China，請使用 https://graph.chinacloudapi.cn。
    * **ARM 資源識別碼**：使用相同的值作為**登入資源識別碼**。
    * **ARM 資源端點**：Azure Resource Manager 資源端點的範例︰

        * 若為雲端系統管理員：https://adminmanagement.local.azurestack.external   
        * 若為租用戶：https://management.local.azurestack.external
 
    * **租用戶識別碼**：選擇性。 只有在必須指定目錄時，才會提供此值。

12. 成功使用 Azure Stack 帳戶登入後，左窗格會填入與該帳戶相關聯的 Azure Stack 訂用帳戶。 選取您想要使用的 Azure Stack 訂用帳戶，然後選取 [套用]。 (選取或清除 [所有訂用帳戶] 核取方塊切換開關，可選取全部或不選取任何列出的 Azure Stack 訂用帳戶。)

    ![在填妥 [自訂雲端環境] 對話方塊後選取 Azure Stack 訂用帳戶][30]  
    左窗格會顯示與所選 Azure Stack 訂用帳戶相關聯的儲存體帳戶。

    ![包含 Azure Stack 訂用帳戶的儲存體帳戶清單][31]

## <a name="work-with-local-development-storage"></a>使用本機開發儲存體
使用儲存體總管 (預覽)，您可以使用 Azure 儲存體模擬器來處理本機儲存體。 此方法可讓您撰寫程式碼並測試儲存體，而不需在 Azure 上部署儲存體帳戶，因為 Azure 儲存體模擬器正在模擬儲存體帳戶。

> [!NOTE]
> Azure 儲存體模擬器目前只適用於 Windows。
>
>

1. 在儲存體總管 (預覽) 的左窗格中，依序展開 [(本機連結)] > [儲存體帳戶] > [(開發)] 節點。

    ![本機開發節點][21]

2. 如果您尚未安裝 Azure 儲存體模擬器，系統會透過資訊列提示您這麼做。 若已顯示資訊列，請選取 [下載最新版本] ，然後安裝模擬器。

    ![下載 Azure 儲存體模擬器提示字元][22]

3. 安裝模擬器之後，您即可建立並使用本機 blob、佇列和資料表。 若要了解如何使用每個儲存體帳戶類型，請參閱下列其中一項：

    * [管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
    * 管理 Azure 檔案共用儲存體資源：敬請期待 
    * 管理 Azure 佇列儲存體資源：敬請期待 
    * 管理 Azure 表格儲存體資源：敬請期待 

## <a name="attach-or-detach-an-external-storage-account"></a>附加或卸離外部儲存體帳戶
使用儲存體總管 (預覽)，您可以連結至外部儲存體帳戶，因此可以輕鬆地共用儲存體帳戶。 本節說明如何附加至 (及卸離) 外部儲存體帳戶。

### <a name="get-the-storage-account-credentials"></a>取得儲存體帳戶認證
若要共用外部儲存體帳戶，該帳戶的擁有者必須先取得帳戶的認證 (帳戶名稱和金鑰)，然後與想要連結至該 (外部) 帳戶的人員分享該資訊。 執行下列作業，即可透過 Azure 入口網站取得儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [瀏覽] 。

3. 選取 [儲存體帳戶] 。

4. 在 [儲存體帳戶]  刀鋒視窗中，選取所需的儲存體帳戶。

5. 在所選儲存體帳戶的 [設定] 刀鋒視窗中，選取 [存取金鑰]。

    ![存取金鑰選項][5]

6. 在 [存取金鑰] 刀鋒視窗上，複製 [儲存體帳戶名稱] 和 [金鑰1] 值，以便在連結至儲存體帳戶時使用。

    ![存取金鑰][6]

### <a name="attach-to-an-external-storage-account"></a>附加至外部儲存體帳戶
若要附加至外部儲存體帳戶，您需要帳戶的名稱和金鑰。 「取得儲存體帳戶認證」一節說明如何從 Azure 入口網站取得這些值。 不過，在入口網站中，帳戶金鑰稱為 [金鑰1]。 因此，當儲存體總管 (預覽) 要求帳戶金鑰時，您會輸入 [金鑰 1] 值。

1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。

    ![連接至 Azure 儲存體選項][23]

2. 在 [連線至 Azure 儲存體] 對話方塊中，指定帳戶金鑰 (Azure 入口網站中的 [金鑰 1] 值)，然後選取 [下一步]。

    > [!NOTE]
    > 您可以透過國家 Azure 中的儲存體帳戶，輸入儲存體連接字串。 例如，若要連線至 Azure Germany 儲存體帳戶，請輸入如下的連接字串︰ 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >如「取得儲存體帳戶認證」一節所述，您可以使用相同的方式從 Azure 入口網站取得連接字串。

    ![連線至 Azure 儲存體對話方塊][24]

3. 在 [連結外部儲存體] 對話方塊中，於 [帳戶名稱] 方塊中輸入儲存體帳戶名稱，指定任何其他所需的設定，然後選取 [下一步]。

    ![連結外部儲存體對話方塊][8]

4. 在 [連線摘要] 對話方塊中，確認資訊。 如果您想要變更任何項目，請選取 [上一頁] ，並重新輸入所需的設定。 

5. 選取 [ **連接**]。

6. 成功連線後，顯示的外部儲存體帳戶會在儲存體帳戶名稱後面附加 **(外部)**。

    ![連接至外部儲存體帳戶的結果][9]

### <a name="detach-from-an-external-storage-account"></a>從外部儲存體帳戶卸離
1. 以滑鼠右鍵按一下您要卸離的外部儲存體帳戶，然後選取 [卸離]。

    ![中斷與儲存體選項的連結][10]

2. 在確認訊息中，選取 [是] 以確認從外部儲存體帳戶卸離。

## <a name="attach-a-storage-account-by-using-an-sas"></a>使用 SAS 連結儲存體帳戶
[SAS](storage/storage-dotnet-shared-access-signature-part-1.md) 可讓 Azure 訂用帳戶的系統管理員授與儲存體帳戶的暫時存取權，而不需提供 Azure 訂用帳戶認證。

為了說明此案例，我們假設 UserA 是 Azure 訂用帳戶的系統管理員，而且 UserA 想要允許 UserB 在有限的時間內使用特定權限存取儲存體帳戶：

1. UserA 可在特定期間內使用所需的權限產生 SAS (由儲存體帳戶的連接字串所組成)。

2. UserA 可與想要存取儲存體帳戶的人員 (在本例中為 UserB) 共用 SAS。  

3. UserB 可透過儲存體總管 (預覽)，使用所提供的 SAS 來連結至屬於 UserA 的帳戶。

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>針對您要共用的帳戶取得 SAS
1. 在儲存體總管 (預覽) 中，以滑鼠右鍵按一下您要共用的儲存體帳戶，然後選取 [取得共用存取簽章]。

    ![取得 SAS 內容功能表選項][13]

2. 在 [共用存取簽章] 對話方塊中，指定您要用於此帳戶的時間範圍和權限，然後選取 [建立]。

    ![取得 SAS 對話方塊][14]  
    [共用存取簽章] 對話方塊隨即開啟並顯示 SAS。

3. 選取 [連接字串] 旁的 [複製]，將它複製到剪貼簿，然後選取 [關閉]。

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>使用 SAS 連結至共用帳戶
1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。

    ![連接至 Azure 儲存體選項][23]

2. 在 [連線至 Azure 儲存體] 對話方塊中，指定連接字串，然後選取 [下一步]。

    ![連線至 Azure 儲存體對話方塊][24]

3. 在 [連線摘要] 對話方塊中，確認資訊。 若要進行變更，請選取 [上一頁]，然後輸入您想要的設定。 

4. 選取 [ **連接**]。

5. 連結之後，顯示的儲存體帳戶會在您提供之帳戶名稱後面附加 **(SAS)**。

    ![使用 SAS 連結至帳戶的結果][17]

## <a name="attach-a-service-by-using-an-sas"></a>使用 SAS 連結服務
「使用 SAS 連結儲存體帳戶」一節說明 Azure 訂用帳戶系統管理員如何藉由產生及共用儲存體帳戶的 SAS，來授與儲存體帳戶的暫存存取權。 同樣地，可以針對儲存體帳戶內的特定服務 (Blob 容器、佇列或資料表) 產生 SAS。  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>針對您要共用的服務產生 SAS
在此情況下，服務可以是 Blob 容器、佇列或資料表。 若要產生所列服務的 SAS，請參閱︰

* [取得 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 取得檔案共用的 SAS：敬請期待
* 取得佇列的 SAS：敬請期待
* 取得資料表的 SAS：敬請期待

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>使用 SAS 連結至共用帳戶服務
1. 在儲存體 Explorer (預覽) 中，選取 [連接到 Azure 儲存體] 。

    ![連接至 Azure 儲存體選項][23]

2. 在 [連線至 Azure 儲存體] 對話方塊中，指定 SAS URI，然後選取 [下一步]。

    ![連線至 Azure 儲存體對話方塊][24]

3. 在 [連線摘要] 對話方塊中，確認資訊。 若要進行變更，請選取 [上一頁]，然後輸入您想要的設定。 

4. 選取 [ **連接**]。

5. 連結之後，新連結的服務會顯示在 [(服務 SAS)] 節點之下。

    ![使用 SAS 連結至共用服務的結果][20]

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶
如果您有一長串的儲存體帳戶，使用左窗格頂端的 [搜尋] 方塊，可讓您快速尋找特定的儲存體帳戶。

在搜尋方塊中輸入時，左窗格會顯示與您在當時為止已輸入的搜尋值相符的儲存體帳戶。 例如，搜尋名稱包含 **tarcher** 的所有儲存體帳戶會如下列螢幕擷取畫面所示︰

![儲存體帳戶搜尋][11]

## <a name="next-steps"></a>後續步驟
* [使用儲存體總管 (預覽) 來管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)

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

