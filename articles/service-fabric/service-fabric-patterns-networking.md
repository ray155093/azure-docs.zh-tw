---
title: "Azure Service Fabric 的網路功能模式 | Microsoft Docs"
description: "描述 Service Fabric 常見的網路功能模式，以及如何使用 Azure 網路功能建立叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Service Fabric 網路功能模式
建立 Service Fabric 叢集時，我們看到較常見的問題是如何整合將叢集和各種 Azure 網路功能整合。  此文章說明如何使用下列功能建立叢集︰

- [現有的虛擬網路/子網路](#existingvnet)
- [靜態公用 IP 位址](#staticpublicip)
- [僅內部負載平衡器](#internallb)
- [內部 + 外部負載平衡器](#internalexternallb)

需牢記的重要概念是 Service Fabric 會在標準的虛擬機器擴展集中執行，因此，可以在虛擬機器擴展集中使用的任何功能也可以搭配 Service Fabric 叢集使用。 Resource Manager 範本的網路功能部分相同。  一旦您部署至現有的 VNet，即可輕鬆地合併其他網路功能，例如 ExpressRoute、VPN 閘道、網路安全性群組 (NSG) 和 VNet 對等互連。

Service Fabric 唯一特有的部分在於 [Azure 入口網站 (英文)](https://portal.azure.com) 在內部會使用 Service Fabric 資源提供者 (SFRP) 呼叫叢集，以取得節點和應用程式的相關資訊。  SFRP 對管理端點上的 HTTP 閘道連接埠 (預設為&19080;) 需具備可公開存取的輸入存取權，而 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 會使用此連接埠來管理您的叢集。 Service Fabric 資源提供者也會使用此連接埠來查詢您叢集的相關資訊，以顯示在 Azure 入口網站中。  如果無法從 SFRP 存取此連接埠，您會在管理入口網站看到類似「找不到節點」的訊息，且您的節點和應用程式清單會顯示空白。  如果您想透過 Azure 入口網站看到您的叢集，您的負載平衡器必須公開公用 IP 位址，且您的 NSG 必須允許連入的 19080 流量。  如果不符合這些需求，Azure 入口網站就不會顯示您叢集的目前狀態。  除此之外，您的叢集不受影響，您可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 來取得目前的狀態，根據您的網路功能需求，這可能是可接受的限制。  這是暫時性的限制，我們計劃在未來的更新中移除，屆時您的叢集可能無法公開存取，卻不會失去任何管理入口網站功能。

## <a name="templates"></a>範本

您可以在[這裡](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip)找到所有範本。 使用下列 Powershell 命令應該可以依原樣部署範本。  如果要部署現有的 VNet 範本或靜態的公用 IP 範本，請務必先瀏覽[初始設定](#initialsetup)一節。

<a id="initialsetup"></a>
## <a name="initial-setup"></a>初始設定

### <a name="existing-virtual-network"></a>現有的虛擬網路

我以資源群組 *ExistingRG* 中名為 'ExistingRG-vnet' 的現有虛擬網路開始，子網路名為 'default'。  這些資源是使用 Azure 入口網站來建立標準虛擬機器時所建立的預設值。  您也可以建立 VNet 和子網路而不建立虛擬機器。 不過，將叢集新增至現有的 VNet 的主要目標是提供與其他 VM 的網路連接，因此，建立 VM 可提供典型用法的具體範例。  如果您的 Service Fabric 叢集只會使用內部負載平衡器而不使用公用 IP 位址，則 VM 和其公用 IP 也可用來當做跳箱 (Jump Box)。

### <a name="static-public-ip-address"></a>靜態公用 IP 位址

靜態公用 IP 位址通常是從其所屬 VM 個別管理的專用資源，因此會佈建在專用的網路功能資源群組中 (而非 Service Fabric 叢集資源群組本身)。  透過 Azure 入口網站或 Powershell，在同一個 *ExistingRG* 資源群組中建立一個名為 'staticIP1' 的靜態公用 IP 位址：

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric 範本

我使用的是 Service Fabric template.json，在使用標準入口網站精靈建立叢集之前，您可以先從入口網站下載。 您也可以使用[範本庫 (英文)](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric) 中的其中一個範本，例如[五個節點的 Service Fabric 叢集 (英文)](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/)。

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>現有的虛擬網路/子網路

[2016 年 1 月 24 日︰ 還有另一個不在 Service Fabric 範圍內的範例：[https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)]

1. 將子網路參數變更為現有子網路的名稱，並加入兩個新的參數以參考現有的 VNet：

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. 變更 *vnetID* 變數以指向現有的 VNet︰

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. 從資源中移除 *Microsoft.Network/virtualNetworks*，使 Azure 不會建立新的 VNet：

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. 從 *Microsoft.Compute/virtualMachineScaleSets* 的 *dependsOn* 屬性註解掉 VNet，我們就不需要建立新的 VNet：

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. 部署範本：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    部署之後，您的虛擬網路應該會包含新的擴展集 VM，虛擬機器擴展集節點類型應該會顯示現有的 VNet 和子網路。  您也可以 RDP 到 VNet 中現有的 VM，並偵測新的擴展集 VM：

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>靜態公用 IP 位址

1. 加入現有靜態 IP 資源群組的名稱、名稱和 FQDN 的參數︰

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. 移除 *dnsName* 參數，因為靜態 IP 已經有一個參數︰

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. 加入一個參考現有靜態 IP 的變數：

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. 從*資源*中移除 *Microsoft.Network/publicIPAddresses*，使 Azure 不會建立新的 IP 位址：

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 從 *Microsoft.Network/loadBalancers* 的 *dependsOn* 屬性註解掉 IP 位址，我們就不需要建立新的 IP 位址：

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. 將 *Microsoft.Network/loadBalancers* 資源中 *frontendIPConfigurations*的 *publicIPAddress* 元素變更為參考現有的靜態 IP，而非新建的靜態 IP：

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. 將 *Microsoft.ServiceFabric/clusters*資源中的 *managementEndpoint* 變更為靜態 IP 的 DNS FQDN。  **如果您使用安全的叢集，請務必將 'http://' 變更為 'https://'。** (注意︰這項指示僅針對 Service Fabric 叢集。  如果您使用虛擬機器擴展集，請略過此步驟)：

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. 部署範本：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

在您部署後，會看到負載平衡器繫結至另一個資源群組中的公用靜態 IP 位址。 Service Fabric 用戶端連線端點和 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 端點會指向靜態 IP 位址的 DNS FQDN。

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>僅內部負載平衡器

此案例使用僅內部負載平衡器取代預設 Service Fabric 範本中的外部負載平衡器。  請參閱前文以了解 Azure 入口網站與 SFRP 隱含意義。

1. 移除不需要的 *dnsName* 參數︰

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. 如果使用靜態配置方法，請選擇性地加入靜態 IP 位址參數。 如果使用動態配置方法，則不需要此動作︰

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. 從資源中移除 *Microsoft.Network/publicIPAddresses*，使 Azure 不會建立新的 IP 位址：

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 移除 *Microsoft.Network/loadBalancers* 的 IP 位址 *dependsOn* 屬性，我們就不需要建立新的 IP 位址。  新增 VNet *dependsOn* 屬性，因為負載平衡器現在需要 VNet 中的子網路︰

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. 將負載平衡器的 *frontendIPConfigurations* 從使用 *publicIPAddress* 變更為使用子網路和 *privateIPAddress*，而後者使用預先定義的靜態內部 IP 位址。  您可以透過移除 *privateIPAddress* 元素，並將 *privateIPAllocationMethod* 變更為「動態」以使用動態 IP 位址。

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. 在 *Microsoft.ServiceFabric/clusters* 資源中，變更 *managementEndpoint* 以指向內部負載平衡器位址。  **如果您使用安全的叢集，請務必將 'http://' 變更為 'https://'。** (注意︰這項指示僅針對 Service Fabric 叢集。  如果您使用虛擬機器擴展集，請略過此步驟)：

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. 部署範本：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

在您部署後，負載平衡器會使用私用靜態 IP 位址 10.0.0.250。 如果您在同一個 VNet 中有另一部電腦，您也可以瀏覽到內部 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 端點，即可看到它連接至負載平衡器後方的其中一個節點。

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>內部與外部負載平衡器

此案例採用現有的單一節點類型外部負載平衡器，並為同一節點類型新增一個額外的內部負載平衡器。  連接到後端位址集區的後端連接埠只能指派給單一負載平衡器，因此您必須決定哪一負載平衡器應具有您的應用程式連接埠，以及哪一負載平衡器應具有管理端點 (連接埠 19000/19080)。  如果您決定要將管理端點放在內部負載平衡器上，請注意上述的 SFRP 限制。  此範例將管理端點保存在外部負載平衡器，而且新增連接埠 80 應用程式連接埠，並將它放在內部負載平衡器上。

如果您想要兩個節點類型的叢集，其中一個節點類型在外部負載平衡器，另一個在內部負載平衡器，則只要採用入口網站建立的兩個節點類型範本 (隨附 2 個負載平衡器)，然後依照上述＜僅內部負載平衡器＞一節，將第二個負載平衡器切換至內部負載平衡器。

1. 新增靜態內部 LB IP 位址參數 (如果想要使用動態 IP 位址，請參閱上面的備註)：

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. 新增應用程式連接埠 80 參數︰

3. 透過複製/貼上並將 "-Int" 新增到指定處，以新增現有網路功能變數的內部版本︰

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. 如果您以入口網站產生的範本和應用程式連接埠 80 開始，預設入口網站範本會在外部負載平衡器上新增 *AppPort1* (連接埠 80)。  在此情況下，請將它從外部負載平衡器 *loadBalancingRules* 和探查中移除，以將它新增至內部負載平衡器。

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. 新增第二個 *Microsoft.Network/loadBalancers* 資源，看起來非常類似於在先前的[僅內部負載平衡器](#internallb)一節中建立的內部負載平衡器，但使用 '-Int' 負載平衡器變數，並只實作應用程式連接埠 80。  這也會移除 *inboundNatPools* 以在公用負載平衡器上保存 RDP 端點 – 如果您想將 RDP 保存在內部負載平衡器，請將 *inboundNatPools* 從外部負載平衡器移至這個內部負載平衡器。

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. 在 *Microsoft.Compute/virtualMachineScaleSets* 資源的 *networkProfile* 中新增內部後端位址集區︰

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. 部署範本：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

在您部署後，會在資源群組中看到兩個負載平衡器，透過瀏覽負載平衡器，您會看到公用 IP 位址和管理端點 (連接埠 19000/19080) 指派給公用 IP 位址，靜態內部 IP 位址與應用程式端點 (連接埠 80) 則指派給內部負載平衡器，而兩個負載平衡器均使用相同的虛擬機器擴展集後端集區。

## <a name="next-steps"></a>後續步驟
既然您已了解 Service Fabric 叢集與 Azure 網路功能的整合，請繼續進行並[建立叢集](service-fabric-cluster-creation-via-arm.md)。 

