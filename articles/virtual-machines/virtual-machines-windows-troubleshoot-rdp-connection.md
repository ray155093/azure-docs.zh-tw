<properties
	pageTitle="疑難排解遠端桌面連線到 Azure VM | Microsoft Azure"
	description="在 Windows VM 上疑難排解遠端桌面連線錯誤。取得快速因應步驟、錯誤訊息的說明和詳細的網路疑難排解。"
	keywords="遠端桌面錯誤、遠端桌面連線錯誤、無法連接到 VM、遠端桌面疑難排解"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# 疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線

有各式各樣的原因可能導致以 Windows 為基礎之 Azure 虛擬機器 (VM) 的「遠端桌面通訊協定」(RDP) 連線失敗。問題可能與 VM 上的遠端桌面服務、網路連線或主機電腦上的遠端桌面用戶端有關。本文將引導您完成一些可解決 RDP 連線問題的最常見方法。如果這裡沒有列出您的問題，或您仍然無法透過 RDP 連接到您的 VM，您也可以閱讀 [更詳細的 RDP 疑難排解概念和步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。

<a id="quickfixrdp"></a>

## 為使用 Resource Manager 部署模型建立的 VM 進行疑難排解

在每個疑難排解步驟完成之後，請嘗試重新連接到 VM。

> [AZURE.TIP] 如果入口網站中的 [連接] 按鈕呈現灰色，而且您未透過 [ExpressRoute](../expressroute/expressroute-introduction.md) 或[網站間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 連線來連接到 Azure，您就必須先建立 VM 並為其指派公用 IP 位址，才能使用 RDP。您可以深入了解 [Azure 中的公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

- 使用 Powershell 重設遠端存取。
	- 如果您尚未這樣做，請參閱[安裝並設定最新的 Azure PowerShell](../powershell-install-configure.md)。

	- 使用下列任一個 PowerShell 命令來重設您的 RDP 連線。使用與您的設定相關的值來取代 `myRG`、`myVM`、`myVMAccessExtension` 和位置。

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	或

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] 在前面的範例中，`myVMAccessExtension` 或 `MyVMAccess` 是您為將在程序中安裝的新擴充功能指定的名稱。這通常會設定為 VM 的名稱。如果您先前使用了 VMAccessAgent，則可以使用 `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` 來檢查 VM 的屬性，以取得現有擴充功能的名稱。然後在輸出的「擴充功能」區段下尋找。由於一部 VM 上只能有一個 VMAccessAgent，因此使用 `Set-AzureRmVMExtension` 時，您還需要新增 `-ForceReRun` 參數來強制重新註冊代理程式。

- 重新啟動您的 VM 以處理其他啟動問題。選取 [瀏覽] > [虛擬機器] >「您的 VM」> [重新啟動]。

- [將 VM 重新部署到新的 Azure 節點](virtual-machines-windows-redeploy-to-new-node.md)。

	請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器相關聯的動態 IP 位址。
	
- 確認您的[網路安全性群組規則](../virtual-network/virtual-networks-nsg.md)允許 RDP 流量 (TCP 連接埠 3389)。

- 檢閱 VM 的主控台記錄檔或螢幕擷取畫面，以修正開機問題。選取 [瀏覽] > [虛擬機器] >「您的 Windows 虛擬機器」 > [支援 + 疑難排解] > [開機診斷]。

- [重設您的 VM 密碼](virtual-machines-windows-reset-rdp.md)。

- 如果您仍然遇到 RDP 問題，您可以[開啟支援要求](https://azure.microsoft.com/support/options/)或閱讀[更詳細的 RDP 疑難排解概念和步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## 為使用傳統部署模型建立的 VM 進行疑難排解

在每個疑難排解步驟完成之後，請嘗試重新連接到 VM。

- 從 [Azure 入口網站](https://portal.azure.com)重設「遠端桌面」服務。選取 [瀏覽] > [虛擬機器 (傳統)] >「您的 VM」> [重設遠端...]。

- 重新啟動您的 VM 以處理其他啟動問題。選取 [瀏覽] > [虛擬機器 (傳統)] >「您的 VM」> [重新啟動]。

- [將 VM 重新部署到新的 Azure 節點](virtual-machines-windows-redeploy-to-new-node.md)。

	請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器相關聯的動態 IP 位址。
	
- 確認您的[雲端服務端點允許 RDP 流量](../cloud-services/cloud-services-role-enable-remote-desktop.md)。

- 檢閱您的 VM 主控台記錄檔或螢幕擷取畫面以修正開機問題。選取 [瀏覽] > [虛擬機器 (傳統)] >「您的 VM」> [設定] > [開機診斷]。

- 檢查 VM 的「資源健康狀態」是否有任何平台問題。選取 [瀏覽] > [虛擬機器 (傳統)] >「您的 VM」> [設定] > [檢查健康情況]。

- [重設您的 VM 密碼](virtual-machines-windows-reset-rdp.md)。

- 如果您仍然遇到 RDP 問題，您可以[開啟支援要求](https://azure.microsoft.com/support/options/)或閱讀[更詳細的 RDP 疑難排解概念和步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## 疑難排解特定的遠端桌面連線錯誤

嘗試透過 RDP 連接到您的 VM 時，您可能會收到特定的錯誤。以下是您會看到的最常見錯誤訊息：

- [遠端工作階段中斷，因為沒有提供授權的遠端桌面授權伺服器可以使用](#rdplicense)。

- [遠端桌面找不到電腦「名稱」](#rdpname)。

- [發生驗證錯誤。無法連絡本機安全性授權](#rdpauth)。

- [Windows 安全性錯誤：您的認證無法運作](#wincred)。

- [這部電腦無法連線到遠端電腦](#rdpconnect)。

<a id="rdplicense"></a>
### 遠端工作階段中斷，因為沒有提供授權的遠端桌面授權伺服器可以使用。

原因：遠端桌面角色的 120 天授權寬限期已過期，您需要安裝授權。

作為因應措施，請從入口網站儲存 RDP 檔案的本機複本，並在 PowerShell 命令提示字元執行該命令來連接。這只會停用該連線的授權：

		mstsc <File name>.RDP /admin

如果您並非真的需要多於兩個對 VM 的同步遠端桌面連線，則您可以使用伺服器管理員來移除遠端桌面伺服器角色。

如需詳細資訊，請參閱部落格文章：[Azure VM fails with "No Remote Desktop License Servers available" (Azure VM 因「沒有可用的遠端桌面授權伺服器」而失敗)](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx)。

<a id="rdpname"></a>
### 遠端桌面找不到電腦「名稱」。

原因：您電腦上的遠端桌面用戶端無法解析此 RDP 檔案設定中的電腦名稱。

可能的解決方案：

- 如果您位於組織內部網路，請確保您的電腦能存取 Proxy 伺服器，並能將 HTTPS 流量傳送給 Proxy 伺服器。

- 如果您使用本機儲存的 RDP 檔案，請嘗試使用入口網站所產生的檔案。這可確保您擁有虛擬機器或雲端服務的正確 DNS 名稱，以及 VM 的端點連接埠。以下是由入口網站產生的 RDP 檔案範例：

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

此 RDP 檔案的位址部分包含︰
- 雲端服務的完整網域名稱，其中包含 VM (本例中為 "tailspin-azdatatier.cloudapp.net")。

- 遠端桌面流量 (55919) 端點的外部 TCP 連接埠。

<a id="rdpauth"></a>
### 發生驗證錯誤。無法連絡本機安全性授權。

原因：目標 VM 在認證的使用者名稱部分找不到安全性授權。

當您的使用者名稱格式為 *SecurityAuthority*\*UserName* (範例：CORP\\User1) 時，*SecurityAuthority* 部分會是 VM 的電腦名稱 (針對本機安全性授權) 或 Active Directory 網域名稱。

可能的解決方案：

- 如果帳戶是 VM 的本機帳戶，請確認 VM 名稱拼寫正確。

- 如果該帳戶位於 Active Directory 網域，請檢查網域名稱的拼字。

- 如果該帳戶是 Active Directory 網域帳戶且網域名稱的拼字正確，請確認該網域內可以使用網域控制站。其為包含網域控制站的 Azure 虛擬網路中常見的問題，網域控制站無法使用的原因是其尚未啟動為因應措施，您可以使用本機系統管理員帳戶，而不使用網域帳戶。

<a id="wincred"></a>
### Windows 安全性錯誤：您的認證無法運作。

原因：目標 VM 無法驗證您的帳戶名稱和密碼。

以 Windows 為基礎的電腦可以驗證本機帳戶或網域帳戶之認證。

- 針對本機帳戶，請使用 *ComputerName*\*UserName* 語法 (範例：SQL1\\Admin4798)。
- 針對網域帳戶，請使用 *DomainName*\*UserName* 語法 (範例：CONTOSO\\peterodman)。

如果您在新的 Active Directory 樹系將 VM 提升為網域控制站，您用來登入的本機系統管理員帳戶會轉換為對等的帳戶，在新樹系和網域中使用相同的密碼。本機帳戶隨即刪除。

例如，如果您以本機帳戶 DC1\\DCAdmin 登入，然後將此虛擬機器提升為 corp.contoso.com 網域中新樹系的網域控制器，則會刪除 DC1\\DCAdmin 本機帳戶，並且以相同密碼建立一個新的網域帳戶 (CORP\\DCAdmin)。

請確保帳戶名稱為虛擬機器可驗證為有效帳戶的名稱，且密碼正確。

如果您需要變更本機系統管理員帳戶的密碼，請參閱[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)。

<a id="rdpconnect"></a>
### 這部電腦無法連線到遠端電腦。

原因：用來連接的帳戶並沒有遠端桌面的登入權限。

每部 Windows 電腦都有遠端桌面使用者本機群組，其中包含能夠遠端登入的帳戶和群組。本機系統管理員群組成員也有權限，即使這些帳戶未列在遠端桌面使用者本機群組中。對於加入網域的機器，本機系統管理員群組也包含此網域的網域系統管理員。

請確保您用於連接的帳戶具有遠端桌面登入權限。因應措施是使用網域或本機系統管理員帳戶透過遠端桌面進行連接。然後使用 Microsoft Management Console 嵌入式管理單元 ([系統工具] > [本機使用者和群組] > [群組] > [遠端桌面使用者]) 將所需帳戶新增至「遠端桌面」使用者本機群組。

## 為一般的遠端桌面錯誤進行疑難排解

如果這些錯誤皆未發生，而您仍然無法透過「遠端桌面」連接到 VM，請閱讀詳細的[遠端桌面疑難排解指南](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)

[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

[為以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線疑難排解](virtual-machines-linux-troubleshoot-ssh-connection.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0622_2016-->