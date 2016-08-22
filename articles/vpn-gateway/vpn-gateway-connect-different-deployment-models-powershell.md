<properties 
   pageTitle="如何使用 PowerShell 將傳統虛擬網路連線至 Resource Manager 虛擬網路 | Microsoft Azure"
   description="了解如何使用 VPN 閘道和 PowerShell 在傳統 VNet 和 Resource Manager VNet 之間建立 VPN 連線"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="cherylmc" />

# 使用 PowerShell 從不同的部署模型連接虛擬網路

> [AZURE.SELECTOR]
- [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure 目前有兩種管理模型：傳統和 Resource Manager (RM)。如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。較新的 VM 和角色執行個體可能會在 Resource Manager 中建立的 VNet 中執行。本文將逐步引導您將傳統 Vnet 連接到 Resource Manager Vnet，以允許位於不同部署模型中的資源透過閘道連線彼此通訊。

您可以在不同訂用帳戶、不同區域和不同部署模型中的 VNet 之間建立連線。只要設定的閘道是動態或路由式，您也可以連接已連線到內部部署網路的 Vnet。如需 VNet 對 VNet 連線的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 常見問題集](#faq)。[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## 開始之前

下列步驟引導您完成為每個 VNet 設定動態或路由式閘道，以及建立閘道之間的 VPN 連線所需的設定。此組態不支援靜態或路由式閘道。

開始前，請確認下列事項：

 - 已建立兩個 Vnet。
 - Vnet 的位址範圍不會彼此重疊，或與閘道可能連接的任何其他連線範圍重疊。
 - 您已安裝最新的 PowerShell Cmdlet (1.0.2 或更新版本)。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。確定安裝服務管理 (SM) 和 Resource Manager (RM) Cmdlet。

### <a name="exampleref"></a>設定範例

在下列步驟中使用 PowerShell Cmdlet 時，您可以使用設定範例做為參考。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet <br> 位置 = 美國西部 <br> 虛擬網路位址空間 = 10.0.0.0/24 <br> Subnet-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> 區域網路名稱 = RMVNetLocal <br> GatewayType = DynamicRouting

**Resource Manager VNet 設定**

VNet 名稱 = RMVNet <br> 資源群組 = RG1 <br> 虛擬網路 IP 位址空間 = 192.168.1.0/16 <br> Subnet -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> 位置 = 美國東部 <br> 閘道公用 IP 名稱 = gwpip <br> 區域網路閘道 = ClassicVNetLocal <br> 虛擬網路閘道名稱 = RMGateway <br> 閘道 IP 位址組態 = gwipconfig


## <a name="createsmgw"></a>區段 1 - 設定傳統 VNet

### 第 1 部份 - 下載您的網路組態檔

1. 在 PowerShell 主控台中登入您的 Azure 帳戶。此 Cmdlet 會提示您提供 Azure 帳戶的登入認證。登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。您將使用 SM PowerShell Cmdlet 才能完成這個部分的設定。

		Add-AzureAccount

2. 執行下列命令以匯出 Azure 網路組態檔。您可以視需要變更此檔案的位置，以匯出至不同的位置。您將編輯此檔案，然後將它匯入至 Azure。

		Get-AzureVNetConfig -ExportToFile c:\AzureNet\NetworkConfig.xml

3. 開啟您下載的 .xml 檔案加以編輯。如需網路組態檔的範例，請參閱[網路組態結構描述](https://msdn.microsoft.com/library/jj157100.aspx)。
 

### 第 2 部份 - 驗證閘道子網路

在 **VirtualNetworkSites** 元素中，將閘道子網路 (若已建立) 加入至您的 VNet。使用網路組態檔時，閘道子網路必須命名為 "GatewaySubnet"，否則 Azure 無法辨識並將它當作閘道子網路。
	
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### 步驟 3 - 新增區域網路站台

您新增的區域網路站台代表您要連接的 RM VNet。您可能必須將 **LocalNetworkSites** 元素 (如果尚未存在) 加入至檔案。此時在組態中，VPNGatewayAddress 可以是任何有效的公用 IP 位址，因為我們尚未建立 Resource Manager VNet 的閘道。一旦建立閘道，我們會以指派給 RM 閘道的正確公用 IP 位址取代此預留位置 IP 位址。

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.1.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### 第 4 部份 – 建立 VNet 與區域網路站台的關聯

在此區段中，我們會指定您要 VNet 連接的區域網路站台。在此例中，這是您稍早參考的 Resource Manager VNet。確定名稱相符。此步驟不會建立閘道。它會指定閘道將要連接的區域網路。

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### 第 5 部份 - 儲存檔案並上傳

儲存檔案，然後執行下列命令，將它匯入至 Azure。確定您會視需要變更環境的檔案路徑。

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\ClassicVNet.xml

您應會看到類似以下的結果顯示匯入成功。

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### 第 6 部份 - 建立虛擬網路閘道 

您可以使用傳統入口網站或 PowerShell 來建立虛擬網路閘道。目前，不可能在 Azure 入口網站中建立傳統閘道。

#### 在傳統入口網站中建立閘道

1. 在[傳統入口網站](https://manage.windowsazure.com)中，移至 [網路]，然後按一下您要為其建立虛擬網路閘道的傳統 VNet。這會開啟 VNet 的主頁面。
2. 按一下頁面頂端的 [儀表板] 以變更成 [儀表板] 頁面。
3. 按一下 [儀表板] 頁面底部的 [建立閘道]，然後按一下 [動態路由]。
4. 按一下 [是] 即可開始建立閘道。
5. 等待閘道建立。有時可能需要 45 分鐘或更久的時間才能完成。
6. 建立閘道之後，您可以在 [儀表板] 頁面上檢視閘道 IP 位址。這是您的閘道的公用 IP 位址。記下或複製公用 IP 位址。您會在稍後步驟中使用它來建立 Resource Manager VNet 組態的區域網路。
7. 您可以選擇性地使用此 Cmdlet 來擷取閘道設定和檢查閘道狀態︰`Get-AzureVirtualNetworkGateway`

#### 使用 PowerShell 建立閘道

若要使用 PowerShell 建立閘道，請使用下列範例。

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting


## <a name="creatermgw"></a>區段 2 - 設定 RM VNet 閘道

若要建立 ARM VNet 的 VPN 閘道，請遵循下列指示。直到您已擷取傳統 VNet 閘道的公用 IP 位址以後，才可開始下列步驟。

1. 在 PowerShell 主控台中**登入您的 Azure 帳戶**。下列 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。登入之後，便會下載您的帳戶設定，以供 Azure PowerShell 使用。

		Login-AzureRmAccount 

 	如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。

		Get-AzureRmSubscription

	指定您要使用的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **建立區域網路閘道**。在虛擬網路中，區域網路閘道通常是指您的內部部署位置。在此例中，區域網路閘道會參考您的傳統 VNet。賦予它一個可供 Azure 參考的名稱，並且指定位址空間前置詞。Azure 會使用您指定的 IP 位址前置詞來識別要傳送至內部部署位置的流量。如果您稍後需要先在此調整資訊，再建立您的閘道，您可以修改下列值並再次執行範例。<br><br>
	- `-Name` 是您要指派給區域網路閘道的參考名稱。<br>
	- `-AddressPrefix` 是傳統 VNet 的位址空間。<br>
	- `-GatewayIpAddress` 是傳統 VNet 閘道的公用 IP 位址。請務必變更下列範例，以反映正確的 IP 位址。
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **要求公用 IP 位址**以配置給 Azure Resource Manager VNet VPN 閘道。您無法指定想要使用的 IP 位址。該 IP 位址會以動態方式配置給您的閘道。但是，這不代表 IP 位址會變更。Azure VPN 閘道 IP 位址只會在刪除或重新建立閘道時變更。它不會因為重新調整、重設或其他 Azure VPN 閘道內部維護/升級而變更。<br>在此步驟中，我們還會設定稍後步驟所用的變數。


		$ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
		-ResourceGroupName RG1 -Location 'EastUS' `
		-AllocationMethod Dynamic

4. **確認您的虛擬網路有閘道子網路**。如果閘道器子網路不存在，請新增一個。確定閘道子網路命名為 GatewaySubnet。

5. 執行下列命令，以**擷取用於閘道的子網路**。在此步驟中，我們也會設定要用於下一個步驟中的變數。
	- `-Name` 是 Resource Manager VNet 的名稱。
	- `-ResourceGroupName`與 VNet 相關聯的資源群組。此閘道子網路必須已為此 VNet 存在且命名為 GatewaySubnet，才能正常運作。


			$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **建立閘道 IP 位址組態**。閘道器組態定義要使用的子網路和公用 IP 位址。使用下列範例來建立您的閘道組態。<br>在此步驟中，`-SubnetId` 和 `-PublicIpAddressId` 參數必須分別收到來自子網路和 IP 位址物件的 ID 屬性。您無法使用簡單的字串。這些變數設定於要求公用 IP 的步驟以及擷取子網路的步驟中。

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name gwipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id
	
7. 執行下列命令，以**建立 Resource Manager VNet 閘道**。`-VpnType` 必須為 RouteBased。

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. 建立 VPN 閘道後，請**複製公用 IP 位址**。您會在進行傳統 VNet 的區域網路設定時用到它。您可以使用下列 Cmdlet 來擷取公用 IP 位址。公用 IP 位址會在傳回資料中列為 IpAddress。

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## 區段 3︰修改傳統 Vnet 的區域網路

在這個步驟中，您可以匯出網路組態檔、加以編輯，然後儲存檔案並匯回 Azure。或者，可以在傳統入口網站中修改此設定。

### 在入口網站中修改

1. 開啟[傳統入口網站](https://manage.windowsazure.com)。

2. 在傳統入口網站中，在左側向下捲動並按一下 [網路]。在 [網路] 頁面上，按一下頁面頂端的 [區域網路]。

3. 在 [區域網路] 頁面上，按一下以選取您在第 1 部份中設定的區域網路，然後移至頁面底部並按一下 [編輯]。

4. 在 [指定區域網路詳細資料] 頁面上，使用您在上一個區段中為 Resource Manager 閘道建立的公用 IP 位址取代預留位置 IP 位址。按一下箭號移至下一個區段。確認 [位址空間] 正確無誤，然後按一下核取記號以接受變更。

### 使用網路組態檔進行修改

1. 匯出網路組態檔。
2. 在文字編輯器中，修改 VPN 閘道 IP 位址。

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. 儲存您的變更並將編輯過的檔案匯回 Azure。


## <a name="connect"></a>區段 4：在閘道之間建立連線

在閘道之間建立連線需要 PowerShell。您可以需要加入您的 Azure 帳戶，才能使用傳統 PowerShell Cmdlet。若要這麼做，您可使用下列 Cmdlet：
		
	Add-AzureAccount

1. 執行下列命令來**設定共用金鑰**。在此範例中，`-VNetName` 是傳統 VNet 的名稱，而 `-LocalNetworkSiteName` 是在傳統入口網站中設定區域網路時所指定的名稱。`-SharedKey` 是您可以產生和指定的值。您在此指定的值，必須與您在下一個步驟中建立連線時指定的值相同。此範例的傳回資料應該顯示狀態︰成功。

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. 執行下列命令來建立 VPN 連線。

	**設定變數**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**建立連線**<br> 請注意，`-ConnectionType` 是 IPsec，而不是 Vnet2Vnet。
		
		New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. 您可以在任一入口網站，或使用 `Get-AzureRmVirtualNetworkGatewayConnection` Cmdlet 來檢視您的連線。

		Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>VNet 對 VNet 常見問題集

檢視常見問題集詳細資料以取得 VNet 對 VNet 連線的其他資訊。

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016-->