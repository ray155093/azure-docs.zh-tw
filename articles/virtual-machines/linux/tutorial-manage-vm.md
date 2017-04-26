---
title: "使用 Azure CLI 管理 Linux 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure CLI 管理 Linux 虛擬機器"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: c0f22806034eef1fc5ff37d547d066f554ec0a85
ms.lasthandoff: 04/07/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>使用 Azure CLI 管理 Linux 虛擬機器

在本教學課程中，您將建立虛擬機器並執行一般管理工作，例如新增磁碟、自動化軟體安裝，以及建立虛擬機器快照集。 

若要完成本教學課程，請確定您已安裝最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="step-1--log-in-to-azure"></a>步驟 1 - 登入 Azure

首先，開啟終端機並使用 [az login](/cli/azure/#login) 命令登入您的 Azure 訂用帳戶。

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>步驟 2 - 建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group#create) 命令來建立資源群組。 

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。 在此範例中，`westeurope` 區域中會建立名為 `myResourceGroup` 的資源群組。 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>步驟 3 - 準備組態

部署虛擬機器時，**cloud-init** 可用於自動進行設定，例如安裝套件、建立檔案及執行指令碼。 在本教學課程中，有兩個項目的設定會自動化︰

- 安裝 NGINX Web 伺服器
- 在 VM 上佈建第二個磁碟

因為 **cloud-init** 設定發生在 VM 部署階段，所以必須在建立虛擬機器之前定義 **cloud-init** 設定。

建立名為 `cloud-init.txt` 的檔案並複製下列內容。 此設定會安裝 NGINX 套件，並執行命令來格式化和掛接第二個磁碟。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>步驟 4 - 建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm#create) 命令建立虛擬機器。 

建立虛擬機器時，有數個可用的選項，例如作業系統映像、磁碟大小及系統管理認證。 在此範例中，使用 `myVM` 名稱建立執行 Ubuntu 的虛擬機器。 使用 `--data-disk-sizes-gb` 引數建立 50-GB 磁碟並將其連結至 VM。 cloud-init 設定會採用 `--custom-data` 引數並將它設置於 VM 上。 最後，如果不存在 SSH 金鑰，也也機以建立。

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

一旦建立 VM，Azure CLI 會輸出下列資訊。 記下公用 IP 位址，此位址可用來存取虛擬機器。 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

即使已部署 VM，**cloud-init** 設定可能需要幾分鐘才能完成。 

## <a name="step-5--configure-firewall"></a>步驟 5 - 設定防火牆

Azure [網路安全性群組](../../virtual-network/virtual-networks-nsg.md) (NSG) 控制一或多部虛擬機器的輸入和傳出流量。 網路安全性群組規則可允許或拒絕特定連接埠或連接埠範圍上的網路流量。 這些規則也可以包含來源位址首碼，所以只有在預先定義的來源產生的流量可以與虛擬機器通訊。

在上一節中會安裝 NGINX Web 伺服器。 若沒有網路安全性群組規則可允許連接埠 80 上的輸入流量，便無法在網際網路上存取 Web 伺服器。 此步驟會引導您建立 NSG 規則，以允許連接埠 80 上的輸入連線。

### <a name="create-nsg-rule"></a>建立 NSG 規則

若要建立輸入 NSG 規則，請使用 [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) 命令。 下列範例會開啟虛擬機器的連接埠 `80`。

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

現在瀏覽至虛擬機器的公用 IP 位址。 備妥 NSG 規則，就會顯示預設 NGINX 網站。

![預設 NGINX 網站](./media/tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>步驟 6 – 建立虛擬機器快照集

建立磁碟快照集，以建立磁碟的讀取、時間點複本。 在此步驟中，建立 VM 作業系統磁碟的快照集。 透過 OS 磁碟快照集，即可將虛擬機器迅速地還原為特定狀態，或可將快照集用來建立具有相同狀態的新虛擬機器。

### <a name="create-snapshot"></a>建立快照集

建立快照集之前，需要磁碟的識別碼或名稱。 使用 [az vm show](https://docs.microsoft.com/cli/azure/vm#show) 命令取得磁碟識別碼。 在此範例中，磁碟會儲存在變數中並使用於稍後的步驟。

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

您現在有磁碟的識別碼，下列命令會建立快照集。

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>從快照集建立磁碟

此快照集可以接著轉換成磁碟，進而用於重新建立虛擬機器。

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>從快照集還原虛擬機器

若要示範虛擬機器復原，請刪除現有的虛擬機器。 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

重新建立虛擬機器時，會重複使用現有的網路介面。 這可確保保留網路安全性組態。

使用 [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list) 命令取得網路介面名稱。 此範例會將名稱放入名為 `nic` 的變數，該變數使用於下一個步驟。

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

從快照磁碟建立新的虛擬機器。

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

請記下新的公用 IP 位址，並透過網際網路瀏覽器瀏覽至此位址。 您會看到 NGINX 正在還原的虛擬機器中執行。 

### <a name="reconfigure-data-disk"></a>重新設定資料磁碟

資料磁碟現在可以重新連結至虛擬機器。 

首先使用 [az disk list](https://docs.microsoft.com/cli/azure/disk#list) 命令來尋找資料磁碟名稱。 此範例會將磁碟名稱放入名為 `datadisk` 的變數，該變數使用於下一個步驟。

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

使用 [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) 命令來連結磁碟。

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

此磁碟也必須掛接到作業系統。 若要掛接磁碟，請連線至虛擬機器並執行 `sudo mount /dev/sdc1 /datadrive`，或您慣用的磁碟掛接作業。 

## <a name="step-7--management-tasks"></a>步驟 7 – 管理工作

在虛擬機器的生命週期內，您可以執行一些管理工作，例如啟動、停止或刪除虛擬機器。 此外，您可以建立指令碼來自動執行重複或複雜的工作。 使用 Azure CLI，可以從命令列或在指令碼中執行許多常見的管理工作。 

### <a name="get-ip-address"></a>取得 IP 位址

此命令會傳回虛擬機器的私人和公用 IP 位址。  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>調整虛擬機器的大小

若要調整 Azure 虛擬機器的大小，您需要知道所選 Azure 區域中可用的大小名稱。 使用 [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes) 命令即可找到這些大小。

```azurecli
az vm list-sizes --location westeurope --output table
```

使用 [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize) 命令可以調整虛擬機器的大小。 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>停止虛擬機器

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>啟動虛擬機器

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>刪除資源群組

刪除資源群組同時會刪除其內含的所有資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟
本教學課程使用個別的 Azure 資源建立單一虛擬機器。 下一個教學課程會以這些概念為基礎，建立已負載平衡且能從維護事件中復原的高可用性應用程式。 請繼續至下一個教學課程：[在 Azure 中的 Linux 虛擬機器上建置已負載平衡的高可用性應用程式](tutorial-load-balance-nodejs.md)。

範例 – [Azure CLI 範例指令碼](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
