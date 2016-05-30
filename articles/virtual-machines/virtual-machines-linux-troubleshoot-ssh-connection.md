<properties
	pageTitle="疑難排解 SSH 連線到 Azure VM | Microsoft Azure"
	description="針對執行 Linux 的 Azure 虛擬機器進行疑難排解和修正 SSH 錯誤，如 SSH 連線失敗或 SSH 連線被拒。"
	keywords="ssh 連線被拒, ssh 錯誤, azure ssh, SSH 連線失敗"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# 為以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線進行疑難排解

當您嘗試連線到以 Linux 為基礎的 Azure 虛擬機器時，有許多原因可能會導致安全殼層 (SSH) 錯誤。本文將協助您找出原因並加以更正。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後選取 [取得支援]。如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 修正常見的 SSH 錯誤

本節列出常見 SSH 連線問題的快速修正方法。

### 為使用傳統部署模型建立的虛擬機器疑難排解

請嘗試下列步驟，來解決使用傳統部署模型所建立之虛擬機器中最常見的 SSH 連線失敗。在每個步驟完成後，請嘗試重新連接至虛擬機器。

- 透過 [Azure 入口網站](https://portal.azure.com)，重設遠端存取。在 Azure 入口網站中，依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [重設遠端...]。

- 重新啟動虛擬機器。在 [Azure 入口網站](https://portal.azure.com)中，依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [重新啟動]。

	-或-

	在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [虛擬機器] > [執行個體] > [重新啟動]。

- 將虛擬機器重新部署至新的 Azure 節點。如需如何執行這項作業的資訊，請參閱[將虛擬機器重新部署至新的 Azure 節點](virtual-machines-windows-redeploy-to-new-node.md)。

	請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器相關聯的動態 IP 位址。

- 請遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md) 中關於虛擬機器的指示進行，以：
	- 重設密碼或 SSH 金鑰。
	- 建立新的 _sudo_ 使用者帳戶。
	- 重設 SSH 組態。

- 檢查 VM 的資源健康狀態是否有任何平台問題。<br> 依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [設定] > [檢查健康情況]。


### 為使用 Resource Manager 部署模型建立的虛擬機器疑難排解

請嘗試下列步驟，來解決使用 Azure Resource Manager 部署模型所建立之虛擬機器最常見的 SSH 問題。

#### 重設 SSH 連線
使用 Azure CLI，確定已安裝 [Microsoft Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md) 2.0.5 版或更新版本。

如果尚未安裝，請[安裝 Azure CLI 並連線至您的 Azure 訂用帳戶](../xplat-cli-install.md)，並使用 `azure login` 命令登入。請確定您處於 Resource Manager 模式：```
	azure config mode arm
	```

使用下列其中一種方法重設 SSH 連線：

**方法 1：**如下列範例所示，使用 `vm reset-access` 命令。這將會在虛擬機器上安裝 `VMAccessForLinux` 擴充功能。

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```



**方法 2：**使用下列內容建立名稱為 PrivateConf.json 的檔案︰

	```
	{  
		"reset_ssh":"True"
	}
	```

然後以手動方式執行 `VMAccessForLinux` 擴充功能以重設 SSH 連線：

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

#### 重設 SSH 認證

**方法 1：**執行 `vm reset-access` 命令以設定任何 SSH 認證。

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

如需詳細資訊，請在命令列上輸入 `azure vm reset-access -h`。

**方法 2：**使用下列內容建立名稱為 PrivateConf.json 的檔案︰

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

然後使用上述檔案名稱 (PrivateConf.json) 執行 Linux 擴充功能：

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

如果您想嘗試其他疑難排解方法，可以遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md) 中的類似步驟。請記得修改 Resource Manager 模式的 Azure CLI 指示。


## 詳細的 SSH 錯誤疑難排解

如果 SSH 用戶端仍然無法連線至虛擬機器上的 SSH 服務，這可能涉及許多原因。下列圖表顯示相關的元件。

![顯示 SSH 服務元件的圖表](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot1.png)

下列步驟將協助您隔離失敗的來源，並找出解決方案或因應措施。

### 採取預備步驟

首先，在入口網站中檢查虛擬機器的狀態。

在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，針對使用傳統部署模型建立的虛擬機器︰

1. 選取 [虛擬機器] > VM 名稱。
2. 選取 VM 的 [儀表板] 檢查其狀態。
3. 選取 [監視器] 以查看計算、儲存體和網路資源的近期活動。
4. 選取 [端點] 以確保有 SSH 流量的端點。

在 [Azure 入口網站](https://portal.azure.com)：

1. 如果是使用傳統部署模型建立的虛擬機器，請選取 [瀏覽] > [虛擬機器 (傳統)] > VM 名稱。

	-或-

	如果是使用 Resource Manager 模型建立的虛擬機器，請選取 [瀏覽] > [虛擬機器 (傳統)] > VM 名稱。

	虛擬機器的狀態窗格應該會顯示為**執行中**。向下捲動以顯示計算、儲存體和網路資源的近期活動。

2. 選取 [設定] 以檢查端點、IP 位址和其他設定。

	若要識別虛擬機器中使用 Resource Manager 建立的端點，請檢查是否已定義[網路安全性群組](../virtual-network/virtual-networks-nsg.md)，並驗證網路安全性群組所套用的規則，以及子網路中是否有參考這些規則。

若要確認網路連線，請檢查設定的端點，並判斷您是否可以透過另一個通訊協定 (例如 HTTP 或另一個服務) 連接到 VM。

在這些步驟之後，請再試一次 SSH 連線。


### 找出問題來源

您電腦上的 SSH 用戶端若不能連線至 Azure 虛擬機器上的 SSH 服務，可能為下列問題或錯誤設定所造成：

- SSH 用戶端電腦
- 組織邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Linux 為基礎的 Azure 虛擬機器

#### 來源 1：SSH 用戶端電腦

若要排除電腦為失敗來源的可能性，請確認您的電腦是否能 SSH 連線至另一部以 Linux 為基礎的內部部署電腦。

![強調 SSH 用戶端電腦元件的圖表](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot2.png)

如果失敗，請在您的電腦上檢查下列項目：

- 正封鎖輸入或輸出 SSH 流量的本機防火牆設定 (TCP 22)
- 正阻止 SSH 連線的本機安裝用戶端 Proxy 軟體
- 正阻止 SSH 連線的本機安裝網路監視軟體
- 其他類型的安全性軟體，這會監視流量或允許/不允許特定類型的流量。

如果有上述任何情況發生，請暫時停用該軟體，然後再嘗試 SSH 連線到內部部署的電腦，以找出電腦連線被封鎖的原因。接著，請和網路管理員合作，修正軟體設定以允許 SSH 連線。

如果您使用憑證驗證，請確認您在主目錄中擁有 ssh 資料夾的下列權限：

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (或您儲存私密金鑰的任何其他檔案)
- Chmod 644 ~/.ssh/known\_hosts (包含您已透過 SSH 連接的主機)

#### 來源 2：組織邊緣裝置

若要排除組織邊緣裝置為失敗來源的可能性，請確認直接連線到網際網路的電腦能 SSH 連線到您的 Azure VM。如果您是透過網站間 VPN 或 Azure ExpressRoute 連線存取 VM，請跳至[來源 4：網路安全性群組](#nsg)。

![強調組織邊緣裝置的圖表](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot3.png)

如果您沒有直接連線到網際網路的電腦，則您只要將新的 Azure 虛擬機器建立於專屬的資源群組或雲端服務中，並加以使用即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Linux 的虛擬機器](virtual-machines-linux-quick-create-cli.md)。當您完成測試後，請刪除此資源群組或虛擬機器及雲端服務。

如果您可以對直接連線到網際網路的電腦建立 SSH 連線，請檢查組織邊緣裝置的下列項目：

- 內部防火牆是否導致網際網路 SSH 流量受到封鎖
- Proxy 伺服器是否阻止 SSH 進行連線
- 在邊緣網路裝置上執行的入侵偵測或網路監視軟體是否阻止 SSH 進行連線

請和網路管理員合作，修正組織邊緣裝置的設定，允許網際網路的 SSH 流量。

#### 來源 3：雲端服務端點和 ACL

> [AZURE.NOTE] 此來源僅適用於使用傳統部署模型所建立的虛擬機器。如果是使用 Resource Manager 建立的虛擬機器，請跳到[來源 4：網路安全性群組](#nsg)。

若要排除雲端服務端點和 ACL 為失敗來源的可能性，請確認同一個虛擬網路中的其他 Azure VM 是否能 SSH 連線至您的 VM。

![強調雲端服務端點和 ACL 的圖表](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot4.png)

如果在相同的虛擬網路中沒有其他 VM，您可以輕鬆建立一部新的 VM。如需詳細資訊，請參閱[使用 CLI 在 Azure 上建立 Linux VM](virtual-machines-linux-quick-create-cli.md)。當您完成測試後，請刪除額外的 VM。

如果您可以 SSH 連線到相同虛擬網路中的 VM，請檢查下列項目：

- **目標 VM 上的 SSH 流量端點組態。** 此端點的私用 TCP 連接埠應符合 VM 上 SSH 服務正在接聽的 TCP 連接埠，預設連接埠為 22。如果是使用 Resource Manager 部署模型建立的 VM，請選取 [瀏覽] > [虛擬機器 (v2)] > VM 名稱 > [設定] > [端點]，確認 Azure 入口網站中的 SSH TCP 連接埠號碼。

- **目標虛擬機器上的 SSH 流量端點 ACL。** ACL 可讓您指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入 SSH 流量。檢查您的 ACL，確保允許來自您的 Proxy 或其他邊緣伺服器之公用 IP 位址的連入流量。如需詳細資訊，請參閱[關於網路存取控制清單 (ACL)](../virtual-network/virtual-networks-acl.md)。

若要排除端點為問題來源的可能性，請移除目前的端點，建立一個新端點，再指定 SSH 名稱 (TCP 連接埠 22 作為公用及私用連接埠號碼)。如需詳細資訊，請參閱[在 Azure 中設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。

<a id="nsg"></a>
#### 來源 4：網路安全性群組

網路安全性群組可讓您更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許往來網際網路的 SSH 流量。如需詳細資訊，請參閱[關於網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

#### 來源 5：以 Linux 為基礎的 Azure 虛擬機器

最後一個可能的問題來源就是 Azure 虛擬機器本身。

![強調以 Linux 為基礎的 Azure 虛擬機器的圖表](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot5.png)

如果您還沒這麼做，請遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md) 中的指示進行。

再次嘗試從您的電腦連線。如果仍然失敗，下列為可能的原因：

- SSH 服務未在目標虛擬機器上執行。
- SSH 服務未在 TCP 連接埠 22 上接聽。若要測試這種情況，請在本機電腦上安裝 telnet 用戶端，並執行 "telnet *cloudServiceName*.cloudapp.net 22"。這樣可以判斷虛擬機器是否允許和 SSH 端點的輸入和輸出通訊。
- 目標虛擬機器上的本機防火牆具有防止輸入或輸出 SSH 流量的規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止 SSH 連線。


## 其他資源

- 如需針對使用傳統部署模型所建立之虛擬機器進行疑難排解的詳細資訊，請參閱[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md)。

- 如需為使用 Resource Manager 部署模型建立之虛擬機器進行疑難排解的詳細資訊，請參閱：
	- [疑難排解以 Windows 為基礎之 Azure 虛擬機器的 Windows 遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)
	-	[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->