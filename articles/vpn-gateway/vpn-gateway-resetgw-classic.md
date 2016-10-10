<properties
   pageTitle="重設 Azure VPN 閘道 | Microsoft Azure"
   description="本文將引導您重設您的 Azure VPN 閘道。本文章適用於傳統，和 Resource Manager 部署模型兩者的 VPN 閘道。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# 使用 PowerShell 重設 Azure VPN 閘道


本文逐步引導您使用 PowerShell cmdlet 重設 Azure VPN 閘道。這些指示包含傳統部署模型和 Resource Manager 部署模型兩者。

如果您遺失一或多個 S2S VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。

每個 Azure VPN 閘道都是由在作用中待命組態中執行的兩個 VM 執行個體組成。當您使用 PowerShell Cmdlet 來重設閘道時，它會重新啟動閘道，然後將跨單位組態重新套用至閘道。閘道會保留它永遠擁有的公用 IP 位址。這表示您不需要利用 Azure VPN 閘道的新公用 IP 位址更新 VPN 路由器組態。

一旦發出此命令，會立即重新啟動 Azure VPN 閘道目前作用中的執行個體。從作用中執行個體 (正在重新啟動) 容錯移轉到待命執行個體期間，會有短暫的間隔。此間隔應該不超過一分鐘。

如果連接未在第一次重新啟動之後儲存，請再次發出相同的命令來重新啟動第二個 VM 執行個體 (新的使用中閘道器)。如果接連要求兩次重新啟動，兩個 VM 執行個體 (作用中和待命) 的重新啟動時間還要更久一點。這會導致 VPN 連線上較長的間隔，要讓 VM 完成重新啟動，最多需要 2 到 4 分鐘。

在兩次重新啟動之後，如果仍有跨單位連線問題，請從 Azure 入口網站開啟支援要求。

## 開始之前

在您重設您的閘道器之前，請確認以下所列每個 IPsec 網站間 (S2S) VPN 通道的重要項目。任何項目的不相符都會導致 S2S VPN 通道中斷連線。驗證並更正內部部署和 Azure VPN 閘道的組態可為您避免不必要的重新開機，也避免閘道器上的其他作用中連線中斷。

重設您的閘道器之前請確認下列項目：

- Azure VPN 閘道與在內部部署 VPN 閘道的網際網路 IP 位址 (VIP) 已利用 Azure 和內部部署 VPN 原則正確設定。
- 預先共用的金鑰在 Azure 和內部部署 VPN 閘道上必須是相同的。
- 如果您套用特定的 IPsec/IKE 組態，例如加密、雜湊演算法和 PFS (完整轉寄密碼)，確定 Azure 和內部部署 VPN 閘道都具有相同組態。

## 使用 Resource Management 部署模型重設 VPN 閘道

用於重設閘道器的 PowerShell Resource Manager Cmdlet 是 `Reset-AzureRmVirtualNetworkGateway`。以下範例會重設資源群組 "TestRG1" 中的 Azure VPN 閘道 "VNet1GW"。

	$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
	Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## 使用傳統部署模型重設 VPN 閘道

用於重設 Azure VPN 閘道的 PowerShell Cmdlet 是 `Reset-AzureVNetGateway`。下列範例會重設稱為 "ContosoVNet" 的虛擬網路的 Azure VPN 閘道。
 
	Reset-AzureVNetGateway –VnetName “ContosoVNet” 

結果︰

	Error          :
	HttpStatusCode : OK
	Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	Status         : Successful
	RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
	StatusCode     : OK


## 後續步驟
	
如需詳細資訊請參閱 [PowerShell Service Management Cmdlet 參考](https://msdn.microsoft.com/library/azure/mt617104.aspx)和 [PowerShell Resource Manager Cmdlet 參考](http://go.microsoft.com/fwlink/?LinkId=828732)。

<!---HONumber=AcomDC_0928_2016-->