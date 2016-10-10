<properties
	pageTitle="遠端桌面的詳細疑難排解 |Microsoft Azure"
	description="針對您無法透過遠端桌面連線到 Azure 中 Windows 虛擬機器的錯誤，檢閱詳細的疑難排解步驟"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"
	keywords="無法連線到遠端桌面, 疑難排解遠端桌面, 遠端桌面無法連線, 遠端桌面錯誤, 遠端桌面疑難排解, 遠端桌面問題"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Azure 中 Windows VM 之遠端桌面連線問題的詳細疑難排解步驟

本文章提供診斷和修正複雜的以 Windows 為基礎的 Azure 虛擬機器的遠端桌面錯誤的詳細疑難排解步驟。

> [AZURE.IMPORTANT] 若要消除較常見的「遠端桌面」錯誤，請務必先閱讀[遠端桌面的基本疑難排解文章](virtual-machines-windows-troubleshoot-rdp-connection.md)，再繼續進行。

您可能會遇到不像[基本遠端桌面疑難排解指南](virtual-machines-windows-troubleshoot-rdp-connection.md)所涵蓋之任何特定錯誤訊息的「遠端桌面」錯誤訊息。請依照下列步驟來判斷「遠端桌面」(RDP) 用戶端為何無法連線至 Azure VM 上的 RDP 服務。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。


## 遠端桌面連線的元件

以下是 RDP 連線相關的元件：

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

在繼續之前，在心裡檢閱最後一個成功的遠端桌面連線至 VM 以來的變更可能有幫助。例如：

- VM 或包含 VM 之雲端服務的公用 IP 位址 (也稱為虛擬 IP 位址 [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) 已變更。RDP 失敗的原因可能是 DNS 用戶端快取仍然有針對該 DNS 名稱註冊的「舊 IP 位址」。請清除 DNS 用戶端快取並嘗試再次連接 VM。或嘗試直接與新的 VIP 連接。
- 您使用協力廠商應用程式來管理「遠端桌面」連線，而不是使用 Azure 入口網站所產生的連線。請確認應用程式組態包含正確的「遠端桌面」流量 TCP 連接埠。您可以在 [Azure 入口網站](https://portal.azure.com)中查看傳統虛擬機器的這個連接埠，方法是按一下該 VM 的 [設定] > [端點]。


## 預備步驟

繼續詳細疑難排解之前，

- 檢查 Azure 傳統入口網站或 Azure 入口網站中虛擬機器的狀態，以找出是否有任何明顯的問題。
- 依照[基本疑難排解指南中常見 RDP 錯誤的快速檢修步驟](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps)操作。


嘗試在完成這些步驟之後透過遠端桌面重新連接至 VM。


## 詳細的疑難排解步驟

由於下列來源的問題，可能會造成遠端桌面用戶端無法連線到 Azure VM 上的遠端桌面服務：

- [遠端桌面用戶端電腦](#source-1-remote-desktop-client-computer)
- [組織內部網路的邊緣裝置](#source-2-organization-intranet-edge-device)
- [雲端服務端點和存取控制清單 (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [網路安全性群組](#source-4-network-security-groups)
- [以 Windows 為基礎的 Azure VM](#source-5-windows-based-azure-vm)

## 來源 1：遠端桌面用戶端電腦

請確認您的電腦能夠建立「遠端桌面」連線，來連接到另一部內部部署且以 Windows 為基礎的電腦。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

如果不能，請檢查您電腦上的下列設定：

- 目前封鎖「遠端桌面」流量的本機防火牆設定。
- 目前阻止「遠端桌面」連線的本機安裝用戶端 Proxy 軟體。
- 目前阻止「遠端桌面」連線的本機安裝網路監視軟體。
- 目前阻止「遠端桌面」連線的其他類型安全性軟體，這些軟體會監視流量或允許/不允許特定類型的流量。

在所有這些情況下，請暫時停用軟體，然後嘗試透過「遠端連線」連接到內部部署電腦。如果這樣即可找出造成這個問題的實際原因，請和網路管理員合作，修正軟體設定以允許遠端桌面連線。

## 來源 2：組織內部網路的邊緣裝置

請確認直接連接到網際網路的電腦能遠端連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

如果您沒有直接連線到網際網路的電腦，則在資源群組或雲端服務中建立和測試新的 Azure 虛擬機器。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-hero-tutorial.md)。測試完成之後，您可以刪除虛擬機器和資源群組或雲端服務。

如果您可以對直接連線到網際網路的電腦建立遠端桌面連線，請檢查組織內部網路的邊緣裝置之下列項目：

- 目前封鎖對網際網路之 HTTPS 連線的內部防火牆。
- 目前阻止「遠端桌面」連線的 Proxy 伺服器。
- 目前在邊緣網路中的裝置上執行且阻止「遠端桌面」連線的入侵偵測或網路監視軟體。

請和網路管理員合作，修正您組織內部網路的邊緣裝置設定，允許以 HTTPS 為基礎的網際網路遠端桌面連線。

## 來源 3：雲端服務端點和 ACL

對於使用傳統部署模型建立的 VM，請確認另一部位於相同雲端服務或虛擬網路中的 Azure VM 能夠對您的 Azure VM 進行「遠端桌面」連線。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] 如果是在資源管理員中建立的虛擬機器，請跳到[來源 4：網路安全性群組](#source-4-network-security-groups)。

如果您沒有另一部虛擬機器位於相同的雲端服務或虛擬網路中，請建立一部。依照[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-hero-tutorial.md)中的步驟操作。測試完成之後，請刪除測試虛擬機器。

如果您可以透過「遠端桌面」連線到位於相同雲端服務或虛擬網路中的虛擬機器，請檢查下列設定：

- 目標 VM 上的遠端桌面流量端點組態：端點的私用 TCP 連接埠必須符合 VM 的遠端桌面服務所接聽的 TCP 連接埠 (預設值為 3389)。
- 目標 VM 上的遠端桌面流量端點的 ACL：ACL 讓您可指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入遠端桌面流量。檢查您的 ACL，以確保允許來自您的 Proxy 或其他邊緣伺服器的公用 IP 位址之連入流量。如需詳細資訊，請參閱[什麼是網路存取控制清單 (ACL)？](../virtual-network/virtual-networks-acl.md)

若要檢查端點是否為問題來源，請移除目前的端點，再選擇外部連接埠號碼介於 49152 到 65535 的隨機連接埠來建立新的端點。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。

## 來源 4：網路安全性群組

網路安全性群組能夠更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許來自網際網路的遠端桌面流量：

- 在 Azure 入口網站中選取您的 VM
- 按一下 [所有設定] | [網路介面]，然後選取您的網路介面。
- 按一下 [所有設定] | [網路安全性群組]，然後選取您的網路安全性群組。
- 按一下 [所有設定] | [輸入安全性規則]，並確定您有一個允許在 TCP 連接埠 3389 上進行 RDP 的規則。
	- 如果您還沒有規則，請按一下 [新增] 來建立規則。在通訊協定輸入 **TCP**，然後在目的地連接埠範圍輸入 **3389**。
	- 確定已將動作設定為 [允許]，然後按一下 [確定] 以儲存新的輸入規則。


如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

## 來源 5：以 Windows 為基礎的 Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

請使用 [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)來查看失敗的原因是否在 Azure 虛擬機器本身。如果這個診斷套件無法解決「透過 RDP 連線至 Azure VM (需要重新開機)」問題，請依照[這篇文章](virtual-machines-windows-reset-rdp.md)中的指示操作。這篇文章會重設虛擬機器上的「遠端桌面」服務：

- 啟用「遠端桌面」 Windows 防火牆預設規則 (TCP 連接埠 3389)。
- 藉由將 HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections 登錄值設為 0 ，啟用遠端桌面連線。

再次嘗試來自您電腦的連線。如果您還是無法透過遠端桌面連線，請檢查下列可能的問題：

- 遠端桌面服務未在目標 VM 上執行。
- 遠端桌面服務未在 TCP 連接埠 3389 上接聽。
- Windows 防火牆或另一個本機防火牆有阻止遠端桌面流量的輸出規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止遠端桌面連線。

對於使用傳統部署模型所建立的 VM，您可以使用關於 Azure 虛擬機器的遠端 Azure PowerShell 工作階段。首先，您需要為虛擬機器的代管雲端服務安裝憑證。移至[設定對 Azure 虛擬機器的安全遠端 PowerShell 存取](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)，然後將 **InstallWinRMCertAzureVM.ps1** 指令檔下載到您的本機電腦。

接下來，如果尚未安裝 Azure PowerShell，則請先安裝。請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

接下來，開啟 Azure PowerShell 命令提示字元，並將目前資料夾變更為 **InstallWinRMCertAzureVM.ps1** 指令碼檔案的位置。若要執行 Azure PowerShell 指令碼，您必須設定正確的執行原則。請執行 **Get-ExecutionPolicy** 命令來判斷您目前的原則層級。如需設定適當層級的相關資訊，請參閱 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下來，請填入您的 Azure 訂用帳戶名稱、雲端服務名稱以及虛擬機器名稱 (移除 "<" 和 ">" 字元)，然後再執行這些命令。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

您可以從 **Get-AzureSubscription** 命令顯示畫面中的 _SubscriptionName_ 屬性，取得正確的訂用帳戶名稱。您可以從 **Get-AzureVM** 命令顯示畫面中的 _ServiceName_ 欄，取得虛擬機器的雲端服務名稱。

請檢查您是否擁有新憑證。開啟目前使用者的 [憑證] 嵌入式管理單元，然後查看 [受信任的根憑證授權單位\\憑證] 資料夾。您應該在 Issued To 資料行中查看具有您的雲端服務之 DNS 名稱的憑證 (範例：cloudservice4testing.cloudapp.net)。

接下來，使用這些命令起始遠端 Azure PowerShell 工作階段。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

輸入有效的系統管理員認證之後，您應該會看到類似下列 Azure PowerShell 提示字元的內容：

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

此提示的第一個部分是您的雲端服務名稱，其中包含目標 VM，這可以與 "cloudservice4testing.cloudapp.net" 不同。您現在可以針對此雲端服務發出 Azure PowerShell 命令，以調查所提到的問題並修正組態。

### 若要手動更正接聽 TCP 連接埠的遠端桌面服務

如果您無法針對「透過 RDP 連線至 Azure VM (需要重新開機)」問題執行 [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，請在遠端 Azure PowerShell 工作階段提示字元中執行這個命令。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 屬性會顯示目前的連接埠號碼。如有需要，請使用此命令，將遠端桌面連接埠號碼變更回預設值 (3389)。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

確認已使用此命令將連接埠變更為 3389。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

使用此命令結束遠端 Azure PowerShell 工作階段。

	Exit-PSSession

確認 Azure VM 的遠端桌面端點也正在使用 TCP 連接埠 3398 做為其內部連接埠。重新啟動 Azure VM，然後再試一次遠端桌面連線。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)

[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0928_2016-->