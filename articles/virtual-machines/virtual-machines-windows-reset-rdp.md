<properties
	pageTitle="在 Windows VM 上重設密碼或遠端桌面 | Microsoft Azure"
	description="在使用 Resource Manager 部署模型建立的 Windows VM 上，重設管理員密碼或遠端桌面服務。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="iainfou"/>

# 如何在 Windows VM 中重設遠端桌面服務或其登入密碼

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果您因為忘記密碼或遠端桌面服務組態有問題，而無法連線至 Windows 虛擬機器，可以重設本機系統管理員密碼，或重設遠端桌面服務組態。您可以使用 Azure 入口網站或 Azure PowerShell 中的 VM 存取延伸模組來重設密碼。如果您使用 PowerShell，請務必在工作電腦上安裝最新的 PowerShell 模組，並登入您的 Azure 訂用帳戶。如需詳細步驟，請閱讀[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

> [AZURE.TIP] 您可以使用 `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version` 來檢查已安裝的 PowerShell 版本

## Resource Manager 部署模型中的 Windows VM

### Azure 入口網站
依序按一下 [瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [所有設定] > [重設密碼]，以選取您的 VM。密碼重設刀鋒視窗會如下所示︰

![密碼重設頁面](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

輸入使用者名稱和新的密碼，然後按一下 [儲存]。嘗試再次連接到您的 VM。

### VMAccess 延伸模組和 PowerShell

確認您已安裝 Azure PowerShell 1.0 或更新版本，並且已使用 `Login-AzureRmAccount` Cmdlet 登入帳戶。

#### **重設本機系統管理員帳戶密碼**

您可以使用 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell 命令來重設系統管理員密碼或使用者名稱。

使用下列命令來建立本機系統管理員帳戶認證︰

	$cred=Get-Credential

如果您輸入與目前帳戶不同的名稱，則下列 VMAccess 擴充功能命令會重新命名本機系統管理員帳戶、將密碼指派給該帳戶，以及發出遠端桌面登出。如果本機系統管理員帳戶已停用，則 VMAccess 延伸項目會將它啟用。

使用 VM 存取延伸模組來設定新認證，如下所示︰

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
		-Location WestUS -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


使用和您的設定相關的值取代 `myRG`、`myVM`、`myVMAccess` 和 Location。


#### **重設遠端桌面服務組態**

您可以使用 [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) 或 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) 來重設 VM 的遠端存取，如下所示。(將 `myRG`、`myVM`、`myVMAccess` 和 Location 取代為您自己的值)。

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

或：<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] 兩個命令都會在虛擬機器中加入新的具名 VM 存取代理程式。不論如何，一部 VM 只能有一個 VM 存取代理程式。若要順利設定 VM 存取代理程式屬性，請使用 `Remove-AzureRmVMAccessExtension` 或 `Remove-AzureRmVMExtension` 移除先前設定的存取代理程式。從 Azure PowerShell 1.2.2 版開始，搭配使用 `Set-AzureRmVMExtension` 與 `-ForceRerun` 選項時，您可以避免這個步驟。使用 `-ForceRerun` 時，請務必使用與前述命令所設定之 VM 存取代理程式相同的名稱。

如果您仍然無法從遠端連接虛擬機器，請參閱[針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](virtual-machines-windows-troubleshoot-rdp-connection.md)，以取得其他值得一試的步驟。


## 傳統部署模型中的 Windows VM

### Azure 入口網站

對於使用傳統部署模型所建立的虛擬機器，您可以使用 [Azure 入口網站](https://portal.azure.com)來重設遠端桌面服務。依序按一下 [瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重設遠端]。下列頁面隨即出現。

![重設 RDP 組態頁面](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

您也可以嘗試重設本機系統管理員帳戶的名稱和密碼。依序按一下 [瀏覽] > [虛擬機器 (傳統)] > *您的 Windows 虛擬機器* > [所有設定] > [重設密碼]。下列頁面隨即出現。

![密碼重設頁面](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

輸入新的使用者名稱和密碼，然後按一下 [儲存]。

### VMAccess 延伸模組和 PowerShell

確認已在虛擬機器上安裝 VM 代理程式。只要可以使用 VM 代理程式，您不需要先安裝 VMAccess 延伸模組即可使用。使用以下命令確認已在虛擬機器上安裝 VM 代理程式。(分別將 "myCloudService" 和 "myVM" 取代為雲端服務和 VM 的名稱。若要了解這些項目，您可以在不搭配任何參數的情況下執行 `Get-AzureVM`。

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

如果 **write-host** 命令顯示 **True**，則會安裝 VM 代理程式。如果顯示 **False**，請參閱 Azure 部落格文章 [VM 代理程式與延伸模組 - 第 2 部分](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)中的指示和下載連結。

如果您使用入口網站建立虛擬機器，請檢查 `$vm.GetInstance().ProvisionGuestAgent` 是否會傳回 **True**。否則，請使用此命令加以設定︰

	$vm.GetInstance().ProvisionGuestAgent = $true

此命令可避免在後續步驟中執行 **Set-AzureVMExtension** 命令時發生下列錯誤：「必須先在 VM 物件啟用佈建客體代理程式，才能設定 IaaS VM 存取延伸模組」。

#### **重設本機系統管理員帳戶密碼**

使用目前的本機系統管理員帳戶名稱和新密碼建立登入認證，然後執行 `Set-AzureVMAccessExtension`，如下所示。

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password  | Update-AzureVM

如果您輸入與目前帳戶不同的名稱，則 VMAccess 擴充功能會重新命名本機系統管理員帳戶、將密碼指派給該帳戶，以及發出遠端桌面登出。如果本機系統管理員帳戶已停用，則 VMAccess 延伸項目會將它啟用。

這些命令也會重設遠端桌面服務組態。

#### **重設遠端桌面服務組態**

若要重設遠端桌面服務組態，請執行下列命令：

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 擴充功能會在虛擬機器上執行兩個命令：

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

此命令會啟用允許連入遠端桌面流量 (使用 TCP 連接埠 3389) 的內建 Windows 防火牆群組。

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

此命令會將 fDenyTSConnections 登錄值設為 0，以啟用遠端桌面連線。


## 其他資源

[Azure VM 延伸模組與功能](virtual-machines-windows-extensions-features.md)

[透過 RDP 或 SSH 連接至 Azure 虛擬機器](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0615_2016-->