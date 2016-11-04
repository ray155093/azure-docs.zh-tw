---
title: Azure Resource Manager 範本中的存取和安全性 | Microsoft Docs
description: Azure 虛擬機器 DotNet 核心教學課程
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/21/2016
ms.author: nepeters

---
# <a name="access-and-security-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的存取和安全性
存取裝載在 Azure 中的應用程式時，可能需要透過網際網路或與 Azure 的 VPN/Express Route 連線才能存取。 在「音樂市集」應用程式範例中，是透過公用 IP 位址讓網站在網際網路上可供使用。 建立存取方式之後，應該保護對應用程式的連線，以及對虛擬機器資源本身的存取。 這項存取安全性是透過「網路安全性群組」來提供。 

本文件詳細說明範例 Azure Resource Manager 範本中如何保護「音樂市集」應用程式。 所有相依項目和獨特的設定都會以醒目提示的方式標示。 為了獲得最佳體驗，請將一個解決方案執行個體預先部署到您的 Azure 訂用帳戶，然後與 Azure Resource Manager 範本搭配運作。 您可以在下列連結找到完整的範本 – [Ubuntu 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)。

## <a name="public-ip-address"></a>公用 IP 位址
若要提供對 Azure 資源的公用存取，可以使用公用 IP 位址。 您可以使用靜態或動態 IP 位址來設定公用 IP 位址。 如果使用靜態位址，當虛擬機器被停止並解除配置時，系統就會移除該位址。 當機器重新啟動時，系統可能會為它指派不同的公用 IP 位址。 若要防止 IP 位址變更，可以使用保留的 IP 位址。 

您可以透過使用 Visual Studio 的「加入新資源精靈」或在範本中插入有效的 JSON，將「公用 IP 位址」新增至 Azure Resource Manager 範本。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [公用 IP 位址](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121)。

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

「公用 IP 位址」可以與「虛擬網路介面卡」或「負載平衡器」關聯。 在此範例中，由於「音樂市集」網站的負載會在數部虛擬機器之間進行平衡，因此「公用 IP 位址」會連接至「負載平衡器」。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [公用 IP 位址與負載平衡器的關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208)。

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Azure 入口網站中所示的公用 IP 位址樣子。 請注意，公用 IP 位址是與負載平衡器關聯，而不是虛擬機器。 本系列的下一個文件中將會詳細說明網路負載平衡器。

![公用 IP 位址](./media/virtual-machines-linux-dotnet-core/pubip.png)

如需有關「Azure 公用 IP 位址」的詳細資訊，請參閱 [Azure 中的 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

## <a name="network-security-group"></a>網路安全性群組
建立對 Azure 資源的存取方式之後，應該對此存取進行限制。 就 Azure 虛擬機器而言，是使用「網路安全性群組」來達到保護存取的目的。 在「音樂市集」應用程式範例中，除了透過連接埠 80 進行的 http 存取和透過連接埠 22 進行的 SSH 存取之外，所有對虛擬機器的存取都會受到限制。 您可以透過使用 Visual Studio 的「加入新資源精靈」或在範本中插入有效的 JSON，將「網路安全性群組」新增至 Azure Resource Manager 範本。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [網路安全性群組](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68)。

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

在此範例中，網路安全性群組是與「虛擬網路」資源中宣告的子網路物件關聯。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [網路安全性群組與虛擬網路的關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158)。

```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

以下是網路安全性群組在 Azure 入口網站中看起來的樣子。 請注意，NSG 可以與子網路和 (或) 網路介面關聯。 在此案例中，NSG 是與子網路關聯。 在此組態中，輸入規則會套用至連接到子網路的所有虛擬機器。

![網路安全性群組](./media/virtual-machines-linux-dotnet-core/nsg.png)

如需有關「網路安全性群組」的深入資訊，請參閱[什麼是網路安全性群組](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)。

## <a name="next-step"></a>後續步驟
<hr>

[步驟 3 - Azure Resource Manager 範本中的可用性和規模](virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

<!--HONumber=Oct16_HO2-->


