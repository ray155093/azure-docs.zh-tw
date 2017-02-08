---
title: "設定傳統部署模型的 VNet 對 VNet 連線 | Microsoft Docs"
description: "如何使用 PowerShell 和 Azure 傳統入口網站將 Azure 虛擬網路連接在一起。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 7413827f-233d-4c7c-a133-9c99cf031833
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: c1fd18ad8889033cba8a92f6ae255ba6478e6595


---
# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>設定傳統部署模型的 VNet 對 VNet 連接
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [傳統 - 傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

本文將引導您完成使用傳統部署模型 (也稱為「服務管理」) 來建立虛擬網路並將虛擬網路連接在一起的步驟。 下列步驟會使用 Azure 傳統入口網站來建立 Vnet 和閘道，以及使用 PowerShell 來設定 VNet 對 VNet 連線。 您無法在入口網站中設定連線。

![VNet 對 VNet 連線能力圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 對 VNet 連線的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示 VNet 對 VNet 組態目前可用的部署模型和方法。 如果有內含設定步驟的文章可供使用，我們會從此表格直接連結至該文章。

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>關於 VNet 對 VNet 連線
將一個虛擬網路連接到另一個虛擬網路 (VNet 對 VNet) 類似於將虛擬網路連接到內部部署站台位置。 這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。 

您所連線的 Vnet 可位於不同的訂用帳戶和不同的區域。 您可以將 VNet 對 VNet 通訊與多站台組態結合。 這可讓您建立結合了跨單位連線與內部虛擬網路連線的網路拓撲。

### <a name="why-connect-virtual-networks"></a>為什麼要連接虛擬網路？
針對下列原因，您可能希望連接虛擬網路：

* **跨區域的異地備援和異地目前狀態**
  
  * 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
  * 有了 Azure Load Balancer 和 Microsoft 或協力廠商叢集技術，您便可以利用跨多個 Azure 區域的異地備援，設定具有高可用性的工作負載。 其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組來設定 SQL Always On。
* **具有嚴密隔離界限的區域性多層式應用程式**
  
  * 在相同的區域中，您可以設定既有多個 VNet 相連又有嚴密的隔離及安全的層次間通訊的多層式應用程式。
* **Azure 中的跨訂用帳戶、組織間通訊**
  
  * 如果您有多個 Azure 訂用帳戶，您可以將虛擬網路之間不同訂用帳戶的工作負載安全地連接在一起。
  * 針對企業或服務提供者，您可以在 Azure 中使用安全 VPN 技術啟用跨組織通訊。

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>傳統 Vnet 的 VNet 對 VNet 常見問題集
* 虛擬網路可位於相同或不同的訂用帳戶。
* 虛擬網路可位於相同或不同的 Azure 區域 (位置)。
* 即使虛擬網路連接在一起，雲端服務或負載平衡端點也無法跨虛擬網路。
* 將多個虛擬網路連接在一起並不需要任何 VPN 裝置。
* VNet 對 VNet 支援連接 Azure 虛擬網路。 它不支援連接未部署到虛擬網路中的虛擬機器或雲端服務。
* VNet 對 VNet 需要動態路由閘道。 不支援 Azure 靜態路由閘道。
* 虛擬網路連線能力可以與多站台 VPN 同時使用。 一個虛擬網路 VPN 閘道最多可以有 10 條 VPN 通道連接至其他虛擬網路或內部部署站台。
* 虛擬網路與內部部署區域網路網站的位址空間不得重疊。 位址空間重疊會導致建立虛擬網路或上傳 netcfg 組態檔失敗。
* 不支援成對虛擬網路之間的備援通道。
* VNet 的所有 VPN 通道 (包括 P2S VPN) 在 Azure 中皆共用 VPN 閘道的可用頻寬及相同的 VPN 閘道執行時間 SLA。
* VNet 對 VNet 流量會經過 Azure 的骨幹。

## <a name="a-namestep1astep-1---plan-your-ip-address-ranges"></a><a name="step1"></a>步驟 1 - 規劃 IP 位址範圍
決定將用來設定虛擬網路的範圍相當重要。 就此組態而言，您必須確定沒有任何 VNet 範圍彼此重疊，或是與其連接的區域網路重疊。

下表顯示一個範例，說明如何定義 VNet。 範圍僅供作為指導方針。 請記下您虛擬網路的範圍。 稍後的步驟將會需要這項資訊。

**範例設定**

| 虛擬網路 | 位址空間 | 區域 | 連接至區域網路站台 |
|:--- |:--- |:--- |:--- |
| VNet1 |VNet1 (10.1.0.0/16) |美國西部 |VNet2Local (10.2.0.0/16) |
| VNet2 |VNet2 (10.2.0.0/16) |日本東部 |VNet1Local (10.1.0.0/16) |

## <a name="step-2---create-vnet1"></a>步驟 2 - 建立 VNet1
在此步驟中，我們將建立 VNet1。 使用任何範例時，請務必替換成您自己的值。 如果您的 VNet 已經存在，則不需要執行此步驟。 但是，您必須確認 IP 位址範圍沒有與第二個 VNet 的範圍重疊，或與任何其他您想要連接的 VNet 重疊。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。 在本文中，我們會使用傳統入口網站，因為某些必要的組態設定在 Azure 入口網站中尚未提供。
2. 在畫面的左下角，按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]，以開始組態精靈。 在您瀏覽精靈時，請在每一頁新增指定的值。

### <a name="virtual-network-details"></a>虛擬網路詳細資料
在 [虛擬網路詳細資料] 頁面上，輸入下列資訊：

  ![虛擬網路詳細資料](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

* **名稱** - 為虛擬網路命名。 例如：VNet1。
* **位置** – 當您建立虛擬網路時，您會將該位置與 Azure 位置 (區域) 產生關聯。 例如，如果您希望部署到虛擬網路的 VM 實際位於美國西部，請選取該位置。 建立關聯之後，您就無法變更與您的虛擬網路相關聯的位置。

### <a name="dns-servers-and-vpn-connectivity"></a>DNS 伺服器和 VPN 連線能力
在 [DNS Servers and VPN Connectivity] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。

  ![DNS 伺服器和 VPN 連線能力](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

* **DNS 伺服器** ：輸入 DNS 伺服器名稱和 IP 位址，或從下拉式清單中選取先前註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器。 它可讓您指定要用於此虛擬網路之名稱解析的 DNS 服務。 如果您想要在虛擬網路之間進行名稱解析，您必須設定自己的 DNS 伺服器，而不是使用 Azure 所提供的名稱解析。
* 請勿選取任何 P2S 或 S2S 連線能力核取方塊。 按一下右下角的箭頭，即可移到下一個畫面。

### <a name="virtual-network-address-spaces"></a>虛擬網路位址空間
在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。 這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。 

如果您要建立的 VNet 也將連線到您的內部部署網路，選取的範圍不與用於內部部署網路的任何範圍重疊就特別重要。 在該情況下，您必須與您的網路系統管理員協調。 您的網路系統管理員可能需要從內部部署網路位址空間中切割出一個 IP 位址範圍，以供您用於 VNet。

  ![虛擬網路位址空間頁面](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

* **位址空間** - 包括起始 IP 和位址計數。 請確認您指定的位址空間沒有與您在內部部署網路上擁有的任何位址空間重疊。 在此範例中，我們將 10.1.0.0/16 用於 VNet1。
* **新增子網路** - 包括起始 IP 和位址計數。 不需要其他子網路，但是您可以為將擁有靜態 DIP 的 VM 建立個別的子網路。 或者，您可以讓您的 VM 位於與其他角色執行個體不同的子網路中。

**按一下頁面右下角的核取記號** ，隨即開始建立虛擬網路。 完成時，您會在 [網路] 頁面上看到 [狀態] 底下列出 [已建立]。

## <a name="step-3---create-vnet2"></a>步驟 3 - 建立 VNet2
接著，請重複先前的步驟來建立另一個 VNet。 在稍後的步驟中，您將連接兩個 VNet。 您可以參考步驟 1 中的 [範例設定](#step1) 。 如果您的 VNet 已經存在，則不需要執行此步驟。 不過，您將必須確認 IP 位址範圍沒有與任何其他您想要連接的 VNet 或內部部署網路重疊。

## <a name="step-4---add-the-local-network-sites"></a>步驟 4 - 新增區域網路站台
當您建立 VNet 對 VNet 組態時，需要設定區域網路站台，這些站台會顯示在入口網站的 [區域網路]  頁面中。 Azure 會使用每個區域網路站台中指定的設定，來決定如何路由傳送 VNet 之間的流量。 您需決定要用來參考每個區域網路站台的名稱。 最好是使用描述性名稱，因為您將在稍後的步驟中，從下拉式清單中選取值。

例如，VNet1 會連接到您所建立名為 "VNet2Local" 的區域網路站台。 VNet2Local 的設定會包含 VNet2 的位址首碼，以及 VNet2 閘道的公用 IP 位址。 VNet2 會連接到您所建立名為 "VNet1Local" 的區域網路站台，此站台包含 VNet1 的位址首碼和 VNet1 閘道的公用 IP 位址。

### <a name="a-namelocalnetaadd-the-local-network-site-vnet1local"></a><a name="localnet"></a>新增區域網路站台 VNet1Local
1. 在畫面的左下角，按一下 [新增] > [網絡服務] > [虛擬網路] > [加入區域網路]。
2. 在 [指定您的區域網路詳細資料] 頁面上，針對 [名稱] 輸入要用來代表您所要連線網路的名稱。 在此範例中，您可以使用 "VNet1Local" 來參照 VNet1 的 IP 位址範圍和閘道。
3. 針對 [VPN 裝置 IP 位址 (選擇性)] ，指定任何有效的公用 IP 位址。 一般而言，您會將實際的外部 IP 位址用於 VPN 裝置。 針對 VNet 對 VNet 組態，您需使用指派給您 VNet 閘道的公用 IP 位址。 但是，由於您尚未建立閘道，因此您可以指定任何有效的公用 IP 位址作為預留位置。 請勿將此欄位留白 - 這不是此組態的選擇性欄位。 在稍後的步驟中，您將在 Azure 產生閘道之後，回到這些設定，並使用對應的閘道 IP 位址來進行設定。 按一下箭號以前進到下一個畫面。
4. 在 [指定位址] 頁面上，輸入 VNet1 的 IP 位址範圍和位址計數。 這必須確實對應到為 VNet1 設定的範圍。 Azure 會使用您指定的 IP 位址範圍來路由傳送要遞送給 VNet1 的流量。 按一下核取記號以建立區域網路。

### <a name="add-the-local-network-site-vnet2local"></a>新增區域網路站台 VNet2Local
您可以使用上述步驟來建立區域網路站台 "VNet2Local"。 如有必要，您可以參考步驟 1 中 [範例設定](#step1) 內的值。

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>將每個 VNet 設定成指向區域網路
每個 VNet 都必須指向要做為流量路由傳送目的地的個別區域網路。 

**對於 Vnet1**

1. 瀏覽至虛擬網路 **VNet1** 的 [設定] 頁面。 
2. 在 [站對站連線能力] 底下，選取 [連接到區域網路]，然後從下拉式清單中選取 [VNet2Local]  作為區域網路。 
3. 儲存您的設定。

**對於 VNet2**

1. 瀏覽至虛擬網路 **VNet2** 的 [設定] 頁面。 
2. 在 [站對站連線能力] 底下，選取 [連接到區域網路]，然後從下拉式清單中選取 [VNet1Local]  作為區域網路。 
3. 儲存您的設定。

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>步驟 5 - 設定每個 VNet 的閘道
設定每個虛擬網路的「動態路由」閘道。 此組態不支援「靜態路由」閘道。 如果您正在使用先前所設定且已經包含「動態路由」閘道的 VNet，則不需要執行此步驟。 如果您的閘道是「靜態路由」閘道，您就必須將它們刪除並重新建立為「動態路由」閘道。 如果您將閘道刪除，系統會把指派給該閘道的公用 IP 位址釋出，您將必須返回，並使用新閘道的新公用 IP 位址來重新設定所有區域網路和 VPN 裝置。

1. 在 [網路] 頁面上，確認您虛擬網路的狀態欄為 [已建立]。
2. 在 [名稱]  欄中，按一下虛擬網路的名稱。 在此範例中，我們使用 "VNet1"。
3. 在 [儀表板]  頁面上，注意此 VNet 尚未設定閘道。 當您進行設定閘道器的步驟時，您會看到此狀態變更。
4. 在頁面的底部，按一下 [建立閘道] 和 [動態路由]。 當系統提示您確認是否要建立閘道時，請按一下 [是]。
   
      ![閘道類型](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  
5. 在建立閘道的期間，請注意頁面上的閘道圖形會變更為黃色，並顯示「正在建立閘道」。 建立閘道通常需要大約 30 分鐘的時間。
6. 為 VNet2 重複相同的步驟。 您不需要第一個 VNet 閘道完成，就可以開始為另一個 VNet 建立閘道。
7. 當閘道狀態變更為「正在連接」時，便可在「儀表板」中看到每個閘道的公用 IP 位址。 記下對應到每個 VNet 的 IP 位址，並小心不要混淆。 這些是您為每個區域網路編輯「VPN 裝置」的預留位置 IP 位址時會使用的 IP 位址。

## <a name="step-6---edit-the-local-network"></a>步驟 6 - 編輯區域網路
1. 在 [區域網路] 頁面上，按一下您想要編輯的 [區域網路名稱] 的名稱，然後按一下頁面底部的 [編輯]。 針對 [VPN 裝置 IP 位址] ，輸入對應於 VNet 之閘道的 IP 位址。 例如，針對 VNet1Local，請輸入指派給 VNet1 的閘道 IP 位址。 按一下頁面底部的箭頭。
2. 在 [指定位址空間]  頁面上，按一下右下角的核取記號，而不進行任何變更。

## <a name="step-7---create-the-vpn-connection"></a>步驟 7 - 建立 VPN 連線
當所有先前的步驟都已完成時，請設定 IPsec/IKE 預先共用金鑰並建立連線。 這組步驟會使用 PowerShell，無法在入口網站中設定。 如需有關如何安裝 Azure PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。 請務必下載最新版的「服務管理」(SM) Cmdlet。 

1. 開啟 Windows PowerShell 並登入。
   
        Add-AzureAccount
2. 選取您 VNet 所在的訂用帳戶。
   
        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"
3. 建立連線。 在這些範例中，請注意，共用金鑰是完全相同的。 共用金鑰必須一律相符。

    VNet1 對 VNet2 連線

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 對 VNet1 連線

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. 等待連線初始化。 將閘道初始化之後，閘道看起來就會像下圖。
   
    ![閘道狀態 - 已連線](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  
   
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>後續步驟
您可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱 [虛擬機器文件](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) 。




<!--HONumber=Dec16_HO1-->


