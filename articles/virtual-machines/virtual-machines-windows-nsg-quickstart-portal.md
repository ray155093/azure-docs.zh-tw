---
title: "使用 Azure 入口網站對 VM 開啟連接埠 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中使用 Resource Manager 部署模型對 Windows VM 開啟連接埠 / 建立端點"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 979260cdb1ce7f3eaa4b7e05be4c34fce06d0399


---
# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>使用 Azure 入口網站對 Azure 中的 VM 開啟連接埠
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
您也可以 [使用 Azure PowerShell 來執行這些步驟](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

首先，建立您的「網路安全性群組」。 在入口網站中選取一個資源群組，按一下 [新增]，然後搜尋並選取 [網路安全性群組]：

![新增網路安全性群組](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

輸入您「網路安全性群組」的名稱，選取或建立資源群組，然後選取位置。 完成後，請按一下 [建立]：

![建立網路安全性群組](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

選取您的新「網路安全性群組」。 選取 [輸入安全性規則]，然後按一下 [新增] 按鈕來建立規則︰

![新增輸入規則](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

提供新規則的名稱。 預設已輸入連接埠 80。 此刀鋒視窗是您將其他規則新增到「網路安全性群組」時，用來變更來源、通訊協定及目的地的地方。 按一下 [確定] 以建立規則：

![建立輸入規則](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

最後一個步驟是將您的「網路安全性群組」與子網路或特定網路介面建立關聯。 讓我們將「網路安全性群組」與子網路建立關聯。 選取 [子網路]，然後按一下 [關聯]:

![將網路安全性群組與子網路建立關聯](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

選取您的虛擬網路，然後選取適當的子網路：

![將網路安全性群組與虛擬網路功能建立關聯](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

您現在已建立「網路安全性群組」、已建立允許連接埠 80 上流量的輸入規則，並且已將它與子網路建立關聯。 透過連接埠 80 可連線到您連接到該子網路的所有 VM。

## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。 「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。 您可以深入了解 [建立網路安全性群組和 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。 深入了解 [使用範本建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送來將唯一的外部連接埠對應至您 VM 上的內部連接埠，請使用負載平衡器和「網路位址轉譯」(NAT) 規則。 例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。 您可以深入了解 [建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。 您可以從下列文章中，找到有關建立更詳細環境的資訊︰

* [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)
* [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
* [負載平衡器的 Azure Resource Manager 概觀](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


