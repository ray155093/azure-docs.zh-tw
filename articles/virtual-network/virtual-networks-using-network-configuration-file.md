---
title: "設定 Azure 虛擬網路 (傳統) - 網路組態檔 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站 (傳統 ) 匯出、變更和匯入網路組態檔，以修改虛擬網路 (傳統)。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 3827dc0958c51fa0c4ecb1a2e8e3b7bbed42a75a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>使用網路組態檔來設定虛擬網路 (傳統)
您可以使用 Azure 入口網站 (傳統)，或使用網路組態檔來設定虛擬網路 (傳統)。 您無法使用網路組態檔並透過 Azure Resource Manager 部署模型，而建立或修改虛擬網路。 您也無法使用 Azure 入口網站來建立或修改虛擬網路 (傳統)。

## <a name="creating-and-modifying-a-network-configuration-file"></a>建立和修改網路設定檔
若要撰寫網路組態檔，最簡單的方式是從現有的虛擬網路 (傳統) 組態匯出網路設定，然後修改檔案，以包含設定虛擬網路所需的設定。

若要編輯網路組態檔，您可以直接開啟該檔案，並進行適當的變更，然後儲存檔案。 您可以使用任何 *xml* 編輯器來變更網路組態檔。 

您應該確實遵循〈 [網路組態檔結構描述設定](https://msdn.microsoft.com/library/azure/jj157100.aspx)〉的指引。 

Azure 會將已部署的子網路視為 **使用中**。 使用中的子網路無法加以修改。 在修改之前，請將已部署到不同子網路的任何內容移到不修改的子網路。   請參閱〈 [將 VM 或角色執行個體移至不同的子網路](virtual-networks-move-vm-role-to-subnet.md)〉。

## <a name="export-and-import-virtual-network-settings-using-the-azure-portal-classic"></a>使用 Azure 入口網站 (傳統) 匯出和匯入虛擬網路設定
您可以使用 PowerShell 或管理入口網站匯入和匯出網路組態檔中包含的網路組態設定。 下列指示將協助您使用管理入口網站匯出和匯入。 

### <a name="to-export-your-network-settings"></a>若要匯出網路設定
匯出時，訂閱中虛擬網路所有的設定將寫入至 .xml 檔。 

1. 登入 [Azure 入口網站 (傳統)](https://manage.windowsazure.com/)。
2. 在入口網站的 [網路] 頁面底部，按一下 [匯出]。 
3. 在 [ **匯出網路組態** ] 視窗中，確認您已選取要匯出網路設定的訂用帳戶。 然後，按一下右下方的核取記號。 
4. 當提示出現時，將 *NetworkConfig.xml* 檔案儲存到您選擇的位置。

### <a name="to-import-your-network-settings"></a>若要匯入網路設定
1. 在入口網站左下方的導覽窗格中，按一下 [新增]。
2. 按一下 [網路服務]  ->  [虛擬網路]  ->  [匯入組態]。
3. 在 [匯入網路組態檔] 頁面上，瀏覽至您的網路組態檔，然後按一下 [下一步] 箭號。
4. 在 [ **建置您的網路** ] 頁面上，您會看見畫面上的資訊顯示將變更或建立的網路組態區段。 如果變更正確無誤，請按一下核取記號繼續更新或建立虛擬網路。 


