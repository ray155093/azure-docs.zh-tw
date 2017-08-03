---
title: "Azure 環境中的 Oracle 災害復原案例概觀 | Microsoft Docs"
description: "Azure 環境中 Oracle Database 12c 的災害復原案例。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: zh-tw
ms.lasthandoff: 07/10/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Azure 環境上 Oracle 12c 資料庫的災害復原 (DR)

## <a name="assumptions"></a>假設

- 了解 Oracle Data Guard 設計和 Azure 環境


## <a name="goals"></a>目標
- 設計符合您 DR 需求的拓撲和設定。

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>案例 1 (Azure 上的主要和 DR 站台)

客戶具有主要網站上的 Oracle 資料庫設定。 DR 站台位於不同的區域上。 Oracle Data Guard 用於這些站台間的快速復原。 主要站台也有次要資料庫可進行報告和其他用途。 

### <a name="topology"></a>拓撲

以下是 Azure 設定摘要。

- 兩個網站 (主要和 DR 站台)
- 兩個虛擬網路 (VNet)
- 兩個具有 Data Guard 的 Oracle 資料庫 (主要和待命)
- 兩個具有 Golden Gate 或 Data Guard 的 Oracle 資料庫 (僅限主要站台)
- 主要站台上的兩個應用程式服務以及 DR 站台上的一個應用程式服務
- 「可用性設定組」是用於主要站台上的資料庫和應用程式服務
- 一個站台上的一個 Jumpbox (限制對私人網路的存取) 只允許系統管理員登入
- Jumpbox、應用程式服務、資料庫和 VPN 閘道位於不同的子網路
- NSG 是在應用程式和資料庫子網路上強制執行

![DR 拓撲頁面的螢幕擷取畫面](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>案例 2 (主要站台內部部署和 Azure 上的 DR 站台)

客戶具有內部部署 Oracle 資料庫設定 (主要站台)。 DR 站台是在 Azure 上。 Oracle Data Guard 用於這些站台間的快速復原。 主要站台也有次要資料庫可進行報告和其他用途。 

這項設定有兩種方法。

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1.內部部署與 Azure 之間的直接連線，需要防火牆上有開放 TCP 連接埠。 不建議這種方法，因為它向外界公開 TCP 連接埠。

### <a name="topology"></a>拓撲

以下是 Azure 設定摘要。

- 一個站台 (DR 站台)
- 一個虛擬網路 (VNet)
- 一個具有 Data Guard 的 Oracle 資料庫 (使用中)
- DR 站台上的一個應用程式服務
- 使用一個 Jumpbox (限制對私人網路的存取)，只允許系統管理員登入
- Jumpbox、應用程式服務、資料庫和 VPN 閘道位於不同的子網路
- NSG 是在應用程式和資料庫子網路上強制執行
- 新增 NSG 原則/規則以允許輸入 TCP 連接埠 1521 (或使用者定義)
- 新增 NSG 原則/規則，僅限制 IP 位址/位址內部部署 (DB 或應用程式) 來存取 VNet。

![DR 拓撲頁面的螢幕擷取畫面](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2.更好的方法是使用站對站 VPN。 如需設定 VPN 的詳細資訊，請參閱此[連結](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)。

### <a name="topology"></a>拓撲

以下是 Azure 設定摘要。

- 一個站台 (DR 站台)
- 一個虛擬網路 (VNet)
- 一個具有 Data Guard 的 Oracle 資料庫 (使用中)
- DR 站台上的一個應用程式服務
- 使用一個 Jumpbox (限制對私人網路的存取)，只允許系統管理員登入
- Jumpbox、應用程式服務、資料庫和 VPN 閘道位於不同的子網路
- NSG 是在應用程式和資料庫子網路上強制執行
- 內部部署與 Azure 之間的站對站 VPN 連線

![DR 拓撲頁面的螢幕擷取畫面](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>其他文章：

- [在 Azure 上設計和實作 Oracle 資料庫](oracle-design.md)
- [設定 Oracle Data Guard](configure-oracle-dataguard.md)
- [設定 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 備份和復原](oracle-backup-recovery.md)


## <a name="next-steps"></a>後續步驟

[教學課程︰建立高可用性 VM](../../linux/create-cli-complete.md)

[瀏覽 VM 部署 Azure CLI 範例](../../linux/cli-samples.md)

