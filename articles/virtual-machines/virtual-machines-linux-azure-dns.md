<properties 
   pageTitle="Azure 中 Linux VM 的 DNS 名稱解析選項"
   description="Azure IaaS 中 Linux VM 的名稱解析案例 (包括提供的 DNS 服務)：混合式外部 DNS 和自備 DNS 伺服器。"
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/11/2016"
   ms.author="rclaus" />

# Azure 中 Linux VM 的 DNS 名稱解析選項

Azure 預設會提供單一虛擬網路內所含之所有 VM 的 DNS 名稱解析。在 Azure 託管 VM 上設定專屬 DNS 服務，即可實作專屬 DNS 名稱解析方案。下列案例應該可協助您選擇哪一種較適合您的特定情況。

- [Azure 提供的名稱解析](#azure-provided-name-resolution)

- [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)

您使用的名稱解析類型取決於 VM 和角色執行個體如何彼此通訊。

**下表說明各種案例和對應的名稱解析方案：**

| **案例** | **解決方案** | **尾碼** |
|--------------|--------------|----------|
| 位於相同虛擬網路中的角色執行個體或 VM 之間的名稱解析 | [Azure 提供的名稱解析](#azure-provided-name-resolution)| 主機名稱或 FQDN |
| 位於不同虛擬網路中的角色執行個體或 VM 之間的名稱解析 | 客戶管理的 DNS 伺服器將 VNET 之間的查詢轉送供 Azure (DNS Proxy) 解析。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)。| 僅 FQDN |
| 解析 Azure 中角色執行個體或 VM 的內部部署電腦及伺服器名稱 | 客戶管理的 DNS 伺服器 (例如內部部署的網域控制站、本機唯讀網域控制站或使用區域傳輸同步的次要 DNS)。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)。|僅 FQDN |
| 從內部部署電腦解析 Azure 主機名稱 | 將查詢轉送到所對應 vnet 中客戶管理的 DNS Proxy 伺服器，Proxy 伺服器將查詢轉送給 Azure 進行解析。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)。| 僅 FQDN |
| 內部 IP 的反向 DNS | [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) | n/a |

## Azure 提供的名稱解析

除了公用 DNS 名稱的解析之外，Azure 也提供位於相同虛擬網路內的 VM 和角色執行個體的內部名稱解析。在 ARM 型虛擬網路中，DNS 尾碼在虛擬網路之間是一致的，因此不需要 FQDN，而 DNS 名稱則可以指派給 NIC 和虛擬機器。雖然 Azure 提供的名稱解析不需要任何設定，但它不適用於所有部署案例，如上表所示。

### 功能和注意事項

**功能：**

- 方便使用：不需要設定，就能使用 Azure 提供的名稱解析。

- Azure 提供的名稱解析服務都高度可用，可省去您建立和管理 DNS 伺服器叢集的需求。

- 可與您自己的 DNS 伺服器搭配使用，以解析內部部署及 Azure 主機名稱。

- 在虛擬網路的 VM 之間提供名稱解析，不需要 FQDN。

- 您可以使用最能描述部署的主機名稱，而不是使用自動產生的名稱。

**注意事項：**

- Azure 建立的 DNS 尾碼不能修改。

- 您無法手動註冊您自己的記錄。

- 不支援 WINS 和 NetBIOS。

- 主機名稱必須是 DNS 相容 (只能使用 0-9、a-z 和 '-'，無法以 '-' 開始或結束。請參閱 RFC 3696 第 2 節)。

- 每個 VM 的 DNS 查詢流量已經過節流。這應該不會影響大部分的應用程式。如果觀察到要求節流，請確定用戶端快取已啟用。如需詳細資料，請參閱[充分利用 Azure 提供的名稱解析](#Getting-the-most-from-Azure-provided-name-resolution)。


### 充分利用 Azure 提供的名稱解析
**用戶端快取：**

並非所有的 DNS 查詢都需要透過網路傳送。用戶端快取可藉由解決本機快取的週期性 DNS 查詢，協助減少延遲以及改善網路標誌的恢復能力。DNS 記錄包含存留時間 (TTL)，可讓快取盡可能長時間儲存記錄而不會影響記錄的有效性，所以用戶端快取適用於大部分的情況。

某些 Linux 發行版預設不包含快取功能，因此我們建議您 (在已經確認沒有本機快取之後) 為每個 Linux VM 新增快取。

有許多不同 DNS 快取封裝可用，例如 dnsmasq，以下是在最常見的散發版本上安裝 dnsmasq 的步驟：

- **Ubuntu (使用 resolvconf)**：
	- 只安裝 dnsmasq 封裝 (“sudo apt-get install dnsmasq”)。
- **SUSE (使用 netconf)**：
	- 安裝 dnsmasq 封裝 (“sudo zypper install dnsmasq”) 
	- 啟用 dnsmasq 服務 (“systemctl enable dnsmasq.service”) 
	- 啟動 dnsmasq 服務 (“systemctl start dnsmasq.service”) 
	- 編輯 “/etc/sysconfig/network/config” 並將 NETCONFIG\_DNS\_FORWARDER="" 變更為 ”dnsmasq”
	- 更新 resolv.conf ("netconfig update") 來設定快取做為本機 DNS 解析程式
- **OpenLogic (使用 NetworkManager)**：
	- 安裝 dnsmasq 封裝 (“sudo yum install dnsmasq”)
	- 啟用 dnsmasq 服務 (“systemctl enable dnsmasq.service”)
	- 啟動 dnsmasq 服務 (“systemctl start dnsmasq.service”)
	- 將 “prepend domain-name-servers 127.0.0.1;” 新增至 “/etc/dhclient-eth0.conf”
	- 重新啟動網路服務 (「服務網路重新啟動」) 來設定快取做為本機 DNS 解析程式

> [AZURE.NOTE]：'dnsmasq' 封裝只是許多適用於 Linux 之 DNS 快取的其中一個。使用它之前，請檢查特定需求的適用性，而且沒有安裝其他快取。

**用戶端重試：**

DNS 主要是 UDP 通訊協定。因為 UDP 通訊協定並不保證訊息傳遞，所以重試邏輯會在 DNS 通訊協定本身處理。每個 DNS 用戶端 (作業系統) 可以展現不同的重試邏輯，根據建立者喜好設定而定：

 - Windows 作業系統會在 1 秒後重試，然後再依序隔 2、4、4 秒後重試。 
 - 預設 Linux 安裝程式會在 5 秒之後重試。建議您變更為以 1 秒的間隔重試 5 次。  

檢查 Linux VM 上目前的設定，'cat /etc/resolv.conf' 並查看 [選項] 行，例如：

	options timeout:1 attempts:5

resolv.conf 檔案通常是自動產生的，且不可編輯。新增 [選項] 行的特定步驟會因散發版本而有所不同：

- **Ubuntu** (使用 resolvconf)：
	- 將選項行新增至 '/etc/resolveconf/resolv.conf.d/head' 
	- 執行 'resolvconf -u' 以進行更新
- **SUSE** (使用 netconf)：
	- 將 'timeout:1 attempts:5' 新增至 '/etc/sysconfig/network/config' 中的 NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" 參數 
	- 執行 'netconfig update' 以進行更新
- **OpenLogic** (使用 NetworkManager)：
	- 將 'echo "options timeout:1 attempts:5"' 新增至 '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- 執行 'service network restart' 以進行更新

## 使用專屬 DNS 伺服器的名稱解析
在某些情況下，您的名稱解析需要可能會超過 Azure 所能提供的功能，例如，當您需要虛擬網路 (VNET) 之間的 DNS 解析時。為了涵蓋此案例，Azure 提供可讓您使用專屬 DNS 伺服器的能力。

虛擬網路內的 DNS 可以將要求轉送到 Azure 內的遞迴解析程式，以解析該虛擬網路內的主機名稱。例如，在 Azure 中執行的 DNS 伺服器可以回應其專屬 DNS 區域檔案的 DNS 查詢，並將所有其他查詢轉寄到 Azure。這樣可讓 VM 查看您區域檔案中的項目，以及 Azure 提供的主機名稱 (透過轉寄站)。存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。

DNS 轉送也實現 vnet 之間的 DNS 解析，並使內部部署電腦能夠解析 Azure 提供的主機名稱。為了解析 VM的主機名稱，DNS 伺服器 VM 必須位於同一個虛擬網路中，且設定為將主機名稱要求轉送到 Azure。因為每個 vnet 的 DNS 尾碼都不同，所以您可以使用條件性轉送規則來將 DNS 要求傳送到正確的 vnet 進行解析。下圖顯示兩個 vnet 及一個內部部署網路使用此方法進行 vnet 之間的 DNS 解析：

![虛擬網路之間的 DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

使用 Azure 提供的名稱解析時，會提供內部 DNS 尾碼給每部使用 DHCP 的 VM。使用您自己的名稱解析解決方案時，不會提供 VM 此尾碼，因為它會干擾其他 DNS 架構。若要透過 FQDN 參照電腦，或要設定 VM 上的尾碼，可使用 PowerShell 或 API 來決定尾碼：

-  針對 Azure Resource Management Managed VNET，透過[網路介面卡](https://msdn.microsoft.com/library/azure/mt163668.aspx)資源可以取得尾碼；或者，您可以執行命令 `azure network public-ip show <resource group> <pip name>` 來顯示您公用 IP 的詳細資料 (包括 NIC 的 FQDN)。    


如果將查詢轉送到 Azure 不符合您的需求，您會需要提供專屬的 DNS 解決方案。您的 DNS 解決方案將需要：

-  提供適當的主機名稱解析，例如透過 [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md)。請注意，如果使用 DDNS，您可能需要停用 DNS 記錄清除功能，因為 Azure 的 DHCP 租用期很長，所以 DNS 記錄可能會提前被清除。 
-  提供適當的遞迴解析來允許外部網域名稱的解析。
-  可從其服務的用戶端存取 (連接埠 53 上的 TCP 和 UDP)，且能夠存取網際網路。
-  受保護以防止來自網際網路的存取，降低外部代理程式的威脅。

> [AZURE.NOTE] 為了達到最佳效能，使用 Azure VM 做為 DNS 伺服器時，應該停用 IPv6 且 [執行個體層級公用 IP](../virtual-network/virtual-networks-instance-level-public-ip.md) 應該指派給每個 DNS 伺服器 VM。

<!---HONumber=AcomDC_0427_2016-->