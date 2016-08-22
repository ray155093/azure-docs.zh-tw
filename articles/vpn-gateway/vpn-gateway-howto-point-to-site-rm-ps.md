<properties 
   pageTitle="使用 Resource Manager 部署模型設定虛擬網路的點對站 VPN 連線 | Microsoft Azure"
   description="建立點對站 VPN 連線來安全地連線到您的 Azure 虛擬網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc" />

# 使用 PowerShell 設定虛擬網路的點對站連線

> [AZURE.SELECTOR]
- [PowerShell - 資源管理員](vpn-gateway-howto-point-to-site-rm-ps.md)
- [入口網站 - 傳統](vpn-gateway-point-to-site-create.md)

點對站設定可讓您從用戶端電腦個別地建立與虛擬網路的安全連線。VPN 連線的建立方式是從用戶端電腦啟動連線。當您想要從遠端位置 (例如從住家或會議) 連接到您的 VNet 時，或您只有幾個需要連線至虛擬網路的用戶端時，點對站是絕佳的解決方案。

點對站連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。如需點對站連接的詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md#point-to-site-connections)和[關於跨單位連接](vpn-gateway-cross-premises-options.md)。

本文章適用於使用 **Resource Manager 部署模型** (服務管理) 所建立之虛擬網路的點對站 VPN 閘道連線。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**點對站連線的部署模型和工具**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Point-to-Site-diagram](./media/vpn-gateway-point-to-site-create/point2site.png "點對站")


## 關於此組態

在此案例中，您將使用點對站連線建立虛擬網路。點對站連線由下列項目組成：具有閘道的 VNet、上傳的根憑證 .cer 檔案、用戶端憑證，和用戶端上的 VPN 組態。

我們會針對此組態使用下列值：

- 名稱：**TestVNet**，使用位址空間 **192.168.0.0/16** 與 **10.254.0.0/16**。請注意，您可以針對 VNet 使用一個以上的位址空間。
- 子網路名稱：**FrontEnd**，使用 **192.168.1.0/24**。
- 子網路名稱：**BackEnd**，使用 **10.254.1.0/24**。
- 子網路名稱：**GatewaySubnet**，使用 **192.168.200.0/24**。子網路名稱 *GatewaySubnet* 是閘道器能夠運作的必要項目。
- VPN 用戶端位址集區：**172.16.201.0/24**。使用這個點對站連線連線到 VNet 的 VPN 用戶端會接收來自這個集區的 IP 位址。
- 訂用帳戶：如果您有一個以上的訂用帳戶，請確認您使用正確的訂用帳戶。
- 資源群組：**TestRG**
- 位置：**美國東部**
- DNS 伺服器：您想要用於名稱解析的 DNS 伺服器的 **IP 位址**。
- GW 名稱：**GW**
- 公用 IP 名稱：**GWIP**
- VPNType：**RouteBased**


## 開始之前

- 請確認您有 Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，可以啟用您的 [MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
	
- 您必須安裝 Azure 資源管理員 PowerShell Cmdlet (1.0.2 或更新版本)。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 為 Azure 設定點對站連線

1. 在 PowerShell 主控台中，登入您的 Azure 帳戶。此 Cmdlet 會提示您提供 Azure 帳戶的登入認證。登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

		Login-AzureRmAccount 

2. 取得您的 Azure 訂用帳戶清單。

		Get-AzureRmSubscription

3. 指定您要使用的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 在此組態中，以您要使用的值宣告下列 PowerShell 變數宣告的值將用於範例指令碼。宣告您想要使用的值。視需要使用下方範例，將該值替換為您自己的值。

		$VNetName  = "TestVNet"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. 建立新的資源群組。

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. 為虛擬網路建立子網路組態，將其命名為 *FrontEnd*、*BackEnd* 和 *GatewaySubnet*。請注意，這些前置詞必須是上面宣告的 VNet 位址空間的一部分。

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. 建立虛擬網路請注意，指定的 DNS 伺服器應該是可以解析您所連接的資源名稱的 DNS 伺服器。對於此範例，我們使用公用 IP 位址，但是您會想要放入您自己的值。

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. 指定您剛剛建立的虛擬網路的變數。

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. 要求動態指派的公用 IP 位址。閘道需要此 IP 位址才能正常運作。您稍後會將閘道連接到閘道 IP 組態。
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. 將受信任的憑證新增至 Azure。您最多可新增 20 個憑證。如需使用 *makecert* 建立自我簽署根憑證的相關指示，請參閱[使用點對站設定的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)。當您將 Base64 編碼 X.509 (.cer) 檔案新增至 Azure 時，便是告訴 Azure 信任該檔案所代表的根憑證。

	若要取得公開金鑰，將憑證匯出為 Base64 編碼的 X.509 (.CER) 檔案。請記下匯出為 .cer 檔案的檔案路徑。以下是取得您的憑證的 Base64 字串表示法的範例。您將需要對此步驟使用自己的 .cer 檔案路徑。
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64


11. 建立 VNet 的虛擬網路閘道。-GatewayType 必須是 **Vpn**，而且 -VpnType 必須是 **RouteBased**。

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## 用戶端組態

使用點對站連線至 Azure 的每個用戶端必須有兩個要點：必須設定 VPN 用戶端以連接，且用戶端必須安裝用戶端憑證。VPN 用戶端組態封裝可供 Windows 用戶端使用。如需詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md#point-to-site-connections)。

1. 下載 VPN 用戶端組態封裝。在此步驟中，使用下列範例下載用戶端組態封裝。

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	PowerShell Cmdlet 會傳回 URL 連結。複製-貼上傳回網頁瀏覽器的連結以將封裝下載至您的電腦。以下是傳回的 URL 的外觀範例。

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. 產生並安裝從用戶端電腦上的根憑證建立的用戶端憑證 (*.pfx)。您可以使用您習慣的任何安裝方法。如果您使用自我簽署根憑證，但不熟悉如何執行這項操作，您可以參考[使用點對站設定的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)。

3. 若要連接至您的 VNet，在用戶端電腦上瀏覽到 VPN 連線，然後找出剛建立的 VPN 連線。它的名稱將會與虛擬網路相同。按一下 [**連接**]。可能會出現與使用憑證有關的快顯訊息。如果出現，按一下 [繼續] 以使用較高的權限。

4. 在 [連線] 狀態頁面上，按一下 [連接] 以便開始連接。如果出現 [選取憑證] 畫面，請確認顯示的用戶端憑證是要用來連接的憑證。如果沒有，請使用下拉箭頭來選取正確的憑證，然後按一下 [確定]。

5. 現在應該已建立您的連接。您可以使用下列程序來確認您的連接。

## 確認您的連接

1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all*。

2. 檢視結果。請注意，您接收到的 IP 位址是您在組態中指定的點對站 VPN 用戶端位址集區中的其中一個位址。結果應該類似下面的內容：
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## 新增或移除受信任的根憑證

憑證是用於點對站 VPN 的 VPN 用戶端驗證。下列步驟將逐步引導您新增和移除根憑證。當您將 Base64 編碼 X.509 (.cer) 檔案新增至 Azure 時，便是告訴 Azure 信任該檔案所代表的根憑證。

### 新增受信任的根憑證

您最多可新增 20 個受信任的根憑證至 Azure。請遵循下列步驟來新增根憑證。

1. 建立並準備要新增 Azure 的新根憑證。

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. 加入新的根憑證。請注意，您一次只能新增一個憑證。

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. 您可使用下列 Cmdlet 確認新憑證已正確新增。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName

### 移除受信任的根憑證

您可以從 Azure 移除受信任的根憑證。當您移除受信任的憑證後，從根憑證產生的用戶端憑證將無法再經由點對站連線到 Azure，直到它們安裝 Azure 中受信任的憑證所產生的用戶端憑證為止。

1. 若要移除受信任的根憑證，請修改下列範例。

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. 使用下列 Cmdlet 確認已成功移除憑證。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## 管理撤銷的用戶端憑證清單

您可以撤銷用戶端憑證。憑證撤銷清單可讓您選擇性地拒絕以個別的用戶端憑證為基礎的點對站連線。從 Azure 移除根憑證會撤銷由撤銷的根憑證產生/簽署的所有用戶端憑證的存取權，因此撤銷用戶端憑證可讓您移除特定憑證的存取權。常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

### 撤銷用戶端憑證

1. 取得要撤銷的用戶端憑證的指紋。

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. 將指紋新增到撤銷的指紋清單。

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. 確認指紋已新增到憑證撤銷清單。您必須一次新增一個指紋。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### 恢復用戶端憑證

您可以從撤銷的用戶端憑證清單中移除指紋來恢復用戶端憑證。

1.  從撤銷的用戶端憑證指紋清單中移除指紋。

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. 檢查指紋是否已從撤銷的清單中移除。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## 後續步驟

您可以將虛擬機器新增至虛擬網路。請參閱[建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)以取得相關步驟。

<!---HONumber=AcomDC_0810_2016---->