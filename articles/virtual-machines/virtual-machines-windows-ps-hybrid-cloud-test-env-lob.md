<properties 
	pageTitle="LOB 應用程式測試環境 | Microsoft Azure" 
	description="了解如何在混合式雲端環境中建立 IT 專業或開發測試的 Web 型企業營運應用程式。" 
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

# 在混合式雲端中設定 Web 型 LOB 應用程式進行測試

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

本主題將逐步引導您建立混合式雲端環境測試 Microsoft Azure 代管的 Web 型企業營運 (LOB) 應用程式。以下是產生的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

如需 Azure 代管生產 LOB 應用程式的範例，請參閱 [Microsoft 軟體架構圖表和藍圖](http://msdn.microsoft.com/dn630664)的**企業營運應用程式**架構藍圖。

這個組態會從您在網際網路上的位置模擬 Azure 生產環境中的 LOB 應用程式。其中包括：

- 簡化的內部部署網路 (公司網路子網路)。
- Azure (TestVNET) 代管的跨單位部署虛擬網路。
- 站對站 VPN 連線。
- 企業營運伺服器、SQL 伺服器，以及 TestVNET 虛擬網路中的第二個網域控制站。

這個組態一般可供您開始：

- 針對 Azure 中的 SQL Server 2014 資料庫後端開發和測試網際網路資訊服務 (IIS) 代管的 LOB 應用程式。
- 執行此混合式雲端型 IT 工作負載的測試。

設定此混合式雲端測試環境分為三個主要階段：

1.	設定混合式雲端環境進行測試。
2.	設定 SQL 伺服器電腦 (SQL1)。
3.	設定 LOB 伺服器 (LOB1)。

此工作負載需要 Azure 訂用帳戶。如果您有 MSDN 或 Visual Studio 訂用帳戶，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## 第 1 階段：設定混合式雲端環境

使用[設定用於測試的混合式雲端環境](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md)主題中的指示。由於這個測試環境不需要公司網路子網路上的 APP1 伺服器，因此可以暫時將它關閉。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

> [AZURE.NOTE] 針對第 1 階段，您也可以設定[模擬混合式雲端測試環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)。
 
## 第 2 階段：設定 SQL 伺服器電腦 (SQL1)

從 Azure 入口網站，視需要啟動 DC2 電腦。

然後，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 SQL1 的 Azure 虛擬機器。在執行這些命令之前，先填入變數值並移除 < and > 字元。

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

使用 Azure 入口網站，利用本機系統管理員帳戶連線到 SQL1。

1.	在 Azure 管理入口網站的左窗格中，按一下 [虛擬機器]，然後按一下 SQL1 [狀態] 欄中的 [執行]。
2.	在工作列上，按一下 [連接]。 
3.	提示開啟 SQL1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用這些：
	- 名稱：**SQL1\**[本機系統管理員帳戶名稱]
	- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，設定 Windows 防火牆規則，允許基本連線測試和 SQL Server 的流量。從 SQL1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 192.168.0.4 的四次成功回覆。

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

在 SQL1 的 Windows PowerShell 命令提示字元下執行下列命令：

	md f:\Data
	md f:\Log
	md f:\Backup

接下來，在 Windows PowerShell 提示字元中使用下列命令將 SQL1 加入 CORP Active Directory 網域中。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

系統提示提供 **Add-Computer** 命令的網域帳戶認證時，請使用 CORP\\User1 帳戶。

重新啟動之後，請使用 Azure 入口網站，利用本機系統管理員帳戶連線到 SQL1。

接著，對於新資料庫和使用者帳戶權限設定 SQL Server 2014 使用 F: 磁碟機。

1.	從 [開始] 畫面中，輸入 **SQL Server 管理**，然後按一下 [SQL Server 2014 Management Studio]。
2.	在 [**連線到伺服器**] 中按一下 [**連線**]。
3.	在 [物件總管] 樹狀結構窗格中，以滑鼠右鍵按一下 [SQL1]，然後按一下 [內容]。
4.	在 [伺服器內容] 視窗中，按一下 [資料庫設定]。
5.	找出 [資料庫預設位置] 並設定下列值： 
	- 對於 [資料]，輸入路徑 **f:\\Data**。
	- 對於 [記錄]，輸入路徑 **f:\\Log**。
	- 對於 [備份]，輸入路徑 **f:\\Backup**。
	- 注意：只有新的資料庫才會使用這些位置。
6.	按一下 [確定] 關閉視窗。
7.	在 [物件總管] 樹狀結構窗格中，開啟 [安全性]。
8.	以滑鼠右鍵按一下 [登入]，然後按一下 [新增登入]。
9.	在 [登入名稱] 中，輸入 **CORP\\User1**。
10.	在 [伺服器角色] 頁面上，按一下 [sysadmin]，然後按一下 [確定]。
11.	關閉 Microsoft SQL Server Management Studio。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## 第 3 階段：設定 LOB 伺服器 (LOB1)。

首先，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 LOB1 的 Azure 虛擬機器。

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，使用 Azure 入口網站，利用本機系統管理員帳戶的認證連線到 LOB1。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 LOB1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 192.168.0.4 的四次成功回覆。

接下來，在 Windows PowerShell 提示字元中使用下列命令將 LOB1 加入 CORP Active Directory 網域中。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

系統提示提供 **Add-Computer** 命令的網域帳戶認證時，請使用 CORP\\User1 帳戶。

重新啟動之後，請使用 Azure 入口網站，利用 CORP\\User1 帳戶和密碼連線到 LOB1。

接著，為 IIS 設定 LOB1，並且測試從 CLIENT1 進行的存取。

1.	執行伺服器管理員，然後按一下 [新增角色及功能]。
2.	在 [開始之前] 頁面中按 [下一步]。
3.	在 [選取安裝類型] 頁面上，按 [下一步]。
4.	在 [選取目的地伺服器] 頁面上，按 [下一步]。
5.	在 [伺服器角色] 頁面上，按一下 [角色] 清單中的 [網頁伺服器 (IIS)]。
6.	出現提示時，按一下 [新增功能]，然後按 [下一步]。
7.	在 [選取功能] 頁面上，按 [下一步]。
8.	在 [網頁伺服器 (IIS)] 頁面上，按一下 [下一步]。
9.	在 [選取角色服務] 頁面上，選取或清除測試 LOB 應用程式所需服務的核取方塊，然後按一下 [下一步]。
10.	在 [確認安裝選項] 頁面上，按一下 [安裝]。
11.	等候元件安裝完成，然後按一下 [關閉]。
12.	以 CORP\\User1 帳戶認證登入 CLIENT1 電腦，然後啟動 Internet Explorer。
13.	在網址列中，鍵入 ****http://lob1/**，然後按 ENTER。您應該會看見預設的 IIS 8 網頁。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
這個環境此時即可供您在 LOB1 部署 Web 型應用程式，並且測試公司網路子網路的功能和效能。

## 後續步驟

- 部署[生產環境](virtual-machines-windows-lob-overview.md)。

<!---HONumber=AcomDC_0518_2016-->