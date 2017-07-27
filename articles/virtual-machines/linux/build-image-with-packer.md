---
title: "如何使用 Packer 來建立 Linux Azure VM 映像 | Microsoft Docs"
description: "了解如何在 Azure 中使用 Packer 建立 Linux 虛擬機器的映像"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>如何在 Azure 中使用 Packer 來建立 Linux 虛擬機器映像
Azure 中的每個虛擬機器 (VM) 都是透過映像所建立，而映像則會定義 Linux 散發套件和作業系統版本。 映像中可包含預先安裝的應用程式與組態。 Azure Marketplace 提供了許多第一方和第三方映像，這些映像適用於最常見的散發套件和應用程式環境，而您也可以建立自己自訂的映像，以符合您的需求。 本文詳述如何使用開放原始碼工具 [Packer](https://www.packer.io/)，在 Azure 中定義並建置自訂映像。


## <a name="create-supporting-azure-resources"></a>建立支援用 Azure 資源
建置程序進行期間，Packer 會在建置來源 VM 時建立暫存的 Azure 資源。 若要擷取該來源 VM 來作為映像，您必須定義資源群組和儲存體帳戶。 Packer 建置程序所產生的輸出會儲存在此資源群組和儲存體帳戶中。

首先，使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
az group create -n myResourceGroup -l eastus
```

接下來，使用 [az storage account create](/cli/azure/storage/account#create) 建立儲存體帳戶。 儲存體帳戶名稱必須是唯一的，長度介於 3 到 24 個字元，而且只能包含數字和小寫字母。 下列範例會建立名為 mystorageaccount 的儲存體帳戶：

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>建立 Azure 認證
Packer 會使用服務主體來向 Azure 驗證。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 Packer 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 建立服務主體，並將 Packer 所需的認證輸出：

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

上述命令的輸出範例如下所示：

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

若要向 Azure 驗證，您也需要使用 [az account show](/cli/azure/account#show) 取得 Azure 訂用帳戶識別碼：

```azurecli
az account show --query [id] --output tsv
```

您將在下一個步驟中使用這兩個命令的輸出。


## <a name="define-packer-template"></a>定義 Packer 範本
若要建置映像，您可以將範本建立為 JSON 檔案。 在此範本中，您必須定義產生器和佈建程式，由它們執行實際的建置程序。 Packer 具有[適用於 Azure 的佈建程式](https://www.packer.io/docs/builders/azure.html)，以供您定義 Azure 資源，例如上述步驟所建立的服務主體認證。

建立名為 ubuntu.json 的檔案，並貼入下列內容。 針對下列參數輸入您自己的值︰

| 參數       | 取得位置 |
|-----------------|----------------------------------------------------|
| client_id      | `az ad sp` 建立命令所產生之輸出的第一行 - appId |
| client_secret  | `az ad sp` 建立命令所產生之輸出的第二行 - password |
| tenant_id      | `az ad sp` 建立命令所產生之輸出的第三行 - tenant |
| subscription_id | `az account show` 命令所產生的輸出 |
| *storage_account* | 您在 `az storage account create` 中指定的名稱 |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

此範本會建置 Ubuntu 16.04 LTS 映像、安裝 NGINX，然後取消佈建 VM。

> [!NOTE]
> 如果您擴充此範本來佈建使用者認證，請將取消佈建 Azure 代理程式的佈建程式命令調整為讀取 `-deprovision` 而不是 `deprovision+user`。
> `+user` 旗標會從來源 VM 中移除所有使用者帳戶。


## <a name="build-packer-image"></a>建置 Packer 映像
如果您尚未在本機電腦上安裝 Packer，請[遵循 Packer 安裝指示](https://www.packer.io/docs/install/index.html)。

請指定 Packer 範本檔案來建置映像，如下所示：

```bash
./packer build ubuntu.json
```

上述命令的輸出範例如下所示：

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>建立 Azure 映像
Packer 建置程序所產生的輸出是所指定儲存體帳戶中的虛擬硬碟 (VHD)。 使用 [az image create](/cli/azure/image#create) 從這個 VHD 建立 Azure 映像，並指定在 Packer 建置輸出結尾所記載的 `OSDiskUri` 路徑。 下列範例會建立名為 `myImage` 的映像：

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

此映像可用來在您的 Azure 訂用帳戶中建立 VM。 您不一定要在與來源映像相同的資源群組中建立 VM。


## <a name="create-vm-from-azure-image"></a>從 Azure 映像建立 VM
您現在可以使用 [az vm create](/cli/azure/vm#create) 從您的映像建立 VM。 指定您使用 `--image` 參數所建立的映像。 下列範例會從 myImage 建立名為 myVM 的 VM，並產生 SSH 金鑰 (如果您還未擁有這些金鑰的話)︰

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

建立 VM 需要幾分鐘的時間。 在 VM 建立好之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 此位址可用來透過 Web 瀏覽器存取 NGINX 網站。

若要讓 Web 流量到達您的 VM，請使用 [az vm open-port](/cli/azure/vm#open-port) 從網際網路開啟通訊埠 80：

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>測試 VM 和 NGINX
現在，您可以開啟 Web 瀏覽器，並在網址列輸入 `http://publicIpAddress`。 提供您自己從 VM 建立程序中取得的公用 IP 位址。 預設 NGINX 網頁即會顯示，如下列範例所示：

![預設 NGINX 網站](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>後續步驟
在此範例中，您已使用 Packer 來建立已安裝 NGINX 的 VM 映像。 您可以使用這個 VM 映像以及現有的部署工作流程，來進行「將應用程式部署至使用 Ansible、Chef 或 Puppet 從映像所建立的 VM」之類的作業。

如需其他 Linux 散發套件的其他 Packer 範本範例，請參閱[這個 GitHub 存放庫](https://github.com/hashicorp/packer/tree/master/examples/azure)。
