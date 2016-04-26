<properties
   pageTitle="使用 CLI 在 Azure 上建立 Linux VM | Microsoft Azure"
   description="使用 CLI 在 Azure 上建立 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# 使用 CLI 在 Azure 上建立 Linux VM

本文示範如何使用 Azure CLI 的 `azure vm quick-create` 命令，在 Azure 上快速建立 Linux 虛擬機器。`quick-create` 命令會建立具有基本基礎結構的 VM，可讓您非常快速地建立原型或測試概念。將它視為達到 Linux bash 殼層的最快速方式。本文需要 Azure 帳戶 ([取得免費試用](https://azure.microsoft.com/pricing/free-trial/)) 和處於資源管理員模式的 [Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。

## 快速命令摘要

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## 詳細的逐步解說

## 建立 Linux VM

在下列命令中，可以使用所需的任何映像，但此範例使用 `canonical:ubuntuserver:14.04.2-LTS:latest`，快速建立 VM。(若要在 Marketplace 中尋找映像，請[搜尋映像](virtual-machines-linux-cli-ps-findimage.md)，或者您可以[上傳您自己的自訂映像](virtual-machines-linux-create-upload-generic.md))。 您會看到類似下列畫面。

在下列命令逐步解說中，請以您自己環境的值取代提示。我們使用本文的「範例」值。

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

您現在可以在預設 SSH 連接埠 22 和上面輸出中所列的公用 IP 位址上，使用 SSH 登入您的 VM。

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

`azure vm quick-create` 可快速建立 VM，因此您可以登入 bash 殼層並開始運作。使用 `vm quick-create` 並不會提供複雜環境的其他好處，因此，如果您想要自訂您的環境，可以[使用 Azure Resource Manager 範本快速建立特定的部署](virtual-machines-linux-cli-deploy-templates.md)，或者您可以[使用 Azure CLI 命令，直接為 Linux VM 建立自己的自訂環境](virtual-machines-linux-cli-deploy-templates.md)。

上述範例會建立：

- Azure 資源群組，將 VM 部署至
- 一個 Azure 儲存體帳戶，用於存放 VM 映像的 .vhd 檔案
- 一個 Azure 虛擬網路和子網路，用於連線至 VM
- 一個虛擬網路介面卡 (NIC)，用於建立 VM 與網路的關聯
- 一個公用 IP 位址和子網域首碼，用於提供網際網路位址供外部使用，然後在該環境內建立 Linux VM。

## 後續步驟

現在您已經快速建立一個用於測試或示範用途的 Linux VM。若要建立為您的基礎結構自訂的 Linux VM，您可以依照以下任何文章執行。

- [使用範本在 Azure 上建立 Linux VM](virtual-machines-linux-cli-deploy-templates.md)
- [使用範本在 Azure 上建立受 SSH 保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [使用 CLI 建立 Linux VM](virtual-machines-linux-create-cli-complete.md)

這些文章可幫您開始建置 Azure 基礎結構，以及多種專屬和開放原始碼基礎結構部署、組態和協調流程工具。

<!---HONumber=AcomDC_0420_2016-->