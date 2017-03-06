---
title: "Azure Government 儲存體 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e
ms.lasthandoff: 02/08/2017


---
# <a name="azure-government-storage"></a>Azure Government 儲存體
## <a name="azure-storage"></a>Azure 儲存體
如需這項服務和使用方式的詳細資料，請參閱 [Azure 儲存體公開文件](../storage/index.md)。

### <a name="storage-service-availability-by-azure-government-region"></a>依 Azure Government 區域區分的儲存體服務可用性

| 服務 | 美國政府維吉尼亞州 | 美國政府愛荷華州 | 注意事項
| --- | --- | --- | --- |
| [Blob 儲存體] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [表格儲存體] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [佇列儲存體] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [檔案儲存體] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [熱/冷Blob 儲存體] (../storage/storage-blob-storage-tiers.md) |NA |NA |
| [儲存體服務加密] (../storage/storage-service-encryption.md) |GA |GA |
| [進階儲存體] (../storage/storage-premium-storage.md) |GA |NA | 包括 DS 系列的虛擬機器。 |
| [Blob 匯入/匯出] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

### <a name="variations"></a>變化
Azure Government 中的儲存體帳戶 URL 不同：

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| Blob 儲存體 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 佇列儲存體 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 資料表儲存體 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| 檔案儲存體 |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> 所有指令碼和程式碼都必須說明適當的端點。  請參閱[設定 Azure 儲存體連接字串](../storage/storage-configure-connection-string.md)。 
>
>

如需 API 的詳細資訊，請參閱 <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> 雲端儲存體帳戶建構函式</a>。

要在這些多載中使用的端點尾碼是 core.usgovcloudapi.net

> [!NOTE]
> [Microsoft Azure 儲存體總管] (../vs-azure-tools-storage-manage-with-storage-explorer.md) 目前不支援以在 Azure Government 中新增帳戶的方式 [連接至 Azure 訂用帳戶] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription)。 使用其他方法 [連接到儲存體帳戶] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)。
當 [附加至外部儲存體帳戶] 時 (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account)，**步驟 3** 的儲存體端點網域請選取 [其他 (於下方指定)]，並指定 Azure Government 的 **core.usgovcloudapi.net**。
>
>

> [!NOTE]
> 當 [掛接檔案共用] 時 (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share)， 如果傳回錯誤&53;「找不到網路路徑」。 可能是因為防火牆封鎖了輸出連接埠。 請嘗試將檔案共用掛接在與儲存體帳戶相同 Azure 訂用帳戶中的 VM 上。
>
>

> [!NOTE]
> 部署「StorSimple Manager 服務」時，請針對 Azure 入口網站和「傳統」入口網站分別使用 https://portal.azure.us/ 和 https://manage.windowsazure.us/ URL。 如需 StorSimple Virtual Array 的部署指示，請參閱 [StorSimple Virtual Array 系統需求] (../storsimple/storsimple-ova-system-requirements.md)，如需 StorSimple 8000 系列的相關指示，請參閱 [StorSimple 軟體、高可用性和網路需求] (../storsimple/storsimple-system-requirements.md)，然後從左側瀏覽區移至 [部署] 區段。 如需一般 StorSimple 文件，請參閱[什麼是 StorSimple？] (../storsimple/index.md)。
>
>

### <a name="considerations"></a>考量
下列資訊可識別 Azure 儲存體的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 在 Azure 儲存體產品內輸入、儲存及處理的資料可以包含受到匯出管制的資料。 靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。 用來管理 Azure 平台元件之憑證的私密金鑰。 其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 |Azure 儲存體中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護儲存體產品時所輸入的所有組態資料。  請不要將受管制/受控制資料輸入至下列欄位︰資源群組、部署名稱、資源名稱、資源標籤 |

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格</a>。

