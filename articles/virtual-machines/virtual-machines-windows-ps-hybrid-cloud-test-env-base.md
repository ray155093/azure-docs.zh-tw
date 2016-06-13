<properties 
	pageTitle="混合式雲端測試環境 | Microsoft Azure" 
	description="了解如何使用簡化的內部部署網路，建立 IT 專業或開發測試的混合式雲端環境。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# 設定用於測試的混合式雲端環境
 
本主題會引導您逐步建立 Microsoft Azure 的混合式雲端環境進行測試。以下是產生的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

這會從您在網際網路上的位置模擬實際的混合式生產環境。其中包括：

-  簡化的內部部署網路 (公司網路子網路)。
-  Azure (TestVNET) 代管的跨單位部署虛擬網路。
-  站對站 VPN 連線。
-  TestVNET 虛擬網路中的第二個網域控制站。

這個組態一般可供您開始：

-  在混合式雲端環境中開發和測試應用程式。
-  建立電腦的測試組態，一些在公司網路子網路上，一些在 TestVNET 虛擬網路內，用於以雲端為基礎的混合式 IT 工作負載。

設定此混合式雲端測試環境分為五個主要階段：

1.	設定公司網路子網路上的電腦。
2.	設定 RRAS1。
3.	建立跨單位 Azure 虛擬網路。
4.	建立站對站 VPN 連線。
5.	設定 DC2。 

如果您還沒有 Azure 訂用帳戶，您可以在[試用 Azure](https://azure.microsoft.com/pricing/free-trial/) 註冊免費帳戶。如果您有 MSDN 或 Visual Studio 訂用帳戶，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

>[AZURE.NOTE] Azure 中的虛擬機器和虛擬網路閘道會在執行時持續耗用成本。Azure VPN 閘道會實作為一組兩個的 Azure 虛擬機器。如需詳細資訊，請參閱[定價 - 虛擬網路](https://azure.microsoft.com/pricing/details/virtual-network/)。為了減少 VPN 閘道的執行成本，請建立測試環境，並盡快執行所需的測試和示範。

此設定要求一個測試子網路，其中最多包含四部使用公用 IP 位址直接連線到網際網路的電腦。如果您沒有這些資源，您也可以[設定用於測試的模擬混合式雲端環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)。模擬的混合式雲端測試環境僅要求 Azure 訂用帳戶。

## 第 1 階段：設定 Corpnet 子網路上的電腦

使用[測試實驗室指南：適用於 Windows Server 2012 R2 的基本組態](http://www.microsoft.com/download/details.aspx?id=39638)的＜設定 Corpnet 子網路的步驟＞一節中的指示，在名為 Corpnet 的子網路上設定 DC1、APP1 和 CLIENT1 電腦。**這個子網路必須與貴公司的網路隔離，因為它會透過 RRAS1 電腦直接連線到網際網路。**

接著，使用 DC1 CORP\\User1 認證登入。若要設定 CORP 網域，讓電腦和使用者使用其本機網域控制站進行驗證，請從系統管理員層級 Windows PowerShell 命令提示字元執行這些命令。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## 第 2 階段：設定 RRAS1

RRAS1 提供公司網路子網路上的電腦與 TestVNET 虛擬網路之間的流量路由和 VPN 裝置服務。RRAS1 必須安裝兩張網路介面卡。

首先，在 RRAS1 上安裝作業系統。

1.	開始安裝 Windows Server 2012 R2。
2.	遵循指示完成安裝，指定本機系統管理員帳戶的強式密碼。使用本機系統管理員帳戶登入。
3.	將 RRAS1 連線到具有網際網路存取的網路，並執行 Windows Update 以安裝 Windows Server 2012 R2 的最新更新。
4.	將一個網路介面卡連接到公司網路子網路，並且將另一個直接連接到網際網路。RRAS1 可以位於網際網路防火牆後方，但是不可位在網路位址轉譯器 (NAT) 後方。

接著，設定 RRAS1 的 TCP/IP 內容。您將需要公開的 IP 位址組態，包括位址、子網路遮罩 (或前置長度)，以及網際網路服務提供者 (ISP) 的預設閘道和 DNS 伺服器。在第 3 階段，需要公用 IP 位址。

在 RRAS1 上的系統管理員層級 Windows PowerShell 命令提示字元下使用下列命令。在執行這些命令之前，先填入變數值並移除 < and > 字元。您可以從 **Get-NetAdapter** 命令的顯示，取得目前的網路介面卡名稱。

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

對於最後一個命令，請確認有四個來自 IP 位址 10.0.0.1 的回應。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## 第 3 階段：建立跨單位 Azure 虛擬網路。

啟動 Azure PowerShell 提示字元。

> [AZURE.NOTE] 下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

登入您的帳戶。

	Login-AzureRMAccount

使用下列命令取得您的訂用帳戶名稱。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂用帳戶使用您用來建置基本組態的同一個訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

接下來，為混合式測試環境建立新的資源群組。

若要判斷唯一的資源群組名稱，請使用此命令列出現有的資源群組。

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

使用下列命令建立新的資源群組。

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

以資源管理員為基礎的虛擬機器需要以資源管理員為基礎的儲存體帳戶。您必須為每個儲存體帳戶挑選只包含小寫字母和數字的全域唯一名稱。您可以使用此命令列出現有的儲存體帳戶。

	Get-AzureRMStorageAccount | Sort Name | Select Name

使用這些命令建立新的儲存體帳戶。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

接下來，您可以建立 Azure 虛擬網路，用它來架設混合式雲端環境，並且使用網路安全性群組進行保護。

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

接下來，使用這些命令來建立站對站 VPN 連接的閘道器。您需要第 2 階段中 RRAS1 之網際網路介面的公用 IP 位址。

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

請留意，完成新的閘道器可能需要 20 分鐘或更久。

接下來，使用下列命令來判斷適用於您虛擬網路之 Azure VPN 閘道的公用 IP 位址。

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

請記下顯示之 **IPAddress** 欄位中的 IP 位址。在第 4 階段中，您需要用到此資訊。

接下來，從您的網路或安全性管理員取得隨機且以強式密碼編譯之 32 個字元的預先共用金鑰。或者，使用 [Create a random string for an IPsec preshared key (建立 IPsec 預先共用金鑰的隨機字串)](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) 中的資訊來取得預先共用金鑰。

使用這些命令，在 Azure 中建立站對站 VPN 連線。

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## 第 4 階段：建立網站間 VPN 連線。

首先，使用路由及遠端存取服務設定 RRAS1，作為公司網路子網路的 VPN 裝置。以本機系統管理員身分登入RRAS1，並在 Windows PowerShell 命令提示字元下執行這些命令。

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

接著，設定 RRAS1 接收來自 Azure VPN 閘道的站對站 VPN 連線。重新啟動 RRAS1，然後以本機系統管理員身分登入RRAS1，並在 Windows PowerShell 命令提示字元下執行這些命令。您需要提供第 3 階段的 Azure VPN 閘道的公用 IP 位址和預先共用金鑰值。

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

允許有幾分鐘的時間，來建立 RRAS1 與 Azure VPN 閘道之間的連線。

接著，設定 RRAS1 支援網際網路位置的轉譯流量。在 RRAS1：

1.	從 [開始] 畫面，輸入 **rras**，然後按一下 [路由及遠端存取]。 
2.	在主控台樹狀結構中，開啟伺服器名稱，然後按一下 [IPv4]。
3.	以滑鼠右鍵按一下 [一般]，然後按一下 [新增路由通訊協定]。
4.	按一下 [NAT]，然後按一下 [確定]。
5.	在主控台樹狀結構中，以滑鼠右鍵按一下 [NAT]、依序按一下 [新增介面] 和 [Corpnet]，然後按一下 [確定] 兩次。
6.	以滑鼠右鍵按一下 [NAT]，然後依序按一下 [新增介面]、[網際網路] 及 [確定]。
7.	在 [NAT] 索引標籤上，按一下 [連線到網際網路的公用介面]、選取 [在此介面上啟用 NAT]，然後按一下 [確定]。

接著，設定 DC1、APP1 和 CLIENT1 使用 RRAS1 作為其預設閘道。
 
在 DC1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

如果介面的名稱不是乙太網路，請使用 **Get-NetAdapter** 命令來決定介面名稱。

在 APP1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

在 CLIENT1 上，於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	ipconfig /renew

這是您目前的組態。
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## 第 5 階段：設定 DC2。

首先，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 DC2 的 Azure 虛擬機器。

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，使用 Azure 入口網站，利用本機系統管理員帳戶的認證連線到新的 DC2 虛擬機器。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DC2 的系統管理員層級 Windows PowerShell 命令提示字元下，執行：

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.1 的 4 次成功回覆。如果您使用的是「模擬混合式雲端組態」，您應該會看到來自 IP 位址 10.0.0.4 的四個成功回覆。這是站對站 VPN 或 VNET 對 VNET 連線的流量測試。

接著，將額外的資料磁碟新增為磁碟機代號 F: 的新磁碟區。

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [磁碟] 群組中，按一下 [磁碟 2] ([磁碟分割] 設為 [不明])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 **[確定]**。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接著，將 DC2 設定為 corp.contoso.com 網域的複本網域控制站。從 DC2 的 Windows PowerShell 命令提示字元執行下列命令。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

請注意，系統將提示您提供 CORP\\User1 密碼和目錄服務還原模式 (DSRM) 密碼，並重新啟動 DC2。

由於 TestVNET 虛擬網路有自己的 DNS 伺服器 (DC2)，因此您必須設定 TestVNET 的虛擬網路使用這個 DNS 伺服器。

1.	在「Azure 入口網站」的左窗格中，按一下虛擬網路圖示，然後按一下 [TestVNET]。
2.	在 [設定] 索引標籤上，按一下 [DNS 伺服器]。
3.	在 [主要 DNS 伺服器] 中，輸入 **192.168.0.4** 以取代 10.0.0.4。
4.	按一下 [儲存]。

接下來，在 Azure PowerShell 命令提示字元下，使用下列命令重新啟動 DC2，以使用新的 DNS 伺服器組態。

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

混合式雲端環境到此準備就緒，可以進行測試。
 
## 後續步驟

- 在這個環境中設定 [SharePoint 內部網路伺服器陣列](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)、[Web 式 LOB 應用程式](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)或 [Office 365 目錄同步作業 (DirSync) 伺服器](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md)。

<!---HONumber=AcomDC_0601_2016-->