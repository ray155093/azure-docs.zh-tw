---
title: "從 Azure Marketplace 中刪除供應項目 | Microsoft Docs"
description: "本文件詳細說明如何從 Azure Marketplace 中刪除供應項目"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>從 Azure Marketplace 中刪除供應項目/SKU

基於各種原因，您可能決定從 Marketplace 中移除您的供應項目。  「移除供應項目」可確保新的客戶無法再購買或部署您的供應項目，但是對現有客戶沒有任何影響。 「終止供應項目」是終止服務及/或您與現有客戶之間授權合約的程序。  [Microsoft Marketplace 發行者合約](http://go.microsoft.com/fwlink/?LinkID=699560) (請參閱第 7 節) 和[參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (請參閱第 6.2 節) 會管理移除和終止供應項目的相關指引和原則。   本文件討論支援的不同刪除案例，以及您可以對其採取的步驟。

## <a name="delete-a-live-sku-from-azure-marketplace"></a>從 Azure Marketplace 中刪除上線的 SKU

您可以遵循下列步驟以從 Azure Marketplace 中刪除上線的 SKU︰

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  從 [所有供應項目] 索引標籤中選取您的供應項目。

3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。

4.  選取您要刪除的 SKU，然後針對該 SKU 按一下 [刪除] 按鈕。

5.  將供應項目[重新發佈至](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除 SKU。

## <a name="rollback-to-a-previous-sku-version"></a>回復為先前的 SKU 版本

您可以遵循下列步驟以從 Azure Marketplace 中刪除上線的 SKU 的目前版本。 此程序完成時，SKU 將會回復為先前的版本。

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  從 [所有供應項目] 索引標籤中選取您的供應項目。

3.  在畫面的左側窗格中，選取 [SKU] 索引標籤。

4.  從已發佈的磁碟版本清單中刪除最新的 [磁碟版本]。

5.  將供應項目[重新發佈至](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) Azure Marketplace。

供應項目在 Azure Marketplace 上線之後，就會從 Azure Marketplace 和 Azure 入口網站中刪除所列 SKU 的目前版本。 SKU 將會回復為先前的版本。

## <a name="delete-a-live-offer"></a>刪除上線的供應項目

要求移除上線的供應項目時，有幾方面需要注意。 請遵循下列步驟來取得支援小組的指引，以從 Azure Marketplace 中移除上線的供應項目︰

1.  使用這個[連結](https://go.microsoft.com/fwlink/?linkid=844975)提出支援票證

2.  選取 [問題類型] 清單中的 [管理供應項目]，以及 [類別] 清單中的 [修改已在生產中的供應項目和/或 SKU]。

3.  提交要求

支援小組會引導您完成供應項目刪除程序。

請注意，刪除 SKU/供應項目不會影響目前已購買的 SKU/供應項目。 這些項目會繼續如往常般運作。 但無法提供給新客戶進行購買。  
