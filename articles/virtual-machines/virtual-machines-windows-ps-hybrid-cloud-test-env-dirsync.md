<properties 
	pageTitle="Office 365 DirSync 測試環境 | Microsoft Azure" 
	description="了解如何在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 伺服器進行 IT 專業或開發測試。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# 在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 進行測試

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。
 
本主題將逐步引導您使用 Microsoft Azure 代管的密碼同步處理建立混合式雲端環境來測試 Office 365 Directory 同步處理 (DirSync)。以下是產生的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
這個組態會從您在網際網路上的位置模擬 Azure 生產環境中的 DirSync 伺服器。其中包括：

- 簡化的內部部署網路 (公司網路子網路)。
- Azure (TestVNET) 代管的跨單位部署虛擬網路。
- 站對站 VPN 連線。
- Office 365 FastTrack 試用訂閱。
- 執行 Azure AD Connect 工具的 DirSync 伺服器以及 TestVNET 虛擬網路中的次要網域控制站。

這個組態一般可供您開始：

- 對於透過密碼同步處理進行內部部署 Active Directory 網域同步處理的 Office 365 開發和測試應用程式。
- 執行此雲端型 IT 工作負載的測試。

設定此混合式雲端測試環境分為三個主要階段：

1.	設定混合式雲端環境進行測試。
2.	設定 Office 365 FastTrack 試用。
3.	設定 DirSync 伺服器 (DS1)。

如果您還沒有 Azure 訂用帳戶，則可以在[試用 Azure](https://azure.microsoft.com/pricing/free-trial/) 註冊免費帳戶。如果您有 MSDN 或 Visual Studio 訂用帳戶，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## 第 1 階段：設定混合式雲端環境

使用[設定用於測試的混合式雲端環境](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md)主題中的指示。由於這個測試環境不需要公司網路子網路上的 APP1 伺服器，因此可以暫時將它關閉。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph1.png)

> [AZURE.NOTE] 針對第 1 階段，您也可以設定[模擬混合式雲端測試環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)。

## 第 2 階段：設定 Office 365 FastTrack 試用

若要開始 Office 365 FastTrack 試用，您需要虛構的公司名稱和 Microsoft 帳戶。雖然非必要，但建議您使用 Contoso (這是 Microsoft 範例內容中使用的虛構公司名稱) 以外公司名稱作為公司名稱。

接著，註冊新的 Microsoft 帳戶。移至 ****http://outlook.com** 並透過類似 user123@outlook.com 的電子郵件地址建立一個帳戶。您將使用這個帳戶註冊 Office 365 FastTrack 試用。

接著，註冊新的 Office 365 企業 E3 試用版。

1.	以 CORP\\User1 帳戶認證登入 CLIENT1。
2.	開啟 Internet Explorer 並移至 ****https://go.microsoft.com/fwlink/p/?LinkID=403802**。
3.	進行註冊 Office 365 企業 E3 試用版的程序。

當系統提示輸入**公司電子郵件地址**時，請指定您新的 Microsoft 帳戶。

當系統提示建立識別碼時，請輸入初始 Office 365 帳戶的名稱、您的虛構公司名稱，然後輸入密碼。在安全的位置記錄產生的電子郵件地址 (例如 user123@contoso123.onmicrosoft.com) 以及密碼。您將需要此資訊以完成階段 3 的 Azure AD Connect 設定。

完成時，您應該會看到主要的 Office 365 入口網站頁面。在最上方的功能區中，按一下 [系統管理]，然後按一下 [Office 365]。Office 365 系統管理中心頁面隨即出現。讓 CLIENT1 保持這個頁面開啟。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph2.png)

## 第 3 階段：設定 DirSync 伺服器 (DS1)

從 Azure 入口網站，視需要啟動 DC2 電腦。

接下來，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 DS1 的 Azure 虛擬機器。在執行這些命令之前，先填入變數值並移除 < and > 字元。

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName DS1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DS1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DS1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DS1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，使用 Azure 入口網站，利用本機系統管理員帳戶的認證連線到 DS1 虛擬機器。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DS1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 192.168.0.4 的四次成功回覆。

接下來，在 Windows PowerShell 提示字元中使用下列命令將 DS1 加入 CORP Active Directory 網域中。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

系統提示提供 **Add-Computer** 命令的網域帳戶認證時，請使用 CORP\\User1 帳戶。

重新啟動之後，請使用 Azure 入口網站，利用 CORP\\User1 帳戶和密碼連線到 DS1。

接下來，在系統管理員層級 Windows PowerShell 命令提示字元下使用此命令，在 DS1 上安裝 .NET 3.5。

	Add-WindowsFeature NET-Framework-Core

接著，對於 Office 365 FastTrack 試用啟用 Directory 同步處理。

1.	在 CLIENT1 上，於 [Office 365 系統管理中心] 頁面的左窗格中，按一下 [使用者]，然後按一下 [作用中使用者]。
2.	對於 **Active Directory 同步處理**，請按一下 [設定]。
3.	在 [設定及管理 Active Directory 同步處理] 頁面的步驟 3 中，按一下 [啟用]。
4.	出現 [您要啟用 Active Directory 同步處理嗎?] 的提示時，按一下 [啟用]。執行此動作後，步驟 3 將顯示 [Active Directory 同步處理已啟動]。
5.	在 CLIENT1 上保持開啟 [設定及管理 Active Directory 同步處理] 頁面。

接下來，從 DC1 的 Windows PowerShell 提示字元中，**逐一**執行這些命令，以建立名為 contoso\_users 的新組織單位以及 Marci Kaufman 和 Lynda Meyer 兩個新的使用者帳戶。

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

您執行各個 **New-ADUser** Windows PowerShell 命令時，系統會提示您輸入新使用者的密碼。記錄這些密碼並儲存於安全之處。稍後您將需要這些資訊。

接下來，在 DS1 上安裝及設定 Azure AD Connect 工具。

1.	執行 Internet Explorer，在網址列中輸入 ****https://www.microsoft.com/download/details.aspx?id=47594**，然後按 ENTER 鍵。
2.	執行 Microsoft Azure AD Connect 安裝程式。
3.	從桌面上，按兩下 [Azure AD Connect]。
4.	在 [歡迎] 頁面上，選取 [我同意授權合約及隱私權注意事項]，然後按一下 [繼續]。
5.	在 [快速設定] 頁面上，按一下 [使用快速設定]。
6.	在 [連線到 Azure AD] 頁面中，輸入您在階段 2 中設定 Office 365 FastTrack 試用時建立的初始帳戶電子郵件地址和密碼。按 [下一步]。
7.	在 [連線到 AD DS] 頁面上，於 [使用者名稱] 中輸入 **CORP\\User1**，並在 [密碼] 中輸入 User1 的帳戶密碼。按 [下一步]。
8.	在 [準備好設定] 頁面上，檢閱設定，然後按一下 [安裝]。
9.	在 [設定完成] 頁面上，按一下 [結束]。

接著，確認 CORP 網域中的使用者帳戶已同步處理至 Office 365。請注意，可能需要經過數小時才會進行同步處理。

在 CLIENT1 上，於 [設定及管理 Active Directory 同步處理] 頁面中，按一下此頁面步驟 6 中的**使用者**連結。如果 Directory 同步處理順利進行，您應該會看見如下所示的內容。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-example.png)

[狀態] 欄將指出已透過 Active Directory 網域的同步處理功能取得帳戶。

接著，示範 Lynda Myer Active Directory 帳戶的 Office 365 密碼同步。

1.	在 CLIENT1 上，於 [作用中使用者] 頁面中，選取 [Lynda Meyer] 帳戶。
2.	在 Lynda Meyer 帳戶內容中的 [已指派的授權] 下，按一下 [編輯]。
3.	在 [指派授權] 索引標籤上，選取 [設定使用者位置] 中的位置 (例如美國)。
4.	選取 [Microsoft Office 365 方案 E3]，然後按一下 [儲存]。
5.	關閉 Internet Explorer。
6.	執行 Internet Explorer 並移至 ****http://portal.microsoftonline.com**。
7.	使用 Lynda Meyer 的 Office 365 認證登入。她的使用者名稱將是 lyndam@<*虛構名稱*>.onmicrosoft.com。密碼是 Lynda Meyer Active Directory 使用者帳戶密碼。
8.	成功登入後，您將看見 Office 365 入口網站的主要首頁顯示「讓今天有所不同」。

這是您目前的組態。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
這個環境到此準備就緒，可供您測試運用 Office 365 DirSync 功能的 Office 365 應用程式，或測試 DS1 的 DirSync 功能和效能。

## 後續步驟

- 在[生產](http://technet.microsoft.com/library/dn635310.aspx)中部署此工作負載。

<!---HONumber=AcomDC_0413_2016-->