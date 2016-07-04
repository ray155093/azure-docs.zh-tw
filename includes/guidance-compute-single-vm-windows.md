本文概述一組已經實證的做法，以便在 Azure 上執行 Windows 虛擬機器 (VM)，並注意延展性、可用性、管理性和安全性。

> [AZURE.NOTE] Azure 有兩個不同的部署模型：[資源管理員][resource-manager-overview]和傳統。本文使用 Microsoft 建議用於新部署的資源管理員。

我們不建議將單一 VM 用於生產工作負載，因為 Azure 上的單一 VM 沒有正常運作 SLA。若要取得 SLA，您必須在可用性設定組中部署多個 VM。如需詳細資訊，請參閱[在 Azure 上執行多個 Windows VM][multi-vm]。

## 架構圖表

在 Azure 中佈建 VM 牽涉的移動組建比 VM 本身更多。有計算、網路及儲存體元素。

![IaaS：單一 VM](./media/guidance-blueprints/compute-single-vm.png)

- **資源群組。** [_資源群組_][resource-manager-overview]是保存相關資源的容器。建立資源群組以保存此 VM 的資源。

- **VM**。您可以從已發佈的映像清單，或從您上傳至 Azure Blob 儲存體的 VHD 檔案佈建 VM。

- **作業系統磁碟。** 作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD。這表示即使主機電腦關閉，它仍持續存在。

- **暫存磁碟。** VM 是使用暫存磁碟 (Windows 上的 `D:` 磁碟機) 來建立。此磁碟會儲存在主機電腦的實體磁碟機上。不會儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中消失。僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。

- **資料磁碟。** [資料磁碟][data-disk]是用於應用程式資料的持續性 VHD。資料磁碟會儲存在 Azure 儲存體中，例如作業系統磁碟。

- **虛擬網路 (VNet) 和子網路。** 在 Azure 中的每個 VM 都會部署到虛擬網路 (VNet) 中，而虛擬網路會進一步分成數個子網路。

- **公用 IP 位址。** 必須要有公用 IP 位址才能與 VM 進行通訊，例如透過遠端桌面 (RDP)。

- **網路介面 (NIC)**。NIC 可讓 VM 與虛擬網路通訊。

- **網路安全性群組 (NSG)**。[NSG][nsg] 是用來允許/拒絕對子網路的網路流量。您可以將 NSG 與獨立的 NIC 或子網路關聯。如果您將它與子網路關聯，該 NSG 規則會套用至子網路中的所有 VM。
 
- **診斷。** 診斷記錄對於管理和針對 VM 進行疑難排解十分重要。

## 建議

### VM 建議

- 我們建議使用 DS 和 GS 系列，除非您有高效能運算等特殊工作負載。如需詳細資訊，請參閱[虛擬機器的大小][virtual-machine-sizes]。將現有的工作負載移至 Azure 時，從最符合您內部部署伺服器的 VM 大小開始。然後根據 CPU、記憶體和磁碟 IOPS 測量您的實際工作負載效能，並視需要調整大小。此外，如果您需要多個 NIC，請留意每個大小的 NIC 限制。  

- 當您佈建 VM 和其他資源時，您必須指定一個位置。一般而言，選擇最接近您的內部使用者或客戶的位置。不過，並非所有 VM 大小在所有位置都可用。如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。若要列出指定位置可用的 VM 大小，請執行下列 Azure CLI 命令：

    ```
    azure vm sizes --location <location>
    ```

- 如需選擇已發佈 VM 映像的相關資訊，請參閱[瀏覽並選取 Azure 虛擬機器映像][select-vm-image]。

### 磁碟和儲存體建議

- 為了達到最佳的磁碟 I/O 效能，我們建議使用[進階儲存體][premium-storage]，這會將資料儲存在固態硬碟 (SSD)。成本是依佈建的磁碟大小而定。IOPS 和輸送量 (亦即，資料傳輸速率) 也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。 

- 新增一或多個資料磁碟。當您建立新的 VHD 時，它仍未格式化。登入 VM 來格式化磁碟。

- 如果您有大量的資料磁碟，請注意儲存體帳戶的總 I/O 限制。如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

- 為了達到最佳效能，請建立個別的儲存體帳戶來保存診斷記錄。標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。

- 若情況允許，請將應用程式安裝在資料磁碟，不要安裝在作業系統磁碟。不過，某些舊版的應用程式可能需要將元件安裝在 C: 磁碟機。在此情況下，您可以使用 PowerShell [重新調整作業系統磁碟大小][resize-os-disk]。

### 網路建議

- 此公用 IP 位址可以是動態或靜態。預設值為動態。

    - 如果您需要不會變更的固定 IP 位址，請保留[靜態 IP 位址][static-ip] &mdash; 例如，如果您需要在 DNS 中建立 A 記錄，或需要將 IP 位址列入白名單。

    - 您也可以建立 IP 位址的完整網域名稱 (FQDN)。然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 紀錄][cname-record]。如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。

- 所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。預設的規則不能刪除，但其他規則可以覆寫它們。若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。

- 若要啟用 RDP，請新增一個 NSG 規則，以允許將輸入流量輸入至 TCP 連接埠 3389。

## 延展性考量

- 您可以藉由[變更 VM 大小][vm-resize]來相應放大或相應縮小 VM。 

- 若要水平相應放大，請將兩個以上的 VM 置於附載平衡器後方的可用性設定組中。如需詳細資訊，請參閱[在 Azure 上執行多個 Windows VM][multi-vm]。

## 可用性考量

- 如上所述，單一 VM 沒有 SLA。若要取得 SLA，您必須在可用性設定組中部署多個 VM。

- 您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。

- VHD 是由 [Azure 儲存體][azure-storage]支援，而 Azure 儲存體會為了維持持久性和可用性而進行複寫。

- 為了防止在正常作業期間意外遺失資料 (例如，由於使用者錯誤)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具實作時間點備份。

## 管理性考量

- **資源群組。** 將關係密切且共用相同生命週期的資源置於同一個[資源群組][resource-manager-overview]。資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組列出帳單成本。您也可以刪除整組資源，這對於測試部署非常有用。為資源提供有意義的名稱。這樣能更容易找到特定資源及了解其角色。請參閱 [Azure 資源的建議命名慣例][naming conventions]。

- **VM 診斷。** 啟用監視和診斷，包括基本健康情況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。使用 [Azure 記錄檔收集][log-collector]延伸模組來收集 Azure 平台記錄檔並上傳至 Azure 儲存體。

    下列 CLI 命令會啟用診斷：

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **停止 VM。** Azure 會區分「已停止」和「已取消配置」狀態。當 VM 狀態為「已停止」時，您需要付費。VM 解除配置後，則不需付費

    使用下列 CLI 命令來解除配置 VM：

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Azure 入口網站中的 [停止] 按鈕也會取消配置 VM。不過，如果您在登入時透過作業系統來關閉，VM 會停止但不會取消配置，因此您仍需付費。

- **刪除 VM。** 如果您刪除 VM，並不會刪除 VHD。這表示您可以放心地刪除 VM，而不會遺失任何資料。不過，您仍需支付儲存體費用。若要刪除 VHD，請將檔案從 [Blob 儲存體][blob-storage]刪除。

  若要防止意外刪除，請使用[資源鎖定][resource-lock]來鎖定整個資源群組或鎖定個別資源 (例如 VM)。



## 安全性考量

- 使用 [Azure 資訊安全中心][security-center]，可集中檢閱 Azure 資源的安全性狀態。資訊安全中心會監視潛在的安全性問題，例如系統更新、反惡意程式碼，並提供全面性的部署安全性健康狀態。 

    - 資訊安全中心是依每個 Azure 訂用帳戶設定。按照[使用安全中心]所述來啟用安全資料收集。
    - 一旦啟用資料收集，安全性中心就會自動掃描任何該訂用帳戶建立的 VM。

- **修補程式管理。** 如果啟用，安全性中心會檢查是否遺失安全性或重要更新。使用 VM 上的[群組原則設定][group-policy]來啟用自動系統更新。

- **反惡意程式碼。** 如果啟用，安全性中心會檢查是已安裝反惡意程式碼軟體。您也可以使用安全性中心來從 Azure 入口網站內安裝反惡意程式碼軟體。

- 使用[角色型存取控制][rbac] (RBAC) 來控制對您所部署的 Azure 資源的存取。RBAC 可讓您指派授權角色給您 DevOps 小組的成員。例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。某些角色專門用於特定的 Azure 資源類型。例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立新的 VM 等等。對此參考架構可能有用的其他[內建 RBAC 角色][rbac-roles]包括 [DevTest Lab 使用者][rbac-devtest]和[網路參與者][rbac-network]。使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。

    > [AZURE.NOTE] RBAC 不會限制使用者登入 VM 可執行的動作。這些權限是由客體 OS上的帳戶類型來決定。

- 若要重設本機系統管理員密碼，請執行 `vm reset-access` Azure CLI 命令。

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- 使用[稽核記錄檔][audit-logs]中的佈建動作和其他 VM 事件。

- 如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。

## 範例部署指令碼

下列 Windows 批次指令碼會執行 [Azure CLI][azure-cli] 命令，以部署單一 VM 執行個體和相關的網路和儲存體資源 (如前一個圖表所示)。

該指令碼會使用 [Azure 資源的建議命名慣例][naming conventions]中所述的命名慣例。

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## 後續步驟

為了能適用[虛擬機器 SLA][vm-sla]，您必須在「可用性設定組」中部署兩部以上的執行個體。如需詳細資訊，請參閱[在 Azure 上執行多個 Windows VM][multi-vm]。

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/zh-TW/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/zh-TW/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/zh-TW/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/zh-TW/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/zh-TW/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/zh-TW/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/zh-TW/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/zh-TW/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/zh-TW/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[使用安全中心]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/zh-TW/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->