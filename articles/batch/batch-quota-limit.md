---
title: Batch 服務配額和限制 | Microsoft Docs
description: 了解預設的 Azure Batch 配額、限制和條件約束，以及如何要求增加配額
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2016
ms.author: marsma

---
# Azure Batch 服務的配額和限制
如同使用其他 Azure 服務，對於與 Batch 服務相關聯的特定資源有一些限制。這其中有許多限制是 Azure 在訂用帳戶或帳戶層級上所套用的預設配額。本文將討論這些預設值，以及如何申請加大配額。

如果您計劃在 Batch 中執行生產工作負載，您可能需要增加一或多個高於預設值的配額。若要加大配額，您可以開啟線上[客戶支援要求](#increase-a-quota)，不另外加收費用。

> [!NOTE]
> 配額是一種信用限制，不是容量保證。如果您有大規模的容量需求，請連絡 Azure 支援。
> 
> 

## 訂用帳戶配額
| **Resource** | **預設限制** | **上限** |
| --- | --- | --- |
| Batch 帳戶 (每一區域的每一訂用帳戶) |1 |50 |

## Batch 帳戶配額
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## 其他限制
| **Resource** | **上限** |
| --- | --- |
| 每個計算節點的[並行工作](batch-parallel-node-tasks.md) |4 x 節點的核心數目 |
| 每一 Batch 帳戶的[應用程式](batch-application-packages.md) |20 |
| 每個應用程式的應用程式封裝 |40 |
| 應用程式封裝大小 (每一個) |大約 195 GB<sup>1</sup> |

<sup>1</sup> 對於區塊 Blob 大小上限的 Azure 儲存體限制

## 檢視 Batch 配額
在 [Azure 入口網站][portal]中檢視您的 Batch 帳戶配額。

1. 在入口網站中選取 [Batch 帳戶]，然後選取您感興趣的 Batch 帳戶。
2. 在 Batch 帳戶的功能表刀鋒視窗上選取 [屬性]
3. [屬性] 刀鋒視窗會顯示目前套用至 Batch 帳戶的「配額」
   
    ![Batch 帳戶配額][account_quotas]

## 增加配額
遵循下列步驟，使用 [Azure 入口網站][portal]來申請加大配額。

1. 選取入口網站儀表板上的 [說明 + 支援] 圖格或入口網站右上角的問號 (**？**)。
2. 選取 [新增支援要求] > [基本]。
3. 在 [基本] 刀鋒視窗上：
   
    a.[問題類型] > [配額]
   
    b.選取您的訂用帳戶。
   
    c.[配額類型] > [Batch]
   
    d.[支援方案] > [配額支援 - 已包含]
   
    按 [下一步]。
4. 在 [問題] 刀鋒視窗上：
   
    a.根據[商業影響][support_sev]選取 [嚴重性]。
   
    b.在 [詳細資料] 中，指定每個您想要變更的配額、Batch 帳戶名稱和新限制。
   
    按 [下一步]。
5. 在 [連絡資訊] 刀鋒視窗上：
   
    a.選取 [偏好的連絡方式]。
   
    b.確認並輸入必要的連絡人詳細資料。
   
    按一下 [建立] 提交支援要求。

一旦您上傳支援要求，Azure 支援會與您連絡。請注意，完成要求最多需要花費 2 個工作天。

## 相關主題
* [使用 Azure 入口網站建立 Azure Batch 帳戶](batch-account-create-portal.md)
* [Azure Batch 功能概觀](batch-api-basics.md)
* [Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0914_2016-->