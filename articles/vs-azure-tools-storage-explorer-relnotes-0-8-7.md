---
title: "Microsoft Azure 儲存體總管 0.8.7 (預覽)| Microsoft Docs"
description: "Microsoft Azure 儲存體總管 0.8.7 (預覽) 的版本資訊"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Microsoft Azure 儲存體總管 0.8.7 (預覽)
## <a name="overview"></a>概觀
本文包含 Azure 儲存體總管 0.8.7 預覽版本的版本資訊。

[Microsoft Azure 儲存體總管 (預覽)](./vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立 App，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。

## <a name="azure-storage-explorer-087-preview"></a>Azure 儲存體總管 0.8.7 (預覽)
### <a name="download-azure-storage-explorer-087-preview"></a>下載 Azure 儲存體總管 0.8.7 (預覽)
- [適用於 Windows 的 Azure 儲存體總管 0.8.7 (預覽)](https://go.microsoft.com/fwlink/?LinkId=708343)
- [適用於 Mac 的 Azure 儲存體總管 0.8.7 (預覽)](https://go.microsoft.com/fwlink/?LinkId=708342)
- [適用於 Linux 的 Azure 儲存體總管 0.8.7 (預覽)](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>新的更新
* 在剛開始更新、下載或複製工作階段時，您可以在 [活動] 視窗中選擇如何解決衝突。
* 將游標暫留在索引標籤上可以看到儲存體資源的完整路徑。
* 按一下索引標籤之後，它會與左側導覽窗格中的位置同步。

### <a name="fixes"></a>修正
* 已修正：「儲存體總管」在 macOS 上現已是受信任的 App。
* 已修正：再次支援 Ubuntu 14.04。
* 已修正：「新增帳戶」UI 在載入訂用帳戶時有時會閃爍。
* 已修正：左側導覽窗格中有時不會列出所有儲存體資源。
* 已修正：[動作] 窗格有時會顯示空白動作。
* 已修正：現已會保留上次工作階段關閉時的視窗大小。
* 已修正：可以使用內容功能表針對相同資源開啟多個索引標籤。

### <a name="known-issues"></a>已知問題
* 「快速存取」僅適用於訂用帳戶相關項目。 此版本不支援透過金鑰或 SAS 權杖附加的本機資源或資源。
* 視您擁有的資源數量而定，「快速存取」可能需要幾秒鐘才能瀏覽至目標資源。
* 同時上傳超過三個的 Blob 或檔案群組可能會造成錯誤。
* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能或造成未處理的例外狀況。
* 在 macOS 上第一次使用「儲存體總管」時，系統可能會多次要求使用者的許可，以存取鑰匙圈。 建議您選取 [保持開放]，這樣就不會再出現提示

## <a name="previous-releases"></a>舊版
### <a name="features"></a>特性
#### <a name="main-features"></a>主要功能
* macOS 、Linux 及 Windows 版
* 登入以根據訂用帳戶來瀏覽您的儲存體帳戶：
    * 使用您的組織帳戶、Microsoft 帳戶、2FA 等等。
    * 設定及管理 Proxy 設定
    * 登出以移除帳戶
* 使用下列方式連線到儲存體帳戶：
    * 帳戶名稱和金鑰
    * 自訂端點 (包含 Azure China)
    * 儲存體帳戶的 SAS URI
* Azure Resource Manager 和傳統儲存體支援
* 產生 Blob、Blob 容器、佇列、資料表或檔案共用的 SAS 金鑰
* 使用共用存取簽章 (SAS) 連線到 Blob 容器、佇列、資料表或檔案共用
* 管理 Blob 容器、佇列、資料表或檔案共用的「Stored Access Policies」
* 使用「儲存體模擬器」進行本機儲存體開發 (僅限 Windows)
* 建立及刪除 Blob 容器、佇列或資料表
* 檢視 $logs Blob 容器和 $metrics 資料表
* 搜尋特定的 Blob、佇列、資料表或檔案共用
* 直接連結到儲存體帳戶或容器、佇列、資料表或檔案共用，以便共用及輕鬆存取資源
* 重新命名 Blob 容器、資料表、檔案共用
* 能夠管理及設定 CORS 規則
* 輕鬆複製儲存體帳戶的主要和次要金鑰
* 針對上傳及下載資料的 MD5 進行檢查以確保資料完整性和一致性
* 從搜尋方塊搜尋 Blob 容器、資料表、佇列、檔案共用或儲存體帳戶
* 現已可將常用的服務釘選到 Quick Access 以便瀏覽
* 現已可在多個索引標籤開啟多個編輯器。 按一下以開啟暫時索引標籤；按兩下以開啟永久索引標籤。 您也可以按一下暫時索引標籤來將它設為永久索引標籤
* 大幅改善上傳及下載的效能和穩定性，尤其是在高效能機器上處理大型檔案的情況
* 重新推出增強的範圍搜尋，並加入「範圍」的概念。 透過暫留圖示，按一下滑鼠右鍵 -> [搜尋此處] 來輸入節點路徑，或以手動的方式將範圍設定在該節點。 一旦設定範圍，您就可以在路徑的結尾加上搜尋詞彙，以從該節點開始深層搜尋
* 已新增多種佈景主題：淺色 (預設)、深色、黑色高對比和白色高對比。
* 移至 [編輯] -> [佈景主題] 來變更佈景主題喜好設定
* 若使用 Linux，必須為 64 位元作業系統
* 已更新我們的標誌！
#### <a name="blobs"></a>Blob
* 檢視 Blob 並瀏覽目錄
* 上傳、下載、刪除及複製 Blob 和資料夾
* 開啟及檢視內容文字和圖片 Blob
* 檢視及編輯 Blob 屬性和中繼資料
* 使用前置詞搜尋 Blob
* 建立及中斷 Blob 和 Blob 容器的租約
* 拖放檔案以上傳
* 重新命名 Blob 和資料夾
* 現可在 Blob 容器內建立空白的「虛擬」資料夾
* 可修改 Blob 和檔案的屬性
#### <a name="tables"></a>資料表
* 使用 ODATA 來檢視及查詢實體，或使用查詢產生器以建立複雜查詢
* 新增、編輯、刪除實體
* 以 CSV 格式匯入及匯出資料表內容 (包含匯出查詢結果)
* 自訂資料行順序
* 能夠儲存查詢
#### <a name="queues"></a>佇列
* 快速查看最近的 32 個訊息
* 新增、清除佇列、檢視訊息
* 清除佇列
* 您現在可以決定是否要對佇列訊息進行編碼/解碼
#### <a name="file-shares"></a>檔案共用
* 檢視檔案及瀏覽目錄
* 上傳、下載、刪除及複製檔案和目錄
* 檢視檔案內容
* 重新命名檔案和目錄

### <a name="bug-fixes"></a>錯誤修正
* 已修正︰畫面凍結的問題
* 已修正：增強安全性

### <a name="known-issues"></a>已知問題
* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能，macOS 安裝可能需要提高權限
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案和實體的所有其他屬性和中繼資料都會在重新命名期間保留
* Azure Stack 目前不支援檔案，因此嘗試展開 [檔案] 節點會導致錯誤
* 在 Linux 14.04 安裝需要更新或升級 gcc 版本。 下列步驟說明如何升級：

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>舊版
#### <a name="october-2016-release-version-085"></a>2016 年 10 月版本 (0.8.5 版)
* [下載 Windows 版](https://go.microsoft.com/fwlink/?LinkId=809306)
* [下載 Mac 版](https://go.microsoft.com/fwlink/?LinkId=809307)
* [下載 Linux 版](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->


