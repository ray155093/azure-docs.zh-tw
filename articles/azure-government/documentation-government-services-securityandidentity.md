---
title: "Azure Government 安全性和身分識別 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security--identity"></a>Azure Government 安全性和身分識別
## <a name="key-vault"></a>金鑰保存庫
如需這項服務和使用方式的詳細資料，請參閱 [Azure 金鑰保存庫公開文件](../key-vault/index.md)。

### <a name="data-considerations"></a>資料考量
下列資訊可識別 Azure 金鑰保存庫的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 使用 Azure 金鑰保存庫金鑰來加密的所有資料都可包含受管制/受控制資料。 |Azure 金鑰保存庫中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護金鑰保存庫時所輸入的所有組態資料。  請勿輸入受管制/受控制資料到下列欄位︰資源群組名稱、金鑰保存庫名稱、訂用帳戶名稱 |

金鑰保存庫已在 Azure Government 中正式推出。 和公開版一樣，其中沒有擴充功能，所以只可透過 PowerShell 和 CLI 取得金鑰保存庫。

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>




<!--HONumber=Nov16_HO3-->


