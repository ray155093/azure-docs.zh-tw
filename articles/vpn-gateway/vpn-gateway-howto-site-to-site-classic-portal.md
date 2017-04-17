---
title: "將內部部署網路連接至 Azure 虛擬網路：站對站 VPN (傳統)：入口網站 | Microsoft Docs"
description: "透過公用網際網路建立從內部部署網路至 Azure 虛擬網路之 IPsec 連線的步驟。 這些步驟可協助您使用入口網站建立跨單位的站對站 VPN 閘道連線。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 81eca4b41b6a0726e5fcf851074bfb7dfca16fb8
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>使用 Azure 入口網站建立站對站連線 (傳統)

網站間 (S2S) VPN 閘道連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。 此類型的連線需要位於內部部署的 VPN 裝置，其具有指派的公用 IP 位址且不是位於 NAT 後方。 網站間連線可以用於跨單位與混合式組態。

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

本文逐步引導您使用傳統部署模型和 Azure 入口網站，建立虛擬網路以及內部部署網路的網站間 VPN 閘道連接。 您也可以為 Resource Manager 部署模型建立這個組態，或如果是傳統部署模型，從下列清單中選取不同選項來建立這個組態︰

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
>
>

#### <a name="additional-configurations"></a>其他組態
如果您想要將 VNet 連接在一起，但不要建立對內部部署位置的連線，請參閱 [設定 VNet 對 VNet 連線](virtual-networks-configure-vnet-to-vnet-connection.md)。 如果您想要網站間連接新增至已經有連接的 VNet，請參閱[將 S2S 連接新增至已有現有 VPN 閘道連接的 VNet](vpn-gateway-multi-site.md)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

在開始設定之前，請確認您具備下列項目：

* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您需要與能夠提供那些詳細資料的人協調。
* 您的 VPN 裝置對外開放的公用 IPv4 IP 位址。 此 IP 位址不能位於 NAT 後方。
* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](http://azure.microsoft.com/pricing/free-trial)。
* 目前需要 PowerShell，才能指定共用金鑰及建立 VPN 閘道連線。 安裝最新版的 Azure 服務管理 (SM) PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 使用 PowerShell 進行這項設定時，請確定您是以系統管理員身分執行。 

> [!NOTE]
> 設定站對站連線時，您的 VPN 裝置需要公開的 IPv4 IP 位址。
>

### <a name="values"></a>此練習的範例組態值
練習這些步驟時，您可以使用範例組態值：

* **VNet 名稱︰**TestVNet1
* **位址空間：** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (對此練習是選擇性的)
* **子網路：**
  * FrontEnd：10.11.0.0/24
  * BackEnd：10.12.0.0/24 (對此練習是選擇性的)
* **GatewaySubnet：**10.11.255.0/27
* **資源群組︰**TestRG1
* **位置：**美國東部
* **DNS 伺服器：** 8.8.8.8 (對此練習是選擇性的)
* **本機網站名稱︰** Site2

## <a name="CreatVNet"></a>1.建立虛擬網路

當您建立要用於 S2S 連線的虛擬網路時，您必須確定您指定的位址空間，不會與您要連接之本機網站的任何用戶端位址空間重疊。 如果有重疊的子網路，您的連線便無法正常運作。

* 如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。 

* 如果您還沒有虛擬網路，請建立一個。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路

1. 透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com) ，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [新增] 。 在 [搜尋 Marketplace] 欄位中，輸入「虛擬網路」。 在傳回的清單中找到 [虛擬網路]，並按一下以開啟 [虛擬網路] 刀鋒視窗。

    ![搜尋虛擬網路刀鋒視窗](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. 從接近 [虛擬網路] 刀鋒視窗底部的 [選取部署模型] 清單中，選取 [傳統]，然後按一下 [建立]。

    ![選取部署模型](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. 在 [建立虛擬網路]  刀鋒視窗中進行 VNet 設定。 在此刀鋒視窗中，您會新增您的第一個位址空間和單一子網路位址範圍。 完成 VNet 建立之後，您可以返回並新增其他子網路和位址空間。

    ![建立虛擬網路刀鋒視窗](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "建立虛擬網路刀鋒視窗")
5. 確認 [訂用帳戶]  正確無誤。 您可以使用下拉式清單變更訂用帳戶。
6. 按一下 [資源群組]  並選取現有的資源群組，或輸入新的資源群組名稱以建立新的資源群組。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md#resource-groups)。
7. 接著，選取 VNet 的 [位置]  設定。 此位置會決定您部署到此 VNet 之資源所在的位置。
8. 如果想要能夠在儀表板上輕鬆地尋找您的 VNet，請選取 [釘選到儀表板]，然後按一下 [建立]。

    ![釘選到儀表板](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "釘選到儀表板")
9. 按一下 [建立] 之後，儀表板上會出現一個反映 VNet 進度的圖格。 建立 VNet 時，此圖格會變更。

    ![建立虛擬網路圖格](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "建立虛擬網路")

一旦建立虛擬網路之後，您將在 Azure 傳統入口網站的網路頁面上看見 [狀態] 下列出 [已建立]。

## <a name="additionaladdress"></a>2.新增其他位址空間

建立虛擬網路之後，您可以新增其他位址空間。 新增其他位址空間不是 S2S 組態的必要部分，但如果您需要多個位址空間，請使用下列步驟︰

1. 在入口網站中找出虛擬網路。
2. 在您的虛擬網路刀鋒視窗上，按一下 [設定] 區段下的 [位址空間]。
3. 在 [設定空間] 刀鋒視窗上，按一下 [+新增] 並輸入其他位址空間。
 
## <a name="dns"></a>3.指定 DNS 伺服器
DNS 設定不是 S2S 組態的必要部分，但如果您想要名稱解析，則需要 DNS。

建立虛擬網路之後，您可以新增 DNS 伺服器的 IP 位址，以便處理名稱解析。 開啟您的虛擬網路設定，按一下 DNS 伺服器，並新增您要用於名稱解析的 DNS 伺服器 IP 位址。 此設定不會建立 DNS 伺服器。 在設定範例中，我們會使用公用 DNS 伺服器。 您通常想要使用私人 DNS 伺服器。 務必新增您的資源可以與其通訊的 DNS 伺服器。

1. 在入口網站中找出虛擬網路。
2. 在您的虛擬網路刀鋒視窗上，按一下 [設定] 區段下的 [DNS 伺服器]。
3. 新增 DNS 伺服器。
4. 若要儲存您的設定，按一下頁面頂端的 [儲存]。
 
## <a name="localsite"></a>4.設定本機網站

本機網站通常是指您的內部部署位置。 它包含您要建立連線之 VPN 裝置的 IP 位址，以及將透過 VPN 閘道路由傳送至 VPN 裝置的 IP 位址範圍。

1. 在入口網站中，瀏覽至要建立閘道的虛擬網路。
2. 在您的虛擬網路刀鋒視窗上，於 [概觀] 刀鋒視窗的 [VPN 連線] 區段中，按一下 [閘道] 以開啟 [新增 VPN 連線] 刀鋒視窗。

    ![按一下以設定閘道設定](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "按一下以設定閘道設定")
3. 在 [新增 VPN 連線] 刀鋒視窗上，選取 [站對站]。

    ![按一下站對站](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "站對站")
4. 按一下 [本機網路 - 進行必要設定] 以開啟 [本機網路] 刀鋒視窗。 進行設定，然後按一下 [確定] 以儲存設定。
    - **名稱︰**建立可讓您輕鬆識別您的本機網站的名稱。
    - **VPN 閘道 IP 位址：**這是您內部部署網路之 VPN 裝置的公用 IP 位址。 VPN 裝置需要 IPv4 公用 IP 位址。 為您要連線的 VPN 裝置指定有效的公用 IP 位址。 它不能在 NAT 後方且必須可讓 Azure 連線。
    - **用戶端位址空間︰**列出您要透過此閘道路由傳送至本機內部部署網路的 IP 位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與虛擬網路要連接的其他網路範圍重疊，或與虛擬網路本身的位址範圍重疊。

    ![本機站台](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "設定本機站台")

## <a name="gatewaysubnet"></a>5.設定閘道子網路

您必須為 VPN 閘道建立閘道子網路。 閘道子網路包含 VPN 閘道服務會使用的 IP 位址。

1. 在 [新增 VPN 連線] 刀鋒視窗上，選取 [立即建立閘道] 核取方塊。 將會出現 [選擇性閘道組態] 刀鋒視窗。 如果您未選取此核取方塊，則不會看到用來設定閘道器子網路的刀鋒視窗。

    ![閘道組態 - 子網路、大小、路由類型](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "閘道組態 - 子網路、大小、路由類型")
2. 按一下 [選擇性閘道組態 - 子網路、大小和路由類型] 可開啟 [閘道組態] 刀鋒視窗。
3. 在 [閘道組態] 刀鋒視窗上，按一下 [子網路 - 進行必要設定] 以開啟 [新增子網路] 刀鋒視窗。

    ![閘道組態 - 閘道子網路](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "閘道組態 - 閘道子網路")
4. 在 [新增子網路] 刀鋒視窗上，新增閘道子網路。 新增閘道子網路時，可能的話，最好使用 /28 或 /27 的 CIDR 區塊來建立閘道子網路。 這會確保您有足夠的 IP 位址來因應未來額外的組態需求。  按一下 [確定]  來儲存設定。

    ![新增閘道子網路](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "新增閘道子網路")

## <a name="sku"></a>6.指定 SKU 和 VPN 類型
1. 選取閘道**大小**。 這是您會用來建立虛擬網路閘道的閘道 SKU。 在入口網站中，[預設 SKU] 為 [基本]。 如需關於閘道 SKU 的資訊，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

    ![選取 SKUL 和 VPN 類型](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "選取 SKU 和 VPN 類型")
2. 選取閘道的 [路由類型]。 這也稱為 VPN 類型。 請務必選取正確的閘道類型，因為您無法轉換閘道的類型。 您的 VPN 裝置必須與您所選取的路由類型相容。 如需 VPN 類型的詳細資訊，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 您可能會看到參考 'RouteBased' 和 'PolicyBased' VPN 類型的文章。 'Dynamic' 對應到 'RouteBased'，而 'Static' 對應到 'PolicyBased'。
3. 按一下 [確定]  來儲存設定。
4. 在 [新增 VPN 連線] 刀鋒視窗中，按一下刀鋒視窗底部的 [確定]，來開始建立虛擬網路閘道。 這項作業可能需要 45 分鐘的時間才能完成。

## <a name="vpndevice"></a>7.設定 VPN 裝置

內部部署網路的站對站連線需要 VPN 裝置。 雖然我們不會提供所有 VPN 裝置的組態步驟，但您可能會發現下列連結中的資訊很有幫助︰

- 如需有關相容的 VPN 裝置資訊，請參閱[ VPN 裝置](vpn-gateway-about-vpn-devices.md)。 
- 如需裝置組態設定的連結，請參閱[已經驗證的 VPN 裝置](vpn-gateway-about-vpn-devices.md#devicetable)。 會以最佳方式來提供這些連結。 建議您時常詢問您的裝置製造商以取得最新的組態資訊。
- 如需編輯裝置組態範例的相關資訊，請參閱[編輯範例](vpn-gateway-about-vpn-devices.md#editing)。
- 如需 IPsec/IKE 參數，請參閱[參數](vpn-gateway-about-vpn-devices.md#ipsec)。
- 請在設定 VPN 裝置之前，檢查您要使用的 VPN 裝置是否有任何[已知裝置相容性問題](vpn-gateway-about-vpn-devices.md#known)。

在設定 VPN 裝置時，您需要下列項目：

- 虛擬網路閘道的公用 IP 位址。 移至您虛擬網路的 [概觀] 刀鋒視窗，即可找到此位址。
- 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用非常基本的共用金鑰。 您應該產生更複雜的金鑰來使用。

## <a name="CreateConnection"></a>8.建立連線
在此步驟中，您可以設定共用的金鑰及建立連線。 您設定的金鑰必須是 VPN 裝置組態中使用的相同金鑰。

> [!NOTE]
> 此步驟目前無法不適用於 Azure 入口網站。 您必須使用 Azure PowerShell Cmdlet 的服務管理 (SM) 版本。                                        >
>

### <a name="step-1-connect-to-your-azure-account"></a>步驟 1. 連線至您的 Azure 帳戶

1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

  ```powershell
  Login-AzureRmAccount
  ```
2. 檢查帳戶的訂用帳戶。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 如果您有多個訂用帳戶，請選取您要使用的訂用帳戶。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. 新增 SM 版本的 PowerShell Cmdlet。

  ```powershell
  Add-AzureAccount
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>步驟 2. 設定共用金鑰及建立連線

使用 PowerShell 和傳統部署模型時，入口網站中的資源名稱有時不是 Azure 使用 PowerShell 時預期看見的名稱。 下列步驟將協助您匯出網路組態檔，以取得名稱的確切值。

1. 在您的電腦上建立目錄，然後將網路組態檔匯出到該目錄。 在此範例中，會將網路組態檔匯出到 C:\AzureNet。

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. 使用 xml 編輯器開啟網路組態檔，並檢查 'LocalNetworkSite name' 和 'VirtualNetworkSite name' 的值。 修改範例以反映這些值。 指定包含空格的名稱時，請使用單引號括住值。

3. 設定共用金鑰及建立連線。 '-SharedKey' 是您產生和指定的值。 在範例中，我們使用的是 'abc123'，但是您可以產生且 (應該) 使用更為複雜的值。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
建立連線後，結果是︰**狀態︰成功**。

## <a name="verify"></a>9.確認您的連接

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>後續步驟
一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。


