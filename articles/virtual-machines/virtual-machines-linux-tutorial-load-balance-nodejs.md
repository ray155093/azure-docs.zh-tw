---
title: "教學課程 - 在 Azure VM 上建立高可用性應用程式 | Microsoft Docs"
description: "了解如何使用 Azure 中的負載平衡器跨三部 Linux VM 建立高度可用且安全的應用程式"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f24cca8230e30ca3862d7e7011bcea649c6aec52
ms.lasthandoff: 03/29/2017

---

# <a name="build-a-highly-available-application-on-linux-virtual-machines-in-azure"></a>在 Azure 中的 Linux 虛擬機器上建置高度可用的應用程式
在本教學課程中，您可以建立高度可用且對維護事件具有彈性的應用程式。 應用程式會使用負載平衡器、可用性設定組和三部 Linux 虛擬機器 (VM)。 本教學課程會建置 Node.js 應用程式，但您可以在本教學課程中使用相同高可用性元件和指導方針來部署不同的應用程式架構。

## <a name="step-1---azure-prerequisites"></a>步驟 1 - Azure 必要條件
若要完成本教學課程，請開啟終端機視窗，並確定您已安裝最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果您尚未登入 Azure 訂用帳戶，請使用 [az login](/cli/azure/#login) 進行登入並遵循螢幕上的指示︰

```azurecli
az login
```

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 您必須先使用 [az group create](/cli/azure/group#create) 建立資源群組，才可以建立任何其他 Azure 資源。 下列範例會在 `westus` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>步驟 2 - 建立可用性設定組
可以跨越邏輯容錯和更新網域建立虛擬機器。 每個邏輯網域都代表基礎 Azure 資料中心硬體的一部分。 當您建立兩部或多部 VM 時，您的計算和儲存體資源會分散於這些網域。 萬一有硬體元件正在進行維護，此分佈會維護您應用程式的可用性。 可用性設定組可讓您定義這些邏輯容錯和更新網域。

使用 [az vm availability-set create](/cli/azure/vm/availability-set#create) 建立可用性設定組。 下列範例會建立名為 `myAvailabilitySet` 的可用性設定組：

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>步驟 3 - 建立負載平衡器
Azure Load Balancer 會使用負載平衡器規則，將流量分散於一組定義的 VM。 健康狀態探查會監視每部 VM 上指定的連接埠，而且只會將流量分散至操作 VM。

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址
若要存取網際網路上您的應用程式，請將公用 IP 位址指派給負載平衡器。 使用 [az network public-ip create](/cli/azure/public-ip#create) 建立公用 IP 位址。 下列範例會建立名為 `myPublicIP` 的公用 IP 位址：

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>建立負載平衡器
使用 [az network lb create](/cli/azure/network/lb#create) 建立負載平衡器。 下列範例會使用 `myPublicIP` 位址建立名為 `myLoadBalancer` 的負載平衡器：

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>建立健康狀態探查
若要讓負載平衡器監視您應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 根據預設，在 15 秒的間隔內連續發生兩次失敗後，VM 就會從負載平衡器分配中移除。 您可根據通訊協定或您應用程式的特定健康狀態檢查頁面，建立健康狀態探查。 下列範例會建立 TCP 探查，雖然您可以擴大這個範例並新增 `--path healthcheck.js`。 必須建立 `healthcheck.js` 並對負載平衡器傳回 **HTTP 200 (確定)** 回應，以將主機保留在輪替中。

使用 [az network lb probe create](/cli/azure/network/lb/probe#create) 建立健康狀態探查。 下列範例會建立名為 `myHealthProbe` 的健康狀態探查，以監視每部 VM：

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
負載平衡器規則用來定義如何將流量分散至 VM。

使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 建立負載平衡器規則。 下列範例會建立名為 `myLoadBalancerRule` 的規則、使用 `myHealthProbe` 健康狀態探查，以及平衡連接埠 `80` 上的流量︰

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>步驟 4 - 設定網路功能
每部 VM 都有一或多個虛擬網路介面卡 (NIC) 可連接至虛擬網路。 此虛擬網路受到保護，可根據定義的存取規則來篩選流量。

### <a name="create-a-virtual-network"></a>建立虛擬網路
若要提供您 VM 的網路連線能力，請使用 [az network vnet create](/cli/azure/vnet#create) 建立虛擬網路。 下列範例會建立名為 `myVnet` 的虛擬網路和名為 `mySubnet` 的子網路：

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>設定網路安全性
網路安全性群組使用規則來控制進出 VM 的流量。 您可定義這些規則，例如，允許連接埠 80 上的流量。 雖然負載平衡器會將流量分散於 VM，但網路安全性群組規則可確保只允許容許的流量。

使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

若要允許 Web 流量連到您的應用程式，請使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 建立網路安全性群組規則。 下列範例會建立名為 `myNetworkSecurityGroupRule` 的網路安全性群組規則：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>建立虛擬網路介面卡 
負載平衡器會搭配虛擬 NIC 資源運作，而不是實際的 VM。 虛擬 NIC 已連結至負載平衡器，然後連結至 VM。

使用 [az network nic create](/cli/azure/network/nic#create) 建立虛擬 NIC。 下列範例會建立三個虛擬 NIC。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)︰

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>步驟 5 - 建置您的應用程式
**cloud-init** 是自訂 VM 的常用方法。 您可以使用 **cloud-init** 來安裝套件和寫入檔案。 當 **cloud_init** 在初始部署期間執行時，沒有其他步驟可讓您的應用程式執行。 一旦 VM 完成部署且應用程式正在執行，負載平衡器就會開始分配流量。 如需有關使用 **cloud-init** 的詳細資訊，請參閱[在建立期間使用 cloud-init 自訂 Linux VM](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

下列 **cloud-init** 組態會安裝 **nodejs** 和 **npm**，然後安裝 **nginx** 並將它設定為您應用程式的 Web Proxy。 此組態也會建立簡單的 'Hello World' Node.js 應用程式，然後使用 **Express** 初始化並啟動應用程式。 如果您想使用不同的應用程式架構，據以調整套件和已部署的應用程式。

建立名為 `cloud-init.txt` 的檔案並貼上下列組態︰

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>步驟 6 - 建立虛擬機器
備妥所有基礎元件，您現在可以建立高度可用的 VM 來執行您的應用程式。

### <a name="create-vms"></a>建立 VM
使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立三部 VM 並產生 SSH 金鑰 (如果尚未存在的話)︰

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

建立及設定這三部 VM 需要幾分鐘的時間。 負載平衡器會自動偵測應用程式何時在每部 VM 上執行。 當應用程式正在執行時，負載平衡器規則就開始分配流量。

### <a name="test-your-app"></a>測試應用程式
使用 [az network public-ip show](/cli/azure/network/public-ip#show) 取得負載平衡器的公用 IP 位址。 下列範例會取得稍早建立之 `myPublicIP` 的 IP 位址︰

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

在 Web 瀏覽器中輸入公用 IP 位址。 應用程式隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱︰

![執行 Node.js 應用程式](./media/virtual-machines-linux-tutorial-load-balance-nodejs/running-nodejs-app.png)

強制重新整理您的 Web 瀏覽器，以查看負載平衡器如何將流量分散於執行您應用程式的這三部 VM。


## <a name="step-7---management-tasks"></a>步驟 7 - 管理工作
您可能需要在執行您應用程式的 VM 上執行維護，例如安裝 OS 更新。 若要處理您應用程式增加的流量，您可能需要新增額外的 VM。 本節說明如何在負載平衡器中移除或新增 VM。

### <a name="remove-a-vm-from-the-load-balancer"></a>從負載平衡器移除 VM
使用 [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove) 從後端位址集區移除 VM。 下列範例會從 `myLoadBalancer` 移除 **myVM2** 的虛擬 NIC：

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

強制重新整理您的 Web 瀏覽器，以查看負載平衡器如何將流量分散到其餘兩部執行您應用程式的 VM。 您現在可以在 VM 上執行維護，例如安裝 OS 更新或執行 VM 重新開機。

### <a name="add-a-vm-to-the-load-balancer"></a>將 VM 新增至負載平衡器
在執行 VM 維護之後，或者如果需要擴充容量，請使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add) 將 VM 新增至後端位址集區。 下列範例會將適用於 **myVM2** 的虛擬 NIC 新增至 `myLoadBalancer`：

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>後續步驟
若要深入了解本教學課程中介紹的一些高可用性功能，請參閱下列資訊︰

- [管理 Linux 虛擬機器的可用性](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure 負載平衡器概觀](../load-balancer/load-balancer-overview.md)
- [使用網路安全性群組來控制網路流量](../virtual-network/virtual-networks-nsg.md)
- [Azure CLI 範例指令碼](virtual-machines-linux-cli-samples.md)
