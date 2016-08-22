<properties
   pageTitle="針對 Linux VM 開啟連接埠或端點 | Microsoft Azure"
   description="了解如何開啟連接埠 / 使用 Resource Manager 部署模型和 Azure CLI 來建立允許從外部存取您 Linux VM 的端點"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# 開啟連接埠與端點
您在 Azure 中藉由建立網路篩選器來開啟連接埠或建立端點，讓流量流向子網路或虛擬機器 (VM) 網路介面上您選擇的連接埠。您可將控制輸入和輸出流量的這些篩選器放在可接收流量的資源所附加的網路安全性群組上。讓我們使用連接埠 80 上的 Web 流量的常見範例。

## 快速命令
若要建立「網路安全性群組」和規則，您需要讓 [Azure CLI](../xplat-cli-install.md) 處於 Resource Manager 模式 (`azure config mode arm`)。

適當地輸入您自己的名稱和位置來建立「網路安全性群組」︰

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

新增規則以允許流向您 Web 伺服器的 HTTP 流量 (或針對自己的案例 (例如 SSH 存取或資料庫連接) 進行調整)︰

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

將「網路安全性群組」與 VM 的網路介面建立關聯：

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

或者，您也可以將「網路安全性群組」與虛擬網路的子網路建立關聯，而不是只與單一 VM 上的網路介面建立關聯：

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## 網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。您可以深入了解[建立網路安全性群組和 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。深入了解[使用範本建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送，以將唯一的外部連接埠對應至您 VM 上的內部連接埠，您將需要使用負載平衡器和「網路位址轉譯」(NAT) 規則。例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。您可以深入了解[建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-cli.md)。

## 後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。您可以從下列文章中，找到有關建立更詳細環境的資訊︰

- [Azure 資源管理員概觀](../resource-group-overview.md)
- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [負載平衡器的 Azure Resource Manager 概觀](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0810_2016---->