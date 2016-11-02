<properties 
   pageTitle="使用 VPN 閘道與 PowerShell 將虛擬網路連線到多個網站 | Microsoft Azure"
   description="本文將逐步引導您使用傳統部署模型的 VPN 閘道，將多個本機內部部署網站連線至虛擬網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />


# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>將站台對站台連線新增至使用現有 VPN 閘道連線的 VNet

> [AZURE.SELECTOR]
- [Resource Manager - 入口網站](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [傳統 - PowerShell](vpn-gateway-multi-site.md)

這篇文章會引導您使用 PowerShell 將站台對站 (S2S) 連線新增至具有現有連線的 VPN 閘道。 這種連線通常稱為「多站台」組態。 

本文適用於使用傳統部署模型 (也稱為「服務管理」) 所建立的虛擬網路。 這些步驟不適用於 ExpressRoute/站台對站並存連線組態。 如需有關並存連線的資訊，請參閱 [ExpressRoute/S2S 並存連線](../expressroute/expressroute-howto-coexist-classic.md)。

### <a name="deployment-models-and-methods"></a>部署模型和方法

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

當此組態有新文章和額外工具可以使用時，我們就會更新此資料表。 當文章可用時，我們會直接從此資料表連結至該文章。

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>關於連線

您可以將多個內部部署網站連線到單一虛擬網路。 這對建立混合式雲端解決方案特別具有吸引力。 建立多網站連線至 Azure 虛擬網路閘道，與建立其他站對站連線非常類似。 事實上，您可以使用現有的 Azure VPN 閘道，只要該閘道是動態的 (路由式)。

如果已經有連線至虛擬網路的靜態閘道，您可以將閘道類型變更為動態，而不需要為了容納多個網站重建虛擬網路。 在變更路由類型之前，請確定您的內部部署 VPN 閘道支援路由式 VPN 組態。 

![多網站圖表](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>考慮事項

**您無法使用 Azure 傳統入口網站來變更此虛擬網路。**  針對此版本，您必須變更網路組態檔案，而非使用 Azure 傳統入口網站。 如果您在 Azure 傳統入口網站中進行變更，這些變更將會覆寫此虛擬網路的多網站參考設定。 

在完成多網站程序時，您應該會覺得使用網路組態檔很輕鬆。 不過，如果有多人在處理您的網路組態，您必須確定每個人都知道這項限制。 這並不表示您完全無法使用 Azure 傳統入口網站。 除了無法對這個特定虛擬網路進行組態變更，您可以用它來執行所有其他事情。

## <a name="before-you-begin"></a>開始之前

開始設定之前，請確認您具有下列必要條件：

- Azure 訂閱。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- 每個內部部署位置都有相容的 VPN 硬體。 查看 [關於虛擬網路連線的 VPN 裝置](vpn-gateway-about-vpn-devices.md) ，確認您想要使用的裝置是否為已知相容的項目。

- 每個 VPN 裝置都有對外的公用 IPv4 IP 位址。 此 IP 位址不能位於 NAT 後方。 這是必要條件。

- 您必須安裝最新版的 Azure PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 。

- 熟悉如何設定 VPN 硬體的人員。 您無法使用從 Azure 傳統入口網站自動產生的 VPN 指令碼來設定 VPN 裝置。 這表示您必須非常了解如何設定 VPN 裝置，或是與了解的人員一起進行。

- 您想要用於虛擬網路的 IP 位址範圍 (如果尚未建立的話)。 

- 您要連線之每個區域網路網站的 IP 位址範圍。 您必須確定您要連線之每個區域網路網站的 IP 位址範圍沒有重疊。 否則，Azure 傳統入口網站或 REST API 將會拒絕所上傳的組態。 

    例如，如果您有兩個區域網路網站都包含 IP 位址範圍 10.2.3.0/24，而您有一個目的地位址為 10.2.3.3 的封裝，Azure 就會不知道您想要將封裝傳送到哪個網站，因為位址範圍重疊了。 為了防止路由問題，Azure 不允許您上傳具有重疊範圍的組態檔。



## <a name="1.-create-a-site-to-site-vpn"></a>1.建立站對站 VPN

如已有動態路由閘道的站對站 VPN，太棒了！ 您可以繼續 [匯出虛擬網路組態設定](#export)。 如果沒有，請執行下列動作：

### <a name="if-you-already-have-a-site-to-site-virtual-network,-but-it-has-a-static-(policy-based)-routing-gateway:"></a>如果您已經有站對站虛擬網路，但其有靜態 (原則式) 路由閘道：

1. 將您的閘道類型變更為動態路由。 多網站 VPN 需要動態 (亦稱作路由式) 路由閘道。 若要變更閘道類型，您必須先刪除現有的閘道，然後建立新的閘道。 如需指示，請參閱 [如何變更閘道的 VPN 路由類型](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway)。  

2. 設定新的閘道，並建立 VPN 通道。 如需相關指示，請參閱 [在 Azure 傳統入口網站中設定 VPN 閘道](vpn-gateway-configure-vpn-gateway-mp.md)。 首先，將您的閘道類型變更為動態路由。 

### <a name="if-you-don't-have-a-site-to-site-virtual-network:"></a>如果您沒有站對站虛擬網路：

1. 使用下列指示建立站台對站台虛擬網路： [在 Azure 傳統入口網站中建立具有站對站 VPN 連線的虛擬網路](vpn-gateway-site-to-site-create.md)。  

2. 使用下列指示設定動態路由閘道： [設定 VPN 閘道](vpn-gateway-configure-vpn-gateway-mp.md)。 閘道類型務必選取 [動態路由]  。

## <a name="<a-name="export"></a>2.-export-the-network-configuration-file"></a><a name="export"></a>2.匯出網路組態檔 

匯出您的網路組態檔。 您匯出的檔案將會用來設定新的多網站設定。 如果需要如何匯出檔案的相關指示，請參閱文章中 [如何在 Azure 入口網站中使用網路組態檔建立 VNet](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)一節。 

## <a name="3.-open-the-network-configuration-file"></a>3.開啟網路組態檔

開啟您在最後一個步驟下載的網路組態檔。 使用您喜歡的任何 xml 編輯器。 檔案看起來應該像下面這樣：

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4.-add-multiple-site-references"></a>4.新增多個網站參考

當您新增或移除網站參考資訊時，將會對 ConnectionsToLocalNetwork/LocalNetworkSiteRef 進行組態變更。 加入新的本機網站參考資訊會促使 Azure 建立新的通道。 下列範例中的網路組態適用於單一網站連線。 完成變更後請儲存檔案。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5.-import-the-network-configuration-file"></a>5.匯入網路組態檔

匯入網路組態檔。 當您匯入含有變更的這個檔案時，就會加入新的通道。 這些通道會使用您稍早建立的動態閘道。 如果需要如何匯入檔案的相關指示，請參閱文章中 [如何在 Azure 入口網站中使用網路組態檔建立 VNet](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)一節。 

## <a name="6.-download-keys"></a>6.下載金鑰

新增您的新通道之後，使用 PowerShell Cmdlet `Get-AzureVNetGatewayKey` ，以取得每個通道的 IPsec/IKE 預先共用金鑰。

例如：

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

如果您想要，也可以使用 *取得虛擬網路閘道共用金鑰* REST API 來取得預先共用金鑰。

## <a name="7.-verify-your-connections"></a>7.確認您的連線

檢查多網站通道狀態。 下載每個通道的金鑰之後，您會想要確認連線。 使用 `Get-AzureVnetConnection` 來取得虛擬網路通道清單，如下列範例所示。 VNet1 是 VNet 的名稱。

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>後續步驟

若要深入了解 VPN 閘道，請參閱 [關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。




<!--HONumber=Oct16_HO2-->


