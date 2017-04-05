---
title: "使用點對站將電腦連接至 Azure 虛擬網路︰PowerShell | Microsoft Docs"
description: "藉由建立點對站 VPN 閘道連線，將電腦安全地連接到您的 Azure 虛擬網路。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8cbf4e8ec2b8904d16c6a74b40cbf6d2ec6a1330
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>使用 PowerShell 設定 VNet 的點對站連線
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

點對站 (P2S) 設定可讓您建立從個別的用戶端電腦到虛擬網路的安全連線。 P2S 是透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。 當您想要從遠端位置 (例如從住家或會議) 連線至 VNet 時，或只有幾個需要連線至虛擬網路的用戶端時，點對站連線是很實用的解決方案。 P2S 連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。 您可從用戶端電腦建立 VPN 連線。

本文逐步引導您使用 PowerShell 在 Resource Manager 部署模型中建立具有點對站連線的 VNet。 如需有關點對站連線的詳細資訊，請參閱本文結尾的[點對站常見問題集](#faq)。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 連線的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示 P2S 組態的兩種部署模型和可用的部署方法。 當包含設定的文章推出時，我們會直接從此資料表連結至該文章。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本工作流程
![將電腦連接至 Azure VNet - 點對站連線圖表](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

在此案例中，您將使用點對站連線建立虛擬網路。 這些指示也會協助您產生此組態所需的憑證。 P2S 連線由下列項目組成：具有 VPN 閘道的 VNet、根憑證 .cer 檔案 (公用金鑰)、用戶端憑證，以及用戶端上的 VPN 組態。 

我們對此組態使用下列值。 我們會在本文的區段 [1](#declare) 中設定變數。 您可以使用這些步驟做為逐步解說並使用未經變更的值，或變更這些值以反映您的環境。 

### <a name="example"></a>範例值
* **名稱：VNet1**
* **位址空間：192.168.0.0/16** 和 **10.254.0.0/16**<br>在此範例中，我們使用一個以上的位址空間來說明此組態將使用多個位址空間。 不過，此組態不需要多個位址空間。
* **子網路名稱：FrontEnd**
  * **子網路位址範圍：192.168.1.0/24**
* **子網路名稱：BackEnd**
  * **子網路位址範圍：10.254.1.0/24**
* **子網路名稱：GatewaySubnet**<br>子網路名稱 *GatewaySubnet* 是 VPN 閘道能夠運作的必要項目。
  * **閘道子網路位址範圍：192.168.200.0/24** 
* **VPN 用戶端位址集區：172.16.201.0/24**<br>使用這個點對站連線來連線到 VNet 的 VPN 用戶端，會收到來自 VPN 用戶端位址集區的 IP 位址。
* **訂用帳戶：**如果您有一個以上的訂用帳戶，請確認您使用正確的訂用帳戶。
* **資源群組：TestRG**
* **位置：美國東部**
* **DNS 伺服器：**您想要用於名稱解析的 DNS 伺服器的 IP 位址。
* **GW 名稱：Vnet1GW**
* **公用 IP 名稱：VNet1GWPIP**
* **VpnType：RouteBased**

## <a name="before-beginning"></a>開始之前
* 請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。
* 安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。 使用 PowerShell 進行這項設定時，請確定您是以系統管理員身分執行。 

## <a name="declare"></a>第 1 部分 - 登入和設定變數
在此區段中，您可以登入並宣告用於此組態的值。 在範例指令碼中，會使用宣告的值。 請變更相關值以反映自己的環境。 或者，可以使用宣告的值並執行各步驟做為練習。

1. 在 PowerShell 主控台中，登入您的 Azure 帳戶。 此 Cmdlet 會提示您提供 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。
   
        Login-AzureRmAccount 
2. 取得您的 Azure 訂用帳戶清單。
   
        Get-AzureRmSubscription
3. 指定您要使用的訂用帳戶。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. 宣告您想要使用的變數。 使用以下範例，在需要時將該值替換為您自己的值。
   
        $VNetName  = "VNet1"
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
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="ConfigureVNet"></a>第 2 部分 - 設定 VNet
1. 建立資源群組。
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. 為虛擬網路建立子網路組態，將其命名為 FrontEndBackEnd 和 GatewaySubnet。 這些前置詞必須是您宣告的 VNet 位址空間的一部分。
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. 建立虛擬網路 指定的 DNS 伺服器應該是可以解析您所連接的資源名稱的 DNS 伺服器。 此範例中，我們使用了公用 IP 位址。 請務必使用您自己的值。
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. 指定您所建立的虛擬網路的變數。
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. 要求動態指派的公用 IP 位址。 閘道需要此 IP 位址才能正常運作。 您稍後會將閘道連接到閘道 IP 組態。
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip


## <a name="Certificates"></a>第 3 部分 - 憑證
憑證是 Azure 用於點對站 VPN 的 VPN 用戶端驗證。 建立根憑證之後，您要將公開憑證資料 (不是私密金鑰)，匯出為 Base-64 編碼的 X.509.cer 檔案。 接著將來自根憑證的公開憑證資料上傳至 Azure。

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 用戶端憑證是從根憑證產生，並安裝在每部用戶端電腦上。 如果未安裝有效的用戶端憑證，且用戶端嘗試連線至 VNet，驗證將會失敗。

### <a name="cer"></a>步驟 1 - 取得根憑證的 .cer 檔案

####<a name="enterprise-certificate"></a>企業憑證
 
如果您是使用企業解決方案，則可以使用現有的憑證鏈結。 取得您想要使用的根憑證 .cer 檔案。

####<a name="self-signed-root-certificate"></a>自我簽署根憑證

如果您未使用企業憑證解決方案，則必須建立自我簽署的根憑證。 若要建立包含 P2S 驗證的必要欄位之自我簽署根憑證，您可以使用 PowerShell。 [使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)將逐步引導您完成建立自我簽署根憑證的步驟。

> [!NOTE]
> 先前，makecert 是建立自我簽署根憑證，並產生用於點對站連線之用戶端憑證的建議方法。 您現在可以使用 PowerShell 來建立這些憑證。 使用 PowerShell 的一個優點是能夠建立 SHA-2 憑證。 請參閱[使用 PowerShell 建立點對站連線的自我簽署根憑證](vpn-gateway-certificates-point-to-site.md)的必要值。
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>若要匯出自我簽署根憑證的公開金鑰

點對站連線需要公開金鑰 (.cer) 上傳至 Azure。 下列步驟可協助您匯出自我簽署根憑證的 .cer 檔案。

1. 若要取得憑證的 .cer 檔案，請開啟 [certmgr.msc] 。 找出自我簽署的根憑證，通常位於 '[憑證 - 目前的使用者]\[個人]\[憑證]' 中，然後按一下滑鼠右鍵。 按一下 [所有工作]，然後按一下 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按 [下一步]。 選取 [否，不要匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼 X.509 (.CER)]，然後按 [下一步]。 
4. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
5. 按一下 [完成]  以匯出憑證。 您將會看到 [匯出成功]。 按一下 [確定] 以關閉精靈。

### <a name="generate"></a>步驟 2 - 產生用戶端憑證
您可以為每個會進行連線的用戶端產生唯一的憑證，您也可以在多個用戶端上使用相同的憑證。 產生唯一的用戶端憑證的優點是能夠視需要撤銷單一憑證。 否則，如果每個人都使用相同的用戶端憑證，而您發現需要撤銷某一個用戶端的憑證時，則必須為所有使用該憑證進行驗證的用戶端產生並安裝新的憑證。

####<a name="enterprise-certificate"></a>企業憑證
- 如果您使用企業憑證解決方案，請以一般的名稱值格式 'name@yourdomain.com' 產生用戶端憑證，而不要使用 'domain name\username' 格式。
- 請確定您簽發的用戶端憑證所根據的憑證範本，是以「用戶端驗證」(而不是「智慧卡登入」等) 作為使用清單中第一個項目的「使用者」憑證範本。您可以按兩下用戶端憑證，然後檢視 [詳細資料] > [增強金鑰使用方法]，來檢查憑證。

####<a name="self-signed-root-certificate"></a>自我簽署根憑證 
如果您使用自我簽署根憑證，請參閱[使用 PowerShell 建立點對站連線的自我簽署憑證](vpn-gateway-certificates-point-to-site.md#clientcert)，以了解產生與點對站連線相容之用戶端憑證的步驟。


### <a name="exportclientcert"></a>步驟 3 - 匯出用戶端憑證

如果您使用 [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) 指示從自我簽署根憑證產生用戶端憑證，它會自動安裝在您用來產生它的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您需要將它匯出。
 
1. 若要匯出用戶端憑證，請開啟 **certmgr.msc**。 以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按一下 [下一步]，接著選取 [是，匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，保留選取預設值。 務必選取 [如果可能的話，包含憑證路徑中的所有憑證]。 然後按 [下一步] 。
4. 在 [安全性]  頁面上，您必須保護私密金鑰。 如果您選取要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。 然後按 [下一步] 。
5. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
6. 按一下 [完成]  以匯出憑證。

### <a name="upload"></a>步驟 4 - 上傳根憑證 .cer 檔案

建立閘道之後，您可以將信任的根憑證的 .cer 檔案上傳至 Azure。 您最多可上傳 20 個根憑證。 您並未將根憑證的私密金鑰上傳至 Azure。 一旦上傳 .cer 檔案，Azure 會使用它來驗證連接至虛擬網路的用戶端。

宣告您的憑證名稱的變數，以自己的值取代：

        $P2SRootCertName = "Mycertificatename.cer"

將檔案路徑以您的路徑取代，然後執行 Cmdlet。

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>第 4 部分 – 建立 VPN 閘道
設定和建立 VNet 的虛擬網路閘道。 -GatewayType 必須是 **Vpn**，而且 -VpnType 必須是 **RouteBased**。 這項作業可能需要 45 分鐘的時間才能完成。

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>第 5 部分 - 下載 VPN 用戶端組態封裝
使用 P2S 連接到 Azure 的用戶端必須安裝用戶端憑證和 VPN 用戶端組態封裝。 VPN 用戶端組態封裝可供 Windows 用戶端使用。

VPN 用戶端套件包含的組態資訊可設定 Windows 內建的 VPN 用戶端軟體。 此套件不會安裝其他軟體。 這些是您所要連接之虛擬網路的專屬設定。 如需支援的用戶端作業系統清單，請參閱本文結尾的[點對站連線常見問題集](#faq)。

1. 建立閘道之後，您可以下載用戶端組態封裝。 這個範例會下載 64 位元用戶端的封裝。 如果您想要下載 32 位元用戶端，請將 'Amd64' 取代為 'x86'。 您也可以使用 Azure 入口網站來下載 VPN 用戶端。
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. PowerShell Cmdlet 會傳回 URL 連結。 這是傳回的 URL 的外觀範例：
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. 複製並貼上傳回給網頁瀏覽器的連結，以下載封裝。 然後在用戶端電腦上安裝封裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [更多資訊]，然後按一下 [仍要執行] 以安裝封裝。
4. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 您會看到列出的連線。 它會顯示將要連接的虛擬網路名稱，如以下範例所示︰ 
   
    ![VPN 用戶端](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="clientcertificate"></a>第 6 部分 - 安裝匯出的用戶端憑證

如果您想要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，您需要安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 [存放區位置] 保留 [目前使用者]，然後按 [下一步]。
2. 在 [要匯入的檔案]  頁面上，請勿進行任何變更。 按 [下一步] 。
3. 在 [私密金鑰保護] 頁面上，如果您已使用密碼，請輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。
4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。
5. 按一下 [完成] 。 在憑證安裝的 [安全性警告] 上，按一下 [是]。 您可以安心地按一下 [是]，因為您已經產生憑證。 現在已成功匯入憑證。

## <a name="connect"></a>第 7 部分 - 連接到 Azure
1. 若要連接至您的 VNet，在用戶端電腦上瀏覽到 VPN 連線，然後找出所建立的 VPN 連線。 其名稱會與虛擬網路相同。 按一下 [ **連接**]。 可能會出現與使用憑證有關的快顯訊息。 如果出現，按一下 [繼續]  以使用較高的權限。 
2. 在 [連線] 狀態頁面上，按一下 [連線] 以便開始連線。 如果出現 [選取憑證]  畫面，請確認顯示的用戶端憑證是要用來連接的憑證。 如果沒有，請使用下拉箭頭來選取正確的憑證，然後按一下 [確定] 。
   
    ![VPN 用戶端連接至 Azure Stack](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 現在應該已建立您的連接。
   
    ![連線已建立](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

> [!NOTE]
> 如果您使用的是藉由企業 CA 解決方案簽發的憑證，而在驗證時發生問題，請檢查用戶端憑證上的驗證順序。 您可以按兩下用戶端憑證，然後移至 [詳細資料] > [增強金鑰使用方法]，來檢查驗證清單順序。 請確定清單所顯示的第一個項目是「用戶端驗證」。 如果不是，您就必須根據以「用戶端驗證」作為清單中第一個項目的「使用者」範本來簽發用戶端憑證。 
>
>

## <a name="verify"></a>第 8 部分 - 驗證您的連線
1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all*。
2. 檢視結果。 請注意，您接收到的 IP 位址是您在組態中指定的點對站 VPN 用戶端位址集區中的其中一個位址。 結果應該類似下面的內容：
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>新增或移除受信任的根憑證

您可以從 Azure 新增和移除受信任的根憑證。 當您移除受信任的憑證時，從根憑證所產生的用戶端憑證將不再能夠透過點對站連線到 Azure。 若您希望用可以戶端連線，他們需要安裝在 Azure 中受信任的憑證所產生的新用戶端憑證。


### <a name="to-add-a-trusted-root-certificate"></a>若要新增受信任的根憑證
您最多可新增 20 個受信任的根憑證 .cer 檔案至 Azure。 請遵循下列步驟來新增根憑證。

1. 建立並準備要新增至 Azure 的新根憑證。 將公開金鑰匯出成 Base-64 編碼 X.509 (.CER) 並使用文字編輯器開啟。 然後僅複製如下所示的區段。 
   
    如下列範例所示，複製相關值：
   
    ![憑證](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > 複製憑證資料時，請確定您是以連續一行的形式複製文字，而不含歸位字元或換行字元。 您可能必須將文字編輯器中的檢視修改成 [顯示符號] 或 [顯示所有字元]，才能看到歸位字元和換行字元。                                                                                                                                                                            
    >


2. 指定憑證名稱和金鑰資訊做為變數。 如下列範例所示，以您自己的資訊加以取代：
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. 加入新的根憑證。 您一次只能新增一個憑證。
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. 您可使用下列範例確認新憑證已正確新增：
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>移除受信任的根憑證
您可以從 Azure 移除受信任的根憑證。 當您移除受信任的憑證時，從根憑證所產生的用戶端憑證將不再能夠透過點對站連線到 Azure。 若您希望用可以戶端連線，他們需要安裝在 Azure 中受信任的憑證所產生的新用戶端憑證。

1. 宣告變數。
   
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. 移除憑證。
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. 使用下列範例確認已成功移除憑證。 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>撤銷用戶端憑證
您可以撤銷用戶端憑證。 憑證撤銷清單可讓您選擇性地拒絕以個別的用戶端憑證為基礎的點對站連線。 這不同於移除受信任的根憑證。 若您從 Azure 移除受信任的根憑證 .cer，就會撤銷所有由撤銷的根憑證所產生/簽署的用戶端憑證之存取權。 撤銷用戶端憑證，而不是根憑證，可以讓從根憑證產生的憑證繼續用於點對站連線的驗證。

常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

### <a name="to-revoke-a-client-certificate"></a>若要撤銷用戶端憑證

1. 擷取用戶端憑證指紋。 如需詳細資訊，請參閱[做法：擷取憑證的指紋](https://msdn.microsoft.com/library/ms734695.aspx)。
2. 將資訊複製到文字編輯器，並移除所有的空格，讓它是連續字串。 您會將它宣告為變數。
3. 宣告變數。 請確定宣告您在上一個步驟中擷取的指紋。
   
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
4. 將指紋新增到撤銷的憑證清單。 指紋新增時，您會看到「成功」。
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
        -Thumbprint $RevokedThumbprint1
5. 確認指紋已新增到憑證撤銷清單。
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
6. 新增指紋之後，憑證無法再用於連線接。 嘗試使用此憑證進行連線的用戶端會收到訊息，指出憑證不再有效。

### <a name="to-reinstate-a-client-certificate"></a>若要恢復用戶端憑證
您可以從撤銷的用戶端憑證清單中移除指紋來恢復用戶端憑證。

1. 宣告變數。 請確定針對您要恢復的憑證宣告正確指紋。
 
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"

2. 從憑證撤銷清單中移除憑證指紋。
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. 檢查指紋是否已從撤銷的清單中移除。
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
## <a name="faq"></a>點對站常見問題集

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>後續步驟
一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。 若要了解網路與虛擬機器的詳細資訊，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/linux/azure-vm-network-overview.md)。



