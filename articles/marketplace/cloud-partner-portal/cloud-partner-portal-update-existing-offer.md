---
title: "更新 Azure Marketplace 的現有供應項目 | Microsoft Docs"
description: "本文件提供有關透過 Cloud Partner 入口網站更新現有供應項目的詳細資料"
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
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 48424cb03c7fa521a2556ff00c2136eb5fae38ad
ms.lasthandoff: 04/12/2017


---
# <a name="update-an-existing-offer-for-azure-marketplace"></a>更新 Azure Marketplace 的現有供應項目
您的供應項目上線後，您可以對它進行各種的更新。

1. 將新的 VM 映像版本新增至現有的 SKU。
1. 變更可取得 SKU 的區域。 
1. 新增 SKU。 
1. 更新供應項目/SKU Marketplace 中繼資料。

若要這麼做，您必須在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中更新您的供應項目並重新發佈。 本文會引導您完成更新 VM 供應項目的各種層面。

## <a name="unpermitted-changes-to-vm-offersku"></a>不允許對 VM 供應項目/SKU 進行的變更
供應項目在 Azure Marketplace 中上線後，便無法修改 VM 供應項目/SKU 的某些屬性。
1. 供應項目的供應項目識別碼和發行者識別碼。
2. 現有 SKU 的 SKU 識別碼。
3. 現有 SKU 的開放連接埠組態。
4. 現有 SKU 的資料磁碟計數。
5. 現有 SKU 的計費/授權模式變更。

## <a name="updating-the-vm-image-version-for-a-sku"></a>更新 SKU 的 VM 映像版本
可能已使用其他功能、安全性修補程式等更新供應項目之 SKU 的 VM 映像。在這類情況下，您可以更新您的 SKU 所參考的 VM 映像。 若要這麼做，請遵循下列步驟：
1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)
2. 在 [所有供應項目] 之下，尋找您想要更新的供應項目
3. 在 [SKU] 表單之下，按一下您要更新其 VM 映像的 SKU
4. 在 [磁碟版本] 之下，按一下 [+新增磁碟版本] 以新增 VM 映像
5. 提供新的 VM 映像**磁碟版本**。 磁碟版本必須遵循[語意版本](http://semver.org/)格式。 版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。 請確定您提供的新版本大於先前的版本，否則在重新發佈時不會顯示在 Azure 入口網站或 Azure Marketplace 中。
6. 針對 [OS VHD URL] ，輸入為作業系統 VHD 建立的[共用存取簽章 URI](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images)。 請注意，無法變更不同 SKU 版本之間的資料磁碟計數。 如果先前的版本已設定資料磁碟，這個新版本也必須設定資料磁碟。 
7. 按一下 [發佈] 開始進行發佈工作流程，以便讓新的 VM 版本在 Azure Marketplace 和 Azure 入口網站上線。 

## <a name="changing-regions-a-sku-is-available-in"></a>變更可取得 SKU 的區域
經過一段時間，您可能想要在更多區域提供您的供應項目/SKU。 或者，您可能想要在特定區域停止支援供應項目/SKU。 若要這麼做，請遵循下列步驟。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)
2. 在 [所有供應項目] 之下，尋找您想要更新的供應項目
3. 在 [SKU] 表單之下，按一下您要更新其區域可用性的 SKU
4. 按一下 [國家/區域可用性] 欄位之下的 [選取國家 (地區)] 按鈕。 
5. 在區域可用性快顯視窗中，新增/移除您想要提供此 SKU 的區域。
6. 按一下 [發佈] 開始進行發佈工作流程，以更新您的 Sku 區域可用性。 

如果在新的區域中提供 SKU，您將可透過 [匯出價格資料] 功能來指定該特定地區的價格。 請注意，如果您加回之前曾經可用的區域，您將無法更新其價格，因為不允許變更價格。 

## <a name="adding-a-new-sku"></a>新增 SKU
您可以選擇讓新的 SKU 可用於現有的供應項目。 若要這麼做，請遵循下列步驟。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)
2. 在 [所有供應項目] 之下，尋找您想要更新的供應項目
3. 在 [SKU] 表單之下，按一下 [新增 SKU] 並在快顯視窗中提供 [SKU 識別碼]。 
4. 請遵循[這裡](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md)指定的其餘步驟。
6. 按一下 [發佈] 開始進行發佈工作流程，讓新的 SKU 上線。

## <a name="updating-offer-marketplace-metadata"></a>更新供應項目 Marketplace 中繼資料。
在某些情況下，您可能需要更新與您的供應項目相關聯的 Marketplace 中繼資料，例如更新您的公司標誌等。若要這麼做，請遵循下列步驟。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)
2. 在 [所有供應項目] 之下，尋找您想要更新的供應項目
3. 移至 [Marketplace] 表單並遵循[這裡](../../cloud-partner-portal/cloud-partner-portal-publish-virtual-machine.md)的指示進行任何變更。 
4. 按一下 [發佈] 開始進行發佈工作流程，讓您的變更生效。


