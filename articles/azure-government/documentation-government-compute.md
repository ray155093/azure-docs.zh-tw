---
title: "Azure Government 計算 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 146fcd9788bab55e4abd9e70dcfe25741f041d34
ms.lasthandoff: 03/15/2017


---
# <a name="azure-government-compute"></a>Azure Government 計算
## <a name="virtual-machines"></a>虛擬機器
如需這項服務和使用方式的詳細資料，請參閱 [Azure 虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="variations"></a>變化
Azure Government 中提供這些產品 (VM)︰

| 產品 (VM) | US Gov VA | US Gov IA | 美國 DOD 東部 | 美國 DOD 西部
| --- | --- | --- |--- |--- |
| A0-A7 |Y |Y |Y |Y |Y |
| Av2 |Y |Y |Y |Y |Y |
| D 系列 |Y |N |N |N |
| Dv2 系列 |Y |Y |Y |Y |
| DS 系列 |Y |N |N |N |
| DSv2 系列 |Y |N |Y |Y |
| F 系列 |Y |Y |Y |Y |
| FS 系列 |Y |N |Y |Y |
| G 系列 |Y |N |N |N |
| GS 系列 |Y |N |N |N |

### <a name="data-considerations"></a>資料考量
下列資訊可識別 Azure 虛擬機器的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 在 VM 內輸入、儲存及處理的資料可以包含受到匯出管制的資料。 二進位檔案是在 Azure 虛擬機器內執行。 靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。 用來管理 Azure 平台元件之憑證的私密金鑰。 SQL 連接字串。  其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 |中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護 Azure 虛擬機器時所輸入的所有組態資料。  請勿輸入受管制/受控制資料到下列欄位︰租用戶角色名稱、資源群組、部署名稱、資源名稱、資源標籤 |

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>


