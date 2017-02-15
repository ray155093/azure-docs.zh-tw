---
title: "使用 Azure Resource Manager 範本部署計算資源 | Microsoft Docs"
description: "Azure 虛擬機器 DotNet 核心教學課程"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b026fe81-1bc1-4899-ac32-886091671498
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b746e4218ea505ea5e0a1579608e0ecfa10e87e7


---
# <a name="application-architecture-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本的應用程式架構
開發 Azure Resource Manager 部署時，必須將計算需求與 Azure 資源和服務對應。 如果應用程式是由數個 http 端點、一個資料庫及一個資料快取服務所組成，就必須將裝載這當中每個元件的 Azure 資源合理化。 例如，範例「音樂市集」應用程式包含一個裝載於虛擬機器上的 Web 應用程式，以及一個裝載於 Azure SQL Database 中的 SQL Database。 

本文件詳細說明範例 Azure Resource Manager 範本中如何設定「音樂市集」計算資源。 所有相依項目和獨特的設定都會以醒目提示的方式標示。 為了獲得最佳體驗，請將一個解決方案執行個體預先部署到您的 Azure 訂用帳戶，然後與 Azure Resource Manager 範本搭配運作。 您可以在下列連結找到完整的範本 – [Windows 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="virtual-machine"></a>虛擬機器
「音樂市集」應用程式包含一個可供客戶瀏覽和購買音樂的 Web 應用程式。 雖然有好幾個 Azure 服務可以裝載 Web 應用程式，但是這個範例是使用「虛擬機器」。 使用範例「音樂市集」範本時，會部署虛擬機器、安裝 Web 伺服器，以及安裝並設定「音樂市集」網站。 針對本文，只會詳細說明虛擬機器部署。 Web 伺服器和應用程式的組態在稍後的文章中會有詳細說明。

您可以透過使用 Visual Studio 的「加入新資源」精靈或在部署範本中插入有效的 JSON，將虛擬機器新增至範本。 部署虛擬機器時，也需要數個相關的資源。 如果使用 Visual Studio 來建立範本，就會為您建立這些資源。 如果以手動方式建構範本，則需要插入及設定這些資源。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [虛擬機器 JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285)。

```json
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

部署之後，就可以在 Azure 入口網站中看到虛擬機器屬性。

![虛擬機器](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>儲存體帳戶
儲存體帳戶有許多儲存體選項和功能。 就 Azure 虛擬機器的內容而言，儲存體帳戶會保有虛擬機器和任何其他資料磁碟的虛擬硬碟。 「音樂市集」範例包含一個儲存體帳戶，其中保有部署中每個虛擬機器的虛擬硬碟。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [儲存體帳戶](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

儲存體帳戶會在虛擬機器的 Resource Manager 範本宣告內與虛擬機器關聯。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [虛擬機器與儲存體帳戶關聯](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321)。

```json
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

部署之後，即可在 Azure 入口網站中檢視儲存體帳戶。

![儲存體帳戶](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

按一下來進入儲存體帳戶 Blob 容器，即可看到以範本部署之每個虛擬機器的虛擬硬碟檔案。

![虛擬硬碟](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

如需有關「Azure 儲存體」的資訊，請參閱 [Azure 儲存體文件](https://azure.microsoft.com/documentation/services/storage/)。

## <a name="virtual-network"></a>虛擬網路
如果虛擬機器需要內部網路功能 (例如與其他虛擬機器及 Azure 資源進行通訊的能力)，就需要「Azure 虛擬網路」。  虛擬網路不會讓虛擬機器變成可透過網際網路存取的虛擬機器。 公用連線能力需要公用 IP 位址，本系列稍後會詳細說明。

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [虛擬網路和子網路](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

在 Azure 入口網站中，虛擬網路看起來會像下圖。 請注意，以範本部署的所有虛擬機器會都連接至虛擬網路。

![虛擬網路](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>網路介面
 網路介面會將虛擬機器連接到虛擬網路，更具體來說，是連接到虛擬網路中已定義的子網路。 

 請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [網路介面](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

每個虛擬機器資源都包含一個網路設定檔。 網路介面是在此設定檔中與虛擬機器關聯。  

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [虛擬機器網路設定檔](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330)。

```json
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

在 Azure 入口網站中，網路介面看起來會像下圖。 您可以在網路介面資源上看到內部 IP 位址與虛擬機器關聯。

![網路介面](./media/virtual-machines-windows-dotnet-core/nic-win.png)

如需有關「Azure 虛擬網路」的詳細資訊，請參閱 [Azure 虛擬網路文件](https://azure.microsoft.com/documentation/services/virtual-network/)。

## <a name="azure-sql-database"></a>Azure SQL Database
除了裝載「音樂市集」網站的虛擬機器之外，也會部署 Azure SQL Database 來裝載「音樂市集」資料庫。 在這裡，使用 Azure SQL Database 的好處在於不需要第二組虛擬機器，且規模和可用性皆已內建於服務中。

您可以透過使用 Visual Studio 的「加入新資源」精靈或在範本中插入有效的 JSON，來新增 Azure SQL Database。 SQL Server 資源包括已獲授與 SQL 執行個體上系統管理權限的使用者名稱和密碼。 此外，也會新增 SQL 防火牆資源。 裝載在 Azure 中的應用程式預設即能夠與 SQL 執行個體連線。 若要允許外部應用程式 (例如 SQL Server Management Studio) 連線到 SQL 執行個體，就必須設定防火牆。 就「音樂市集」示範而言，無須變更預設組態。 

請依循下列連結來查看 Resource Manager 範本內的 JSON 範例 – [Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379)。

```json
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Azure 入口網站中所示的 SQL Server 和 MusicStore 資料庫檢視。

![SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

如需有關部署 Azure SQL Database 的詳細資訊，請參閱 [Azure SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)。

## <a name="next-step"></a>後續步驟
<hr>

[步驟 2 - Azure Resource Manager 範本中的存取和安全性](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


