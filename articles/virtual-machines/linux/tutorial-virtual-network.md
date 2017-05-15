---
title: "Azure 虛擬網路和 Linux 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure CLI 來管理 Azure 虛擬網路和 Linux 虛擬機器"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 1ac628b606a198bb437c02d00467d48462c34334
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>使用 Azure CLI 來管理 Azure 虛擬網路和 Linux 虛擬機器

在本教學課程中，您將了解如何在虛擬網路中建立多部虛擬機器 (VM)，並設定這些機器間的網路連線。 完成時，即可從網際網路透過連接埠 22 (適用於 SSH 連線) 或連接埠 80 (適用於 HTTP 連線) 存取「前端」VM。 具有 MySQL 資料庫的「後端」VM 會被隔離，而只有從前端 VM 透過連接埠 3306 才能存取它。

您可以使用最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli) 來完成本教學課程中的步驟。

## <a name="create-vm-and-vnet"></a>建立 VM 和 VNet

Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 在 VNet 內，您可以找到子網路、可供連線到這些子網路的規則，以及從 VM 到子網路的連線。 Azure CLI 可讓您輕鬆建立所需的一切網路相關資源來支援對 VM 的存取。 

您必須先使用 az group create 來建立資源群組，才能建立任何其他 Azure 資源。 下列範例會在 westus 位置建立名為 myRGNetwork 的資源群組：

```azurecli
az group create --name myRGNetwork --location westus
```

當您使用 Azure CLI 來建立虛擬機器時，也會同時建立它所需的網路資源。 請使用 [az vm create](https://docs.microsoft.com/cli/azure/vm#create) 來建立 myFrontendVM 及支援它的網路資源：

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

建立 VM 之後，請記下公用 IP 位址。 在本教學課程稍後的步驟中，將會用到此位址：

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

以下是所建立的網路資源：

- **myFrontendVMNSG** – 可保護 myFrontendVM 之連入流量的網路安全性群組。
- **myVMPublicIP** –可啟用對 myFrontendVM 網際網路存取的公用 IP 位址。
- **myVMVMNic** – 為 myFrontendVM 提供網路連線能力的虛擬網路介面。
- **myVMVNET** – myFrontendVM 所連接的虛擬網路。

## <a name="install-web-server"></a>安裝 Web 伺服器

建立與 myFrontendVM 的 SSH 連線。 以 VM 的公用 IP 位址取代範例 IP 位址：

```bash
ssh 40.68.254.142
```

執行下列命令以安裝 NGINX：

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

關閉 SSH 工作階段：

```bash
exit
```

## <a name="manage-internet-traffic"></a>管理網際網路流量

網路安全性群組 (NSG) 包含安全性規則清單，這些規則可允許或拒絕傳送到已連線至 VNet 之資源的網路流量。 NSG 可與子網路或連接到 VM 的個別 NIC 建立關聯。 您可以使用 NSG 規則來透過連接埠開啟或關閉對 VM 的存取。 建立 myFrontendVM 時，會自動開啟輸入連接埠 22 以供 SSH 連線使用。

使用 [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port)在 myFrontendVM 上開啟連接埠 80：

```azurecli
az vm open-port --resource-group myRGNetwork --name myFrontendVM --port 80
```

現在您可以瀏覽至 VM 的公用 IP 位址，以查看 NGINX 網站。

![預設 NGINX 網站](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>管理內部網路流量

您也可以使用 NSG 來設定 VM 的內部通訊。 在本節中，您將了解如何在網路中建立額外的子網路並將 NSG 指派給子網路，以允許透過連接埠 3306 從 myFrontendVM 連線到 myBackendVM。 此子網路會接著在 VM 建立後會指派給 VM。

使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create)來新增一個名為 myBackendNSG 的網路安全性群組。 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

設定連接埠來讓 myFrontendVM 和 myBackendVM 在 VNet 中彼此互相通訊。 使用 [az network rule create](/cli/azure/network/rule#create) 新增 NSG 規則，僅允許流量從 myVMSubnet 傳送至 myBackendSubnet：

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>新增後端子網路

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。 NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

使用 [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) 將 myBackEndSubnet 新增至 myFrontendVMVNet：

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>建立後端 VM

藉由 [az vm create](/cli/azure/vm#create) 使用 myBackendSubnet 來建立 myBackendVM：

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>安裝資料庫

針對本教學課程，您需從開發 VM 將私密金鑰複製到 myFrontendVM。 在生產環境中，建議您建立要在 VM 上使用的特定金鑰，而不要在建立 VM 時使用 --generate-ssh-keys。 

後端 VM 不應該可供公開存取。 在本節中，您將了解如何使用 SSH 來登入 myFrontendVM，然後使用 SSH 從 myFrontendVM登入 myBackendVM。

以 myFrontendVM 的公用 IP 位址取代範例 IP 位址：

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

建立與 myFrontendVM 的 SSH 連線。 以 myFrontendVM 的公用 IP 位址取代範例 IP 位址：

```bash
ssh 40.68.254.142
```

從 myFrontendVM 與 myBackendVM 連線：

```bash
ssh myBackendVM
```

執行下列命令來安裝 MySQL：

```bash
sudo apt-get -y install mysql-server
```

依照指示來設定 MySQL。

關閉 SSH 工作階段：

```bash
exit
```

安裝 MySQL 是為了示範如何將應用程式安裝在 myBackendVM上，在本教學課程中並未使用它。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立和保護虛擬機器相關的 Azure 網路。 接著前進到下一個教學課程，了解使用 Azure 資訊安全中心監視 VM 安全性。

[管理虛擬機器安全性](./tutorial-azure-security.md)
