---
title: "在 Azure 中使用虛擬機器擴展集建立高可用性應用程式 | Microsoft Docs"
description: "在 Linux VM 上使用虛擬機器擴展集和 Azure CLI，建立及部署高可用性應用程式。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>在 Linux 中使用虛擬機器擴展集建立高可用性應用程式
本教學課程示範如何在虛擬機器擴展集中建立高可用性應用程式。 您也會了解如何自動化擴展集中的虛擬機器組態。 


## <a name="step-1---create-a-resource-group"></a>步驟 1 - 建立資源群組
若要完成本教學課程，請確定您已安裝最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果您尚未登入 Azure 訂用帳戶，請使用 [az login](/cli/azure/#login) 進行登入並遵循螢幕上的指示。

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 下列範例會在 `westus` 位置建立名為 `myResourceGroupVMSS` 的資源群組：

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>步驟 2 - 定義您的應用程式
您使用的 **cloud-init** 組態，與建立高可用性、負載平衡應用程式的教學課程組態相同。 如需有關使用 **cloud-init** 的詳細資訊，請參閱[在建立期間使用 cloud-init 自訂 Linux VM](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

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


## <a name="step-3---create-scale-set"></a>步驟 3 - 建立擴展集
虛擬機器擴展集可讓您部署和管理一組相同、自動調整的虛擬機器。 擴展集使用的元件，與[在 Azure 上建置高可用性應用程式](tutorial-load-balance-nodejs.md)教學課程中所學習到的元件相同。 這些元件包括可用性設定組、容錯網域和更新網域，以及負載平衡器。

使用擴展集，會為您建立和管理這些資源。 擴展集中的 VM 數目可以根據定義規則自動增加或減少。 您可以[使用自訂映像](capture-image.md)作為虛擬機器的來源，或者如本教學課程所示，在部署期間使用 **cloud-init**來設定 VM。

使用 [az vmss create](/cli/azure/vmss#create) 建立虛擬機器擴展集。 下列範例會建立名為 `myScaleSet` 的擴展集：

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


## <a name="step-4---configure-firewall"></a>步驟 4 - 設定防火牆
負載平衡器會自動建立，作為虛擬機器擴展集的一部分。 負載平衡器會使用負載平衡器規則，將流量分散於一組定義的 VM。 若要允許流量觸達 Web 應用程式，使用 [az network lb probe create](/cli/azure/network/lb/probe#create)建立規則。 下列範例會建立名為 `myLoadBalancerRuleWeb` 的規則：

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>步驟 5 - 測試應用程式
使用 [az network public-ip show](/cli/azure/network/public-ip#show) 取得負載平衡器的公用 IP 位址。 下列範例會取得建立作為擴展集一部分之 `myScaleSetLBPublicIP` 的 IP 位址︰

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

在 Web 瀏覽器中輸入公用 IP 位址。 應用程式隨即顯示，包括負載平衡器分散流量之 VM 的主機名稱︰

![執行 Node.js 應用程式](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

強制重新整理您的 Web 瀏覽器，以查看負載平衡器如何將流量分散於執行應用程式之擴展集中的所有 VM。


## <a name="step-6---management-tasks"></a>步驟 6 - 管理工作
在擴展集生命週期中，您可能需要執行一或多個管理工作。 此外，您可以建立指令碼來自動化各種生命週期工作。 Azure CLI 2.0 提供快速的方式來執行這些工作。 以下是一些常見工作。

### <a name="increase-or-decrease-vm-instances"></a>增加或減少 VM 執行個體
您可以使用 [az vmss scale](/cli/azure/vmss#scale)，增加或減少擴展集中虛擬機器的數目。 下列範例會將擴展集中 VM 的數目增加為 `5`：

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

自動調整規則可讓您定義如何在擴展集中相應增加或相應減少 VM 的數目，以回應例如網路流量或 CPU 使用率的需求。 目前，無法在 Azure CLI 2.0 中設定這些規則。 使用 [Azure 入口網站](https://portal.azure.com)以設定自動調整。

### <a name="get-connection-info"></a>取得連線資訊
若要取得擴展集中 VM 的相關連線資訊，請使用 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info)。 此命令會輸出每部 VM 的公用 IP 位址和連接埠，可讓您與 SSH 連線︰

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>刪除資源群組
刪除資源群組同時會刪除其內含的所有資源。

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>後續步驟
在本教學課程中，我們使用 **cloud-init** 定義 Web 應用程式，並且在部署期間設定每部 VM。 如需擷取 VM 來作為擴展集中來源映像的詳細資訊，請參閱[如何一般化和擷取 Linux 虛擬機器](capture-image.md)。

若要深入了解本教學課程中介紹的虛擬機器擴展集功能，請參閱下列資訊︰

- [Azure 虛擬機器擴展集概觀](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure 負載平衡器概觀](../../load-balancer/load-balancer-overview.md)
- [使用網路安全性群組來控制網路流量](../../virtual-network/virtual-networks-nsg.md)
