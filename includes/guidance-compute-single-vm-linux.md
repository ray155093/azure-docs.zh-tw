本文概述一組已經實證的做法，以便在 Azure 上執行 Linux 虛擬機器 (VM)，並注意延展性、可用性、管理性和安全性。Azure 支援執行數種受歡迎的 Linux 散發套件，包括 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 FreeBSD。如需詳細資訊，請參閱 [Azure 和 Linux][azure-linux]。

> [AZURE.NOTE] Azure 有兩個不同的部署模型：[資源管理員][resource-manager-overview]和傳統。本文使用 Microsoft 建議用於新部署的資源管理員。

我們不建議將單一 VM 用於生產工作負載，因為 Azure 上的單一 VM 沒有正常運作 SLA。若要取得 SLA，您必須在可用性設定組中部署多個 VM。如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。

## 架構圖表

在 Azure 中佈建 VM 牽涉的移動組建比 VM 本身更多。有計算、網路及儲存體元素。

![IaaS：單一 VM](./media/guidance-blueprints/compute-single-vm.png)

- **資源群組。** [_資源群組_][resource-manager-overview]是保存相關資源的容器。建立資源群組以保存此 VM 的資源。

- **VM**。您可以從已發佈的映像清單，或從您上傳至 Azure Blob 儲存體的 VHD 檔案佈建 VM。

- **作業系統磁碟。** 作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD。這表示即使主機電腦關閉，它仍持續存在。作業系統磁碟為 `/dev/sda1`

- **暫存磁碟。** VM 是使用暫存磁碟來建立。此磁碟會儲存在主機電腦的實體磁碟機上。不會儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中消失。僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。暫存磁碟為 `/dev/sdb1` 且掛接於 `/mnt/resource` 或 `/mnt`。

- **資料磁碟。** [資料磁碟][data-disk]是用於應用程式資料的持續性 VHD。資料磁碟會儲存在 Azure 儲存體中，例如作業系統磁碟。

- **虛擬網路 (VNet) 和子網路。** 在 Azure 中的每個 VM 都會部署到虛擬網路 (VNet) 中，而虛擬網路會進一步分成數個子網路。

- **公用 IP 位址。** 必須要有公用 IP 位址才能與 VM 進行通訊，例如透過 ssh。

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

- 新增一或多個資料磁碟。當您建立新的 VHD 時，它仍未格式化。登入 VM 來格式化磁碟。資料磁碟會顯示為 `/dev/sdc`、`/dev/sdd`，依此類推。您可以執行 `lsblk` 以列出區塊裝置，包括磁碟。若要使用資料磁碟，請建立新的磁碟分割和檔案系統，並掛接該磁碟。例如：

    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```

- 如果您有大量的資料磁碟，請注意儲存體帳戶的總 I/O 限制。如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

- 當您新增資料磁碟時，會指派邏輯單元編號 (LUN) 識別碼給磁碟。或者，您可以指定 LUN 識別碼 &mdash;例如，如果您要更換磁碟，而且想要保留相同的 LUN 識別碼，或者您有會查看特定 LUN 識別碼的 App。不過，請記住，每個磁碟的 LUN 識別碼不能重複。

- 您可能會想變更 I/O 排程器以最佳化 SSD (用於進階儲存體) 的效能。一般建議是使用 SSD 的 NOOP 排程器，但您應該使用 [iostat] 等工具，來監視特定工作負載的磁碟 I/O 效能。

- 為了達到最佳效能，請建立個別的儲存體帳戶來保存診斷記錄。標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。


### 網路建議

- 此公用 IP 位址可以是動態或靜態。預設值為動態。

    - 如果您需要不會變更的固定 IP 位址，請保留[靜態 IP 位址][static-ip] &mdash; 例如，如果您需要在 DNS 中建立 A 記錄，或需要將 IP 位址列入白名單。

    - 您也可以建立 IP 位址的完整網域名稱 (FQDN)。然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 紀錄][cname-record]。如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。

- 所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。預設的規則不能刪除，但其他規則可以覆寫它們。若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。

- 若要啟用 ssh，請新增一個規則，以允許將輸入流量輸入至 TCP 連接埠 22。

## 延展性考量

- 您可以藉由[變更 VM 大小][vm-resize]來相應放大或相應縮小 VM。 

- 若要水平相應放大，請將兩個以上的 VM 置於附載平衡器後方的可用性設定組中。如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。

## 可用性考量

- 如上所述，單一 VM 沒有 SLA。若要取得 SLA，您必須在可用性設定組中部署多個 VM。

- 您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。

- VHD 是由 [Azure 儲存體][azure-storage]支援，而 Azure 儲存體會為了維持持久性和可用性而進行複寫。

- 為了防止在正常作業期間意外遺失資料 (例如，由於使用者錯誤)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具實作時間點備份。

## 管理性考量

- **資源群組。** 將關係密切且共用相同生命週期的資源置於同一個[資源群組][resource-manager-overview]。資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組列出帳單成本。您也可以刪除整組資源，這對於測試部署非常有用。為資源提供有意義的名稱。這樣能更容易找到特定資源及了解其角色。請參閱 [Azure 資源的建議命名慣例][naming conventions]。

- **ssh**。在您建立 VM 之前，先產生 2048 位元 RSA 公開-私密金鑰組。建立 VM 的時候使用公開金鑰檔案。如需詳細資訊，請參閱[如何透過 Linux 和 Mac 在 Azure 使用 SSH][ssh-linux]。

- **VM 診斷。** 啟用監視和診斷，包括基本健康情況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。

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

- 藉由使用 [OSPatching] VM 擴充，來自動化作業系統更新。請在您佈建 VM 的時候安裝此擴充。您可以指定安裝修補程式的頻率，以及安裝後是否要重新開機。

- 使用[角色型存取控制][rbac] (RBAC) 來控制對您所部署的 Azure 資源的存取。RBAC 可讓您指派授權角色給您 DevOps 小組的成員。例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。某些角色專門用於特定的 Azure 資源類型。例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立新的 VM 等等。對此參考架構可能有用的其他[內建 RBAC 角色][rbac-roles]包括 [DevTest Lab 使用者][rbac-devtest]和[網路參與者][rbac-network]。使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。

    > [AZURE.NOTE] RBAC 不會限制使用者登入 VM 可執行的動作。這些權限是由客體 OS上的帳戶類型來決定。

- 使用[稽核記錄檔][audit-logs]中的佈建動作和其他 VM 事件。

- 如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。

## 範例部署指令碼

下列 Bash 批次指令碼會執行 [Azure CLI][azure-cli] 命令，以部署單一 VM 執行個體和相關的網路和儲存體資源 (如前一個圖表所示)。

該指令碼會使用 [Azure 資源的建議命名慣例][naming conventions]中所述的命名慣例。

執行指令碼：

1. 產生一組 2048 位元 RSA 驗證金鑰。

        ssh-keygen -t rsa -b 2048

2. 以參數方式將您的 Azure 訂用帳戶識別碼和公開金鑰檔案的名稱傳遞給指令碼。

        ./azurecli-single-vm-sample.sh <subscription ID> ~/.ssh/id_rsa.pub

3. 指令碼完成之後，使用 ssh 登入 VM。使用私密金鑰來驗證。

        ssh testuser@<app>-vm1.<location>.cloudapp.azure.com -i ~/.ssh/id_rsa

    其中的 `<app>` 是 `APP_NAME` 指令碼變數的值，而 `<location>` 是 `LOCATION` 變數的值。

```bat
#!/bin/bash

############################################################################
#script for generating infrastructure for single VM running linux          #
# of user choice. It creates azure resource group, storage account for VM  #
# vnet, subnets for VM, and NSG rule                                       #
# tags for main variables used                                             #
# ScriptCommandParameters                                                  #
# ScriptVars                                                               #
############################################################################

############################################################################
# User defined functions for single VM script                              #
# errhandle : handles errors via trap if any exception happens             #
# in the cli execution or if the user interrupts with CTRL+C               #
# allowing for fast interruption                                           #
############################################################################

# error handling or interruption via ctrl-c.
# line number and error code of executed command is passed to errhandle function

trap 'errhandle $LINENO $?' SIGINT ERR

errhandle()
{
  echo "Error or Interruption at line ${1} exit code ${2} "
  exit ${2}
}

###############################################################################
############################## End of user defined functions ##################
###############################################################################

# 2 paramaters are expected
# public key file needs to be generated using ssh-keygen

if [ $# -ne 2  ]
then
	echo  "Usage:  ${0}  subscription-id public-ssh-key-file"
	exit
fi

if [ ! -f $2  ]
then
	echo "Public Key file ${2} does not exist. please generate it"
	echo "ssh-keygen -t rsa -b 2048"
	exit
fi

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
# ScriptCommandParameters
SUBSCRIPTION=$1
PUBLICKEYFILE=$2

# ScriptVars
LOCATION=eastus2
APP_NAME=app1
ENVIRONMENT=dev
USERNAME=testuser
VM_NAME="${APP_NAME}-vm1"
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
IP_NAME="${APP_NAME}-pip"
NIC_NAME="${VM_NAME}-1nic"
NSG_NAME="${APP_NAME}-nsg"
SUBNET_NAME="${APP_NAME}-subnet"
VNET_NAME="${APP_NAME}-vnet"
VHD_STORAGE="${VM_NAME//-}st1"
DIAGNOSTICS_STORAGE="${VM_NAME//-}diag"

# Use the following command to get the list of URNs for RHEL, UBUNTU, and OPENSUSE:
# RHEL
# azure vm image list $LOCATION  redhat RHEL 7.2
# UBUNTU
# azure vm image list $LOCATION canonical ubuntuserver 14.04.3-LTS
# SUSE
# azure vm image $LOCATION  suse opensuse 13.2

LINUX_BASE_IMAGE=redhat:rhel:7.2:7.2.20160302

# For a list of VM sizes see: 
#   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
# To see the VM sizes available in a region:
# 	azure vm sizes --location <location>
VM_SIZE=Standard_DS1

# Set up the postfix variables attached to most CLI commands

POSTFIX="--resource-group ${RESOURCE_GROUP} --subscription ${SUBSCRIPTION}"

azure config mode arm

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#Create resources

#Create the enclosing resource group
azure group create --name $RESOURCE_GROUP --location $LOCATION --subscription $SUBSCRIPTION

# Create the VNet
azure network vnet create --address-prefixes 172.17.0.0/16 --name $VNET_NAME \
--location $LOCATION $POSTFIX

#Create the network security group
azure network nsg create --name $NSG_NAME --location $LOCATION $POSTFIX

#Create the subnet
azure network vnet subnet create --vnet-name $VNET_NAME --address-prefix  "172.17.0.0/24" \
--name $SUBNET_NAME --network-security-group-name $NSG_NAME $POSTFIX

#Create the public IP address (dynamic)
azure network public-ip create --name $IP_NAME --domain-name-label $VM_NAME \
--location $LOCATION $POSTFIX

#Create the NIC
azure network nic create --public-ip-name $IP_NAME --subnet-name $SUBNET_NAME \
--subnet-vnet-name $VNET_NAME --name $NIC_NAME --location $LOCATION $POSTFIX

#Create the storage account for the OS VHD
azure storage account create --type PLRS --location $LOCATION $POSTFIX $VHD_STORAGE

#Create the storage account for diagnostics logs
azure storage account create --type LRS --location $LOCATION $POSTFIX $DIAGNOSTICS_STORAGE

#Create the VM
azure vm create --name $VM_NAME --os-type Linux --image-urn  $LINUX_BASE_IMAGE \
--vm-size $VM_SIZE --vnet-subnet-name $SUBNET_NAME --vnet-name $VNET_NAME \
--nic-name $NIC_NAME --storage-account-name $VHD_STORAGE \
--os-disk-vhd "${VM_NAME}-osdisk.vhd" --admin-username $USERNAME \
--ssh-publickey-file $PUBLICKEYFILE --boot-diagnostics-storage-uri \
"https://${DIAGNOSTICS_STORAGE}.blob.core.windows.net/" --location $LOCATION $POSTFIX

#Attach a data disk
azure vm disk attach-new --vm-name $VM_NAME --size-in-gb 128 --vhd-name data1.vhd \
--storage-account-name $VHD_STORAGE $POSTFIX

#Allow SSH
azure network nsg rule create --nsg-name $NSG_NAME --direction Inbound --protocol Tcp \
--destination-port-range 22  --source-port-range "*"  --priority 100 --access Allow \
SSHAllow $POSTFIX

#Install patching extension
PATCH_CONFIG='{"rebootAfterPatch":"RebootIfNeed","startTime":"3:00","dayOfWeek":"Sunday","category":"ImportantAndRecommended"}'
azure vm extension set --name OSPatchingForLinux --publisher-name Microsoft.OSTCExtensions \
--public-config $PATCH_CONFIG --vm-name $VM_NAME --version 2.0 $POSTFIX
```

## 後續步驟

為了能適用[虛擬機器 SLA][vm-sla]，您必須在「可用性設定組」中部署兩部以上的執行個體。如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/zh-TW/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/zh-TW/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/zh-TW/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/zh-TW/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/zh-TW/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/zh-TW/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/zh-TW/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->