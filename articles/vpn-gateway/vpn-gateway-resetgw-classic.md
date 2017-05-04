---
title: "重設 Azure VPN 閘道以重新建立 IPsec 通道 | Microsoft Docs"
description: "本文將逐步引導您重設「Azure VPN 閘道」以重新建立 IPsec 通道。 本文章適用於傳統，和 Resource Manager 部署模型兩者的 VPN 閘道。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0065a4a73ddd7eb9877359626bac231ec3fdef7c
ms.lasthandoff: 04/27/2017


---
# <a name="reset-a-vpn-gateway"></a>重設 VPN 閘道

如果您遺失一或多個網站間 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。 本文將引導您重設您的 Azure VPN 閘道。 

每個 Azure VPN 閘道都是一個虛擬網路閘道，由兩個在「作用中-待命」組態中執行的 VM 執行個體所組成。 重設閘道器時會重新啟動閘道，然後將跨單位設定重新套用至閘道器。 閘道會保留它永遠擁有的公用 IP 位址。 這表示您不需要利用 Azure VPN 閘道的新公用 IP 位址更新 VPN 路由器組態。  

一旦發出此命令，會立即重新啟動 Azure VPN 閘道目前作用中的執行個體。 從作用中執行個體 (正在重新啟動) 容錯移轉到待命執行個體期間，會有短暫的間隔。 此間隔應該不超過一分鐘。

如果連接未在第一次重新啟動之後儲存，請再次發出相同的命令來重新啟動第二個 VM 執行個體 (新的使用中閘道器)。 如果接連要求兩次重新啟動，兩個 VM 執行個體 (作用中和待命) 的重新啟動時間還要更久一點。 這會導致 VPN 連線上較長的間隔，要讓 VM 完成重新啟動，最多需要 2 到 4 分鐘。

在兩次重新啟動之後，如果仍有跨單位連線問題，請從 Azure 入口網站開啟支援要求。

## <a name="before-you-begin"></a>開始之前
在您重設您的閘道器之前，請確認以下所列每個 IPsec 網站間 (S2S) VPN 通道的重要項目。 任何項目的不相符都會導致 S2S VPN 通道中斷連線。 驗證並更正內部部署和 Azure VPN 閘道的組態可為您避免不必要的重新開機，也避免閘道器上的其他作用中連線中斷。

重設您的閘道器之前請確認下列項目：

* Azure VPN 閘道與在內部部署 VPN 閘道的網際網路 IP 位址 (VIP) 已利用 Azure 和內部部署 VPN 原則正確設定。
* 預先共用的金鑰在 Azure 和內部部署 VPN 閘道上必須是相同的。
* 如果您套用特定的 IPsec/IKE 組態，例如加密、雜湊演算法和 PFS (完整轉寄密碼)，確定 Azure 和內部部署 VPN 閘道都具有相同組態。

## <a name="reset-a-vpn-gateway-using-the-azure-portal"></a>使用 Azure 入口網站重設 VPN 閘道

您可以使用 Azure 入口網站重設 Resource Manager VPN 閘道。 如果您想要重設傳統的閘道器，請參閱 [PowerShell](#resetclassic) 步驟。

### <a name="resource-manager-deployment-model"></a>資源管理員部署模型。

1. 開啟 Azure 入口網站，並瀏覽至您想要重設的 Resource Manager 虛擬網路閘道。
2. 在虛擬網路閘道的刀鋒視窗上，按一下 [重設]。

    ![重設 VPN 閘道刀鋒視窗](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)

3. 在 [重設] 刀鋒視窗中，按一下 ![重設 VPN 閘道刀鋒視窗](./media/vpn-gateway-howto-reset-gateway/reset-button.png) 按鈕。


## <a name="reset-a-vpn-gateway-using-powershell"></a>使用 PowerShell 重設 VPN 閘道

### <a name="resource-manager-deployment-model"></a>資源管理員部署模型。

您將需要最新版的 PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 。 用於重設閘道器的 PowerShell Resource Manager Cmdlet 是 `Reset-AzureRmVirtualNetworkGateway`。 以下範例會重設資源群組 "TestRG1" 中的 Azure VPN 閘道 "VNet1GW"。

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

### <a name="resetclassic"></a>傳統部署模型

您將需要最新版的 PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 。 用於重設 Azure VPN 閘道的 PowerShell Cmdlet 是 **Reset-AzureVNetGateway**。 下列範例會重設稱為 "ContosoVNet" 的虛擬網路的 Azure VPN 閘道。

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
``` 

結果︰

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK



