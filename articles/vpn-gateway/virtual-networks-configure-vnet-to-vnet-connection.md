<properties
   pageTitle="設定傳統部署模型的 VNet 對 VNet 連線 | Microsoft Azure"
   description="如何使用 PowerShell 和 Azure 傳統入口網站將 Azure 虛擬網路連接在一起。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc"/>


# 設定傳統部署模型的 VNet 對 VNet 連接

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


本文將引導您完成使用傳統部署模型 (也稱為「服務管理」) 來建立虛擬網路並將虛擬網路連接在一起的步驟。下列步驟會使用 Azure 傳統入口網站來建立 Vnet 和閘道，以及使用 PowerShell 來設定 VNet 對 VNet 連線。您無法在入口網站中設定 VNet 對 VNet 連線。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**VNet 對 VNet 的部署模型和工具**

可以在這兩種部署模型中，使用不同的工具，設定 VNet 對 VNet 連線。如需詳細資訊，請參閱以下的資料表。當此組態有新文章、新的部署模型和額外工具可以使用時，我們就會更新此資料表。當文章可用時，我們會直接從資料表連結至該文章。

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## 關於 VNet 對 VNet 連線

將一個虛擬網路連接到另一個虛擬網路 (VNet 對 VNet) 非常類似於將虛擬網路連接到內部部署網站位置。這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。

您所連線的 Vnet 可位於不同的訂用帳戶和不同的區域。您可以將 VNet 對 VNet 通訊與多站台組態結合。這可讓您建立使用內部虛擬網路連線結合跨單位連線的網路拓撲，如下圖所示：

![VNet 對 VNet 連線能力圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/vnet2vnet.png)

### 為什麼要連接虛擬網路？

針對下列原因，您可能希望連接虛擬網路：

- **跨區域的異地備援和異地目前狀態**
	- 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
	- 有了 Azure Load Balancer 和 Microsoft 或協力廠商叢集技術，您便可以利用跨多個 Azure 區域的異地備援，設定具有高可用性的工作負載。其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組來設定 SQL Always On。

- **具有嚴密隔離界限的區域性多層式應用程式**
	- 在相同區域中，您可以使用嚴密的隔離和安全的層次間通訊，設定多層式應用程式與多個虛擬網路連線在一起。

- **在 Azure 中的跨訂用帳戶、組織間通訊**
	- 如果您有多個 Azure 訂用帳戶，可以將虛擬網路之間不同訂用帳戶的工作負載安全地連線在一起。
	- 針對企業或服務提供者，您可以在 Azure 中使用安全 VPN 技術啟用跨組織通訊。

### 傳統 Vnet 的 VNet 對 VNet 常見問題集

- 虛擬網路可位於相同或不同的訂用帳戶。

- 虛擬網路可位於相同或不同的 Azure 區域 (位置)。

- 即使虛擬網路連接在一起，雲端服務或負載平衡端點也無法跨虛擬網路。

- 將多個虛擬網路連接在一起並不需要任何 VPN 裝置，除非需要跨單位連線能力。

- VNet 對 VNet 支援連接 Azure 虛擬網路。它不支援連接未部署到虛擬網路中的虛擬機器或雲端服務。

- VNet 對 VNet 需要動態路由閘道。不支援 Azure 靜態路由閘道。

- 虛擬網路連線能力可以和多站台 VPN 同時使用，一個虛擬網路 VPN 閘道最多可以有 10 個 VPN 通道連接至其他虛擬網路或內部部署網站。

- 虛擬網路與內部部署區域網路網站的位址空間不得重疊。位址空間重疊會導致建立虛擬網路或上傳 netcfg 組態檔失敗。

- 不支援成對虛擬網路之間的備援通道。

- 虛擬網路的所有 VPN 通道 (包括 P2S VPN) 在 Azure VPN 閘道與 Azure 中相同的 VPN 閘道運作時間 SLA 共用可用的頻寬。

- VNet 對 VNet 流量會經過 Azure 的骨幹。


## <a name="step1"></a>步驟 1 - 規劃 IP 位址範圍

決定將用來設定虛擬網路的範圍相當重要。請牢記，就此組態而言，您必須確定沒有任何 VNet 範圍彼此重疊，或是與其連接的區域網路重疊。

下表顯示如何定義 VNet 的範例。僅使用以下的範圍做為指導方針。記下您將用於虛擬網路的範圍。您將需要這項資訊供之後的步驟使用。

**範例設定**

|虛擬網路 |位址空間 |區域 |連接至區域網路站台|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |美國西部 |VNet2Local (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |日本東部 |VNet1Local (10.1.0.0/16) |
  
## 步驟 2 - 建立 VNet1

在此步驟中，我們將建立 VNet1。使用任何範例時，請務必替換成您自己的值。如果您的 VNet 已經存在，則不需要執行此步驟。但是，您將必須確認 IP 位址範圍沒有與第二個 VNet 的範圍重疊，或與任何其他您想要連接的 VNet 重疊。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。請注意，這些步驟不會使用較新的 Azure 入口網站。

2. 在畫面的左下角，按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]，以開始組態精靈。在您瀏覽精靈時，在每一頁中新增下列的指定值。

### 虛擬網路詳細資料

在 [虛擬網路詳細資料] 頁面上，輸入下列資訊。

  ![虛擬網路詳細資料](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **名稱** - 為虛擬網路命名。例如：VNet1。
  - **位置** – 當您建立虛擬網路時，您會將該位置與 Azure 位置 (區域) 產生關聯。例如，如果您希望部署到虛擬網路的 VM 實際位於美國西部，請選取該位置。建立關聯之後，您就無法變更與您的虛擬網路相關聯的位置。

### DNS 伺服器和 VPN 連線能力

在 [DNS Servers and VPN Connectivity] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。

  ![DNS 伺服器和 VPN 連線能力](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)

- **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從下拉式清單中選取先前註冊的 DNS 伺服器。此設定不會建立 DNS 伺服器；它可讓您指定要用於此虛擬網路之名稱解析的 DNS 伺服器。如果您想要在虛擬網路之間進行名稱解析，您必須設定自己的 DNS 伺服器，而不是使用 Azure 所提供的名稱解析。
- 請勿選取任何 P2S 或 S2S 連線能力核取方塊。只要按一下右下角的箭頭，就可以移到下一個畫面。

### 虛擬網路位址空間

在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。

如果您要建立的 VNet 也將連線到您的內部部署網路，選取的範圍不與用於內部部署網路的任何範圍重疊就特別重要。在此情況下，您將需要與網路系統管理員協調，商討如何從內部部署網路位址空間中切割出一段 IP 位址範圍，以供用於虛擬網路。

  ![虛擬網路位址空間頁面](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **位址空間** - 包括起始 IP 和位址計數。請確認您指定的位址空間沒有與您在內部部署網路上擁有的任何位址空間重疊。在此範例中，我們將為 VNet1 使用 10.1.0.0/16。
  - **新增子網路** - 包括起始 IP 和位址計數。不需要其他子網路，但是您可以為將擁有靜態 DIP 的 VM 建立個別的子網路。或者，您可以讓您的 VM 位於與其他角色執行個體不同的子網路中。
 
**按一下頁面右下角的核取記號**，隨即開始建立虛擬網路。完成時，您會在 [網路] 頁面上看到 [狀態] 底下列出 [已建立]。

## 步驟 3 - 建立 VNet2

接下來，請重複上述步驟以建立另一個虛擬網路。您將在稍後連接這兩個虛擬網路。您可以參考步驟 1 中的[範例設定](#step1)。如果您的 VNet 已經存在，則不需要執行此步驟。但是，您將必須確認 IP 位址範圍沒有與任何其他您想要連接的 VNet 或內部部署網路重疊。

## 步驟 4 - 新增區域網路站台

當您建立 VNet 對 VNet 組態時，需要設定區域網路站台，這些站台會顯示在入口網站的 [區域網路] 頁面中。Azure 會使用每個區域網路站台中指定的設定，來決定如何路由傳送 VNet 之間的流量。您需決定要用來參考每個區域網路站台的名稱。最好是使用描述性名稱，因為您將會從下拉式清單中選取值。

例如，在此練習中，VNet1 將連接到您所建立名為 "VNet2Local" 的區域網路站台。VNet2Local 的設定將包含 VNet2 的位址首碼，以及 VNet2 閘道的公用 IP 位址。VNet2 將連接到您所建立名為 "VNet1Local" 的區域網路站台，此站台包含 VNet1 的位址首碼和 VNet1 閘道的公用 IP 位址。

### <a name="localnet"></a>新增區域網路站台 VNet1Local

1. 在畫面的左下角，按一下 [新增] > [網路服務] > [虛擬網路] > [加入區域網路]。

2. 在 [指定您的區域網路詳細資料] 頁面上，針對 [名稱] 輸入要用來代表您想要連線的網路名稱。在此範例中，您可以使用 "VNet1Local" 來參照 VNet1 的 IP 位址範圍和閘道。

3. 針對 [VPN 裝置 IP 位址 (選擇性)]，指定任何有效的公用 IP 位址。一般而言，您會將實際的外部 IP 位址用於 VPN 裝置。針對 VNet 對 VNet 組態，您將會使用指派給您 VNet 閘道的公用 IP 位址。但是，由於您尚未建立閘道，因此請指定任何有效的公用 IP 位址做為預留位置。請勿將此欄位留白 - 這不是此組態的選擇性欄位。接著，您將在 Azure 產生閘道之後，回到這些設定，並使用對應的閘道 IP 位址來進行設定。按一下箭號以前進到下一個畫面。

4. 在 [指定位址頁面] 上，您會為 VNet1 放入實際的 IP 位址範圍和位址計數。這必須確實對應到為 VNet1 設定的範圍。Azure 會使用您指定的 IP 位址範圍來路由傳送要遞送給 VNet1 的流量。按一下核取記號以建立區域網路。

### 新增區域網路站台 VNet2Local

您可以使用上述步驟來建立區域網路站台 "VNet2Local"。如有必要，您可以參考步驟 1 中[範例設定](#step1)內的值。

### 將每個 VNet 設定成指向區域網路

每個 VNet 都必須指向要做為流量路由傳送目的地的個別區域網路。

#### 對於 Vnet1

1. 瀏覽至虛擬網路 **VNet1** 的 [設定] 頁面。
2. 在 [站對站連線能力] 底下，選取 [連接到區域網路]，然後從下拉式清單中選取 [VNet2Local] 做為區域網路。
3. 儲存您的設定。

#### 對於 VNet2

1. 瀏覽至虛擬網路 **VNet2** 的 [設定] 頁面。
2. 在 [站對站連線能力] 底下，選取 [連接到區域網路]，然後從下拉式清單中選取 [VNet1Local] 做為區域網路。
3. 儲存您的設定。

## 步驟 5 - 設定每個 VNet 的閘道

設定每個虛擬網路的「動態路由」閘道。此組態不支援「靜態路由」閘道。如果您正在使用先前所設定且已經包含「動態路由」閘道的 VNet，則不需要執行此步驟。如果您的閘道是「靜態」閘道，您必須將它們刪除並重新建立為「動態路由」閘道。請注意，如果您將閘道刪除，系統將會把指派給該閘道的公用 IP 位址釋出，您將必須返回並以新閘道的新公用 IP 位址重新設定所有區域網路和 VPN 裝置。

1. 在 [網路] 頁面上，確認您的虛擬網路的狀態欄為 [已建立]。

2. 在 [名稱] 欄中，按一下虛擬網路的名稱。在此範例中，我們將使用 VNet1。

3. 在 [儀表板] 頁面上，注意此 VNet 尚未設定閘道。當您進行設定閘道器的步驟時，您會看到此狀態變更。

4. 在頁面的底部，按一下 [建立閘道] 和 [動態路由]。當系統提示您確認是否要建立閘道時，請按一下 [是]。

  	![閘道類型](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. 在建立閘道的期間，請注意頁面上的閘道圖形會變更為黃色，並顯示「正在建立閘道」。建立閘道通常需要大約 30 分鐘的時間。

6. 為 VNet2 重複相同的步驟。您不需要第一個 VNet 閘道完成，就可以開始為另一個 VNet 建立閘道。

7. 當閘道狀態變更為「正在連線」時，將可在 [儀表板] 中看到每個閘道的公用 IP 位址。記下對應到每個 VNet 的 IP 位址，並小心不要混淆。這些是您為每個區域網路編輯「VPN 裝置」的預留位置 IP 位址時將使用的 IP 位址。

## 步驟 6 - 編輯區域網路

1. 在 [區域網路] 頁面上，按一下您想要編輯的 [區域網路名稱] 的名稱，然後按一下頁面底部的 [編輯]。針對 [VPN 裝置 IP 位址]，輸入對應於 VNet 之閘道的 IP 位址。例如，針對 VNet1Local，請輸入指派給 VNet1 的閘道 IP 位址。按一下頁面底部的箭頭。

2. 在 [指定位址空間] 頁面上，按一下右下角的核取記號，而不進行任何變更。

## 步驟 7 - 建立 VPN 連線

所有先前的步驟都完成後，您將設定 IPsec/IKE 預先共用金鑰。我們將使用 PowerShell 來完成此操作。如需有關如何安裝 Azure PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。請務必下載最新版的「服務管理」(SM) Cmdlet。

1. 開啟 Windows PowerShell 並登入。

		Add-AzureAccount

2. 建立連線。在下列範例中，請注意，共用金鑰是完全相同的。共用金鑰必須一律相符。


	VNet1 對 VNet2 連線

		Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

	VNet2 對 VNet1 連線

		Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

3. 等待連線初始化。閘道完成初始化之後，外觀將如下圖所示，且您的虛擬網路將連上線。

	![閘道狀態 - 已連線](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

	[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## 後續步驟

您可以將虛擬機器加入您的虛擬網路。如需詳細資訊，請參閱[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)。



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=AcomDC_0803_2016-->