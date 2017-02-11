---
title: "Azure Resource Manager 範本中的可用性和規模 | Microsoft Docs"
description: "Azure 虛擬機器 DotNet 核心教學課程"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 494724b5-06af-4dea-a774-ba580cf27527
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f944edcafb55634c9338bc8dd518a5f032f56e0
ms.openlocfilehash: e714a7e941b25dfb0926934a3dff387363f306d8


---
# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的可用性和規模
可用性和規模是指滿足需求的執行時間和能力。 如果應用程式必須 99.9% 的時間都處於執行狀態，它就需要一個可允許多個並行計算資源的架構。 例如，具有較高層級可用性的組態不會包含單一網站，而是會包含相同網站的多個執行個體，其中前端會有平衡技術。 在此組態中，可以讓一個應用程式執行個體停機來進行維護，而剩下的執行個體則繼續運作。 另一方面，規模則是指應用程式為需求提供服務的能力。 使用已負載平衡的應用程式時，在集區中新增或移除執行個體，即可讓應用程式調整來滿足需求。

本文件詳細說明如何針對可用性和規模設定「音樂市集」範例部署。 所有相依項目和獨特的設定都會以醒目提示的方式標示。 為了獲得最佳體驗，請將一個解決方案執行個體預先部署到您的 Azure 訂用帳戶，然後與 Azure Resource Manager 範本搭配運作。 您可以在下列連結找到完整的範本 – [Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="availability-set"></a>可用性設定組
「可用性設定組」可讓「Azure 虛擬機器」以邏輯方式跨不同的實體主機和其他基礎結構元件，例如電源供應器和實體網路硬體。 可用性設定組可確保在維護期間，裝置故障或其他停機狀況不會影響到所有虛擬機器。 您可以透過使用 Visual Studio 的「加入新資源精靈」或在範本中插入有效的 JSON，將「可用性設定組」新增至 Azure Resource Manager 範本。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [可用性設定組](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

「可用性設定組」會宣告為「虛擬機器」資源的屬性。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [可用性設定組與虛擬機器的關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302)。

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Azure 入口網站中所示的可用性設定組樣子。 以下詳細顯示每個虛擬機器及組態的相關詳細資料。

![可用性設定組](./media/virtual-machines-windows-dotnet-core/ase-win.png)

如需有關「可用性設定組」的深入資訊，請參閱 [管理虛擬機器的可用性](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

## <a name="network-load-balancer"></a>網路負載平衡器
可用性設定組可提供應用程式容錯移轉，而負載平衡器則是讓單一網路位址上有許多應用程式執行個體可供使用。 多個應用程式執行個體可以裝載在多個虛擬機器上，每個虛擬機器皆連接到負載平衡器。 當應用程式被存取時，負載平衡器會將連入要求路由傳送至各個連接的成員。 您可以透過使用 Visual Studio 的「加入新資源精靈」或在 Azure Resource Manager 範本中插入格式正確的 JSON 資源，來新增「負載平衡器」。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [網路負載平衡器](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

由於範例應用程式是透過公用 IP 位址對網際網路公開，因此這個位址會與負載平衡器關聯。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [網路負載平衡器與公用 IP 位址的關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211)。

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

在 Azure 入口網站中，網路負載平衡器概觀會顯示與公用 IP 位址的關聯。

![網路負載平衡器](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>負載平衡器規則
使用負載平衡器時，會設定規則來控制如何在各個預期的資源之間平衡流量。 在範例「音樂市集」應用程式中，流量會抵達公用 IP 位址的連接埠 80，然後分散到所有虛擬機器的連接埠 80。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [負載平衡器規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226)。

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

入口網站中所示的網路負載平衡器規則樣子。

![網路負載平衡器規則](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>負載平衡器探查
負載平衡器也需要監視每個虛擬機器，以便只將要求提供給執行中的系統。 這項監視會透過經常探查預先定義的連接埠來進行。 「音樂市集」部署是設定為探查所有包含的虛擬機器上的連接埠 80。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [負載平衡器探查](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247)。

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Azure 入口網站中所示的負載平衡器探查樣子。

![網路負載平衡器探查](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>輸入 NAT 規則
使用「負載平衡器」時，必須有適當規則來提供對每個「虛擬機器」的非負載平衡存取。 例如，建立與每個虛擬機器的 RDP 連線時，不應該對此流量進行負載平衡，而是應該設定一個預先決定的路徑。 設定預先決定的路徑時是使用「輸入 NAT 規則」資源來設定。 透過使用這項資源，便可將輸入通訊對應到個別的「虛擬機器」。 

在「音樂市集」應用程式中，從 5000 開始的連接埠會對應到每個「虛擬機器」上的連接埠 3389 以進行 RDP 存取。 `copyindex()` 函式可用來遞增連入連接埠，讓第二個「虛擬機器」接收連入接埠 5001，第三個則接收 5002，依此類推。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [輸入 NAT 規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260)。 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Azure 入口網站中所示的一個範例輸入 NAT 規則樣子。 在部署中會為每個虛擬機器建立一個 RDP NAT 規則。

![輸入 NAT 規則](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

如需有關「Azure 網路負載平衡器」的深入資訊，請參閱 [Azure 基礎結構服務的負載平衡](virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="deploy-multiple-vms"></a>部署多個 VM
最後，為了讓「可用性設定組」或「負載平衡器」有效地運作，必須要有多個虛擬機器。 您可以使用 Azure Resource Manager 範本複製函式來部署多個 VM。 使用複製函式時，並不需要定義有限數量的「虛擬機器」，而是可以在部署時動態提供這個值。 複製函式會取用要建立的執行個體數目，以及處理部署適當數目之虛擬機器及相關資源的作業。

在「音樂市集」範本中，已定義一個會擷取執行個體計數的參數。 在範本中，當建立虛擬機器及相關資源時，會全程使用這個數字。

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

在「虛擬機器」資源上，會為複製迴圈提供名稱及參數用來控制產生之複本數目的執行個體數目。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [虛擬機器複製函式](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290)。 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

存取目前的複製函式反覆項目時，可以透過 `copyIndex()` 函式來存取。 複製索引函式的值可用來命名虛擬機器和其他資源。 例如，如果部署兩個虛擬機器執行個體，它們將需要不同的名稱。 `copyIndex()` 函式可用來作為虛擬機器名稱的一部分，以建立一個唯一的名稱。 在「虛擬機器」資源中可以看到用於命名用途的 `copyindex()` 函式範例。 這裡的電腦名稱是由 `vmName` 參數和 `copyIndex()` 函式串連而成。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [複製索引函式](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309)。 

```json
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

在「音樂市集」範例範本中使用 `copyIndex` 函式多次。 利用 `copyIndex` 的資源和函式會包括單一虛擬機器執行個體的所有特定項目，例如網路介面、負載平衡器規則及與函式相依的任何項目。 

如需有關有關複製函式的詳細資訊，請參閱 [在 Azure Resource Manager 中建立資源的多個執行個體](../azure-resource-manager/resource-group-create-multiple.md)。

## <a name="next-step"></a>後續步驟
<hr>

[步驟 4 - 使用 Azure Resource Manager 範本進行應用程式部署](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO2-->


