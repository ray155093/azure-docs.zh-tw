<properties
	pageTitle="Azure VM 的詳細 SSH 疑難排解 |Microsoft Azure"
	description="針對連線到 Azure 虛擬機器的問題更詳細的 SSH 疑難排解步驟"
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
	ms.date="06/16/2016"
	ms.author="iainfou"/>

# 詳細的 SSH 疑難排解步驟

SSH 用戶端無法連線至 VM 上的 SSH 服務，可能涉及許多原因。如果您已經完成詳細的[一般 SSH 疑難排解步驟](virtual-machines-linux-troubleshoot-ssh-connection.md)，您必須進一步疑難排解連線問題。這篇文章會引導您完成詳細的疑難排解步驟，來判斷 SSH 連線失敗的位置和解決方法。

## 採取預備步驟

下列圖表顯示相關的元件。

![顯示 SSH 服務元件的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

下列步驟將協助您隔離失敗的來源，並找出解決方案或因應措施。

首先，檢查入口網站中 VM 的狀態。

在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，針對使用傳統部署模型建立的 VM︰

1. 選取 [虛擬機器] > VM 名稱。
2. 選取 VM 的 [儀表板] 檢查其狀態。
3. 選取 [監視器] 以查看計算、儲存體和網路資源的近期活動。
4. 選取 [端點] 以確保有 SSH 流量的端點。

在 [Azure 入口網站](https://portal.azure.com)：

1. 如果是使用傳統部署模型建立的 VM，請選取 [瀏覽] > [虛擬機器 (傳統)] > VM 名稱。

	-或-

	如果是使用 Resource Manager 模型建立的虛擬機器，請選取 [瀏覽] > [虛擬機器] > VM 名稱。

	VM 的狀態窗格應該會顯示為**執行中**。向下捲動以顯示計算、儲存體和網路資源的近期活動。

2. 選取 [設定] 以檢查端點、IP 位址和其他設定。

	若要識別 VM 中使用 Resource Manager 建立的端點，請檢查是否已定義[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。並驗證網路安全性群組所套用的規則，以及子網路中是否有參考這些規則。

若要確認網路連線，請檢查設定的端點，並判斷您是否可以透過另一個通訊協定 (例如 HTTP 或另一個服務) 連接到 VM。

在這些步驟之後，請再試一次 SSH 連線。


## 找出問題來源

您電腦上的 SSH 用戶端若不能連線至 Azure VM 上的 SSH 服務，可能為下列問題或錯誤設定所造成：

- SSH 用戶端電腦
- 組織邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Linux 為基礎的 Azure VM

## 來源 1：SSH 用戶端電腦

若要排除電腦為失敗來源的可能性，請確認您的電腦是否能 SSH 連線至另一部以 Linux 為基礎的內部部署電腦。

![強調 SSH 用戶端電腦元件的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

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

## 來源 2：組織邊緣裝置

若要排除組織邊緣裝置為失敗來源的可能性，請確認直接連線到網際網路的電腦能 SSH 連線到您的 Azure VM。如果您是透過網站間 VPN 或 Azure ExpressRoute 連線存取 VM，請跳至[來源 4：網路安全性群組](#nsg)。

![強調組織邊緣裝置的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

如果您沒有直接連線到網際網路的電腦，則您只要將新的 Azure VM 建立於專屬的資源群組或雲端服務中，然後使用它即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Linux 的虛擬機器](virtual-machines-linux-quick-create-cli.md)。當您完成測試後，請刪除此資源群組或 VM 及雲端服務。

如果您可以對直接連線到網際網路的電腦建立 SSH 連線，請檢查組織邊緣裝置的下列項目：

- 內部防火牆是否導致網際網路 SSH 流量受到封鎖
- Proxy 伺服器是否阻止 SSH 進行連線
- 在邊緣網路裝置上執行的入侵偵測或網路監視軟體是否阻止 SSH 進行連線

請和網路管理員合作，修正組織邊緣裝置的設定，允許網際網路的 SSH 流量。

## 來源 3：雲端服務端點和 ACL

> [AZURE.NOTE] 此來源僅適用於使用傳統部署模型所建立的 VM。如果是使用 Resource Manager 建立的 VM，請跳到[來源 4：網路安全性群組](#nsg)。

若要排除雲端服務端點和 ACL 為失敗來源的可能性，請確認同一個虛擬網路中的其他 Azure VM 是否能 SSH 連線至您的 VM。

![強調雲端服務端點和 ACL 的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

如果在相同的虛擬網路中沒有其他 VM，您可以輕鬆建立一部新的 VM。如需詳細資訊，請參閱[使用 CLI 在 Azure 上建立 Linux VM](virtual-machines-linux-quick-create-cli.md)。當您完成測試後，請刪除額外的 VM。

如果您可以 SSH 連線到相同虛擬網路中的 VM，請檢查下列項目：

- **目標 VM 上的 SSH 流量端點組態。** 此端點的私用 TCP 連接埠應符合 VM 上 SSH 服務正在接聽的 TCP 連接埠，預設連接埠為 22。如果是使用 Resource Manager 部署模型建立的 VM，請選取 [瀏覽] > [虛擬機器 (v2)] > VM 名稱 > [設定] > [端點]，確認 Azure 入口網站中的 SSH TCP 連接埠號碼。

- **目標虛擬機器上的 SSH 流量端點 ACL。** ACL 可讓您指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入 SSH 流量。檢查您的 ACL，確保允許來自您的 Proxy 或其他邊緣伺服器之公用 IP 位址的連入流量。如需詳細資訊，請參閱[關於網路存取控制清單 (ACL)](../virtual-network/virtual-networks-acl.md)。

若要排除端點為問題來源的可能性，請移除目前的端點，建立一個新端點，再指定 SSH 名稱 (TCP 連接埠 22 作為公用及私用連接埠號碼)。如需詳細資訊，請參閱[在 Azure 中設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。

<a id="nsg"></a>
## 來源 4：網路安全性群組

網路安全性群組可讓您更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許往來網際網路的 SSH 流量。如需詳細資訊，請參閱[關於網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

## 來源 5：以 Linux 為基礎的 Azure 虛擬機器

最後一個可能的問題來源就是 Azure 虛擬機器本身。

![強調以 Linux 為基礎的 Azure 虛擬機器的圖表](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

如果您還沒這麼做，請遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md) 中的指示進行。

再次嘗試從您的電腦連線。如果仍然失敗，下列為可能的原因：

- SSH 服務未在目標虛擬機器上執行。
- SSH 服務未在 TCP 連接埠 22 上接聽。若要測試這種情況，請在本機電腦上安裝 telnet 用戶端，並執行 "telnet *cloudServiceName*.cloudapp.net 22"。這樣可以判斷虛擬機器是否允許和 SSH 端點的輸入和輸出通訊。
- 目標虛擬機器上的本機防火牆具有防止輸入或輸出 SSH 流量的規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止 SSH 連線。


## 其他資源
如需疑難排解應用程式存取的詳細資訊，請參閱[針對存取在 Azure 虛擬機器上執行的應用程式進行疑難排解](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0622_2016-->