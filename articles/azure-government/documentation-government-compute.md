---
title: Azure Government 文件 | Microsoft Docs
description: 這為 Azure Government 的開發應用程式提供功能和指引的比較
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/29/2016
ms.author: ryansoc

---
# <a name="azure-government-compute"></a>Azure Government 計算
## <a name="virtual-machines"></a>虛擬機器
如需這項服務和使用方式的詳細資料，請參閱 [Azure 虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md)。

### <a name="variations"></a>變化
以下 VM SKU 已在 Azure Government 中公開上市 (GA)︰

| VM SKU | US Gov VA | US Gov IA | 注意事項 |
| --- | --- | --- | --- |
| A |GA |GA |None |
| Dv1 |GA |- |None |
| DSv1 |GA |- |None |
| Dv2 |GA |GA |15 即將推出 |
| F |GA |GA |None |
| G |已規劃 |- |None |

### <a name="data-considerations"></a>資料考量
下列資訊可識別 Azure 虛擬機器的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 在 VM 內輸入、儲存及處理的資料可以包含受到匯出管制的資料。 二進位檔案是在 Azure 虛擬機器內執行。 靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。 用來管理 Azure 平台元件之憑證的私密金鑰。 SQL 連接字串。  其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 |中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護 Azure 虛擬機器時所輸入的所有組態資料。  請勿輸入受管制/受控制資料到下列欄位︰租用戶角色名稱、資源群組、部署名稱、資源名稱、資源標籤 |

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>

<!--HONumber=Oct16_HO2-->


