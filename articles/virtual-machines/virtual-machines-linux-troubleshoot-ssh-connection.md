<properties
	pageTitle="SSH 連線至 Linux VM 被拒、失敗或發生錯誤 |Microsoft Azure"
	description="針對執行 Linux 的 Azure 虛擬機器進行疑難排解和修正 SSH 錯誤，如 SSH 連線失敗或 SSH 連線被拒。"
	keywords="ssh 連線被拒, ssh 錯誤, azure ssh, ssh 連線失敗"
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
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# 針對 SSH 連線至 Azure Linux VM 失敗、發生錯誤或被拒進行疑難排解

當您嘗試連線到以 Linux 為基礎的 Azure 虛擬機器 (VM) 時，有許多原因可能會導致安全殼層 (SSH) 錯誤、SSH 連線失敗或被拒。本文將協助您找出原因並加以更正。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後選取 [取得支援]。如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。

## 使用 Resource Manager 部署模型建立的 VM

您可以直接使用 Azure CLI 命令，或使用 [Azure VMAccessForLinux 擴充功能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)來重設認證或 SSHD。在每個疑難排解步驟完成之後，請再次嘗試連接到 VM。

### Azure CLI 先決條件

如果尚未安裝，請[安裝 Azure CLI 並連線至您的 Azure 訂用帳戶](../xplat-cli-install.md)。使用 `azure login` 命令登入，並確定您在 Resource Manager 模式下 (`azure config mode arm`)。

請確定已安裝 [Microsoft Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md) 2.0.5 版或更新版本。

### 重設 SSHD
SSHD 組態本身可能設定錯誤，或服務發生錯誤。您可以重設 SSH 以確定 SSH 組態本身有效。

#### Azure CLI
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### VM 存取擴充功能
存取擴充功能會讀取 json 檔案，其中定義要執行哪些動作，例如重設 SSH、重設 SSH 金鑰，新增新使用者等等。首先，使用下列內容建立名為 PrivateConf.json 的檔案：

```bash
{  
	"reset_ssh":"True"
}
```

然後以手動方式執行 `VMAccessForLinux` 擴充功能以重設 SSHD 連線：

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### 重設使用者的 SSH 認證
如果 SSHD 似乎能夠正常運作，您可以針對指定的使用者重設密碼。

#### Azure CLI
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

如果使用 SSH 金鑰驗證，您可以針對指定的使用者重設 SSH 金鑰：

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <usernamer> -M <~/.ssh/azure_id_rsa.pub>
```

#### VM 存取擴充功能
使用下列內容建立名稱為 PrivateConf.json 的檔案︰

```bash
{
	"username":"Username", "password":"NewPassword"
}
```

或若要針對指定的使用者重設 SSH 金鑰，請使用下列內容建立名稱為 PrivateConf.json 的檔案：

```bash
{
	"username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

完成上述其中一個步驟後，接著您可以手動執行 `VMAccessForLinux` 擴充功能來重設您的 SSH 使用者認證：

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### 重新部署 VM
您可以將 VM 重新部署到 Azure 中的另一個節點，這可能會修正任何的基礎網路問題。若要使用 Azure 入口網站重新部署 VM，選取 [瀏覽] > [虛擬機器] > 您的 Linux 虛擬機器 > [重新部署]。如需如何執行這項作業的資訊，請參閱[將虛擬機器重新部署至新的 Azure 節點](virtual-machines-windows-redeploy-to-new-node.md)。您目前無法使用 Azure CLI 重新部署 VM。

> [AZURE.NOTE] 請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器相關聯的動態 IP 位址。


## 使用傳統部署模型建立的 VM

請嘗試下列步驟，來解決使用傳統部署模型所建立之 VM 中最常見的 SSH 連線失敗。在每個步驟完成後，請嘗試重新連接至 VM。

- 透過 [Azure 入口網站](https://portal.azure.com)，重設遠端存取。在 Azure 入口網站中，依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [重設遠端]。

- 重新啟動 VM。在 [Azure 入口網站](https://portal.azure.com)中，依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [重新啟動]。

	-或-

	在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [虛擬機器] > [執行個體] > [重新啟動]。

- 將 VM 重新部署到新的 Azure 節點。如需如何執行這項作業的資訊，請參閱[將虛擬機器重新部署至新的 Azure 節點](virtual-machines-windows-redeploy-to-new-node.md)。

	請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器相關聯的動態 IP 位址。

- 請遵循[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md) 中的指示進行，以：
	- 重設密碼或 SSH 金鑰。
	- 建立新的 _sudo_ 使用者帳戶。
	- 重設 SSH 組態。

- 檢查 VM 的資源健康狀態是否有任何平台問題。<br> 依序選取 [瀏覽] > [虛擬機器 (傳統)] > 您的 Linux 虛擬機器 > [設定] > [檢查健康情況]。


## 其他資源

- 如果在遵循這些步驟之後仍無法以 SSH 連線到您的 VM，您可以遵循[更詳細的疑難排解步驟](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)檢閱其他的網路組態和步驟以解決您的問題。

- 如需疑難排解應用程式存取的詳細資訊，請參閱[針對存取在 Azure 虛擬機器上執行的應用程式進行疑難排解](virtual-machines-linux-troubleshoot-app-connection.md)

- 如需針對使用傳統部署模型所建立之虛擬機器進行疑難排解的詳細資訊，請參閱[如何為 Linux 虛擬機器重設密碼或 SSH](virtual-machines-linux-classic-reset-access.md)。

<!---HONumber=AcomDC_0713_2016-->