---
title: "Azure Government 儲存體 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Azure Government 儲存體
## <a name="azure-storage"></a>Azure 儲存體
如需這項服務和使用方式的詳細資料，請參閱 [Azure 儲存體公開文件](../storage/index.md)。

### <a name="variations"></a>變化
Azure Government 中的儲存體帳戶 URL 不同：

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| Blob 儲存體 |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| 佇列儲存體 |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| 資料表儲存體 |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> 所有指令碼和程式碼都必須說明適當的端點。  請參閱[設定 Azure 儲存體連接字串](../storage/storage-configure-connection-string.md)。 
>
>

如需 API 的詳細資訊，請參閱 <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> 雲端儲存體帳戶建構函式</a>。

要在這些多載中使用的端點尾碼是 core.usgovcloudapi.net

### <a name="considerations"></a>考量
下列資訊可識別 Azure 儲存體的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 在 Azure 儲存體產品內輸入、儲存及處理的資料可以包含受到匯出管制的資料。 靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。 用來管理 Azure 平台元件之憑證的私密金鑰。 其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 |Azure 儲存體中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護儲存體產品時所輸入的所有組態資料。  請不要將受管制/受控制資料輸入至下列欄位︰資源群組、部署名稱、資源名稱、資源標籤 |

## <a name="premium-storage"></a>進階儲存體
如需這個服務和其使用方式的詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。

### <a name="variations"></a>變化
進階儲存體已在美國政府維吉尼亞州全面上市。 這包括 DS 系列的虛擬機器。

### <a name="considerations"></a>考量
上方列出的相同儲存體資料考量適用於進階儲存體帳戶。

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格</a>。



<!--HONumber=Nov16_HO3-->


