---
title: "在 Site Recovery 中測試容錯移轉 (VMM 到 VMM) | Microsoft Docs"
description: "Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及復原作業。 了解如何容錯移轉到 Azure 或次要資料中心。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 6b1a5b2879a7b98ec4ad3e8ebbc9e95c0740d89f
ms.openlocfilehash: 3aaa005319b1ce2a10cd913c63b31860d31b797e
ms.lasthandoff: 02/23/2017


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>在 Site Recovery 中測試容錯移轉 (VMM 到 VMM)
> [!div class="op_single_selector"]
> * [執行測試容錯移轉至 Azure](./site-recovery-test-failover-to-azure.md)
> * [測試容錯移轉 (VMM 至 VMM)](./site-recovery-test-failover-vmm-to-vmm.md)


本文提供使用 VMM 管理的內部部署站台做為復原站台，對使用 Site Recovery 保護的虛擬機器與實體伺服器執行測試容錯移轉或 DR (災害復原) 訓練的相關資訊和指示。 

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

執行測試容錯移轉可驗證您的複寫策略，或在沒有資料遺失或停機時間的情況下執行災害復原訓練。 執行測試容錯移轉不會影響任何正在進行的複寫或生產環境。 測試容錯移轉可在虛擬機器或[復原計劃](site-recovery-create-recovery-plans.md)上執行。 觸發測試容錯移轉時，您必須指定測試虛擬機器會連接的網路。 一旦觸發測試容錯移轉，您可以在**工作**頁面追蹤進度。  


## <a name="preparing-infrastructure-for-test-failover"></a>準備測試容錯移轉的基礎結構
* 如果您想要使用現有的網路來執行測試容錯移轉，請在該網路中準備 Active Directory、DHCP 和 DNS。
* 如果您想要使用自動建立 VM 網路的選項來執行測試容錯移轉，請在您將用於測試容錯移轉之復原方案中的群組&1; 前新增手動步驟，然後將基礎結構資源新增到自動建立的網路，接著再執行測試容錯移轉。

### <a name="things-to-note"></a>注意事項
* 複寫到次要站台時，複本機器所使用的網路類型不需要與用來測試容錯移轉的邏輯網路類型相符，但某些組合可能無法運作。 如果複本會使用 DHCP 和 VLAN 架構的隔離，則複本的 VM 網路不需要靜態 IP 位址集區。 因此，使用 Windows 網路虛擬化來測試容錯移轉將不會運作，因為沒有任何位址集區可供使用。 此外，如果複本網路是「未隔離」且測試網路是 Windows 網路虛擬化，則測試容錯移轉將無法運作。 這是因為「未隔離」的網路沒有建立 Windows 網路虛擬化網路所需的子網路。
* 容錯移轉之後，用來將複本虛擬機器連接到對應 VM 網路的方式，取決於 VM 網路在 VMM 主控台中的設定方式：
  * **設定為未隔離或 VLAN 隔離的 VM 網路**- 如果已針對 VM 網路定義 DHCP，則複本虛擬機器將使用針對相關聯邏輯網路中網路站台所指定的設定連接到 VLAN 識別碼。 虛擬機器將從可用的 DHCP 伺服器接收其 IP 位址。 您不需要針對目標 VM 網路定義靜態 IP 位址集區。 如果已針對 VM 網路使用靜態 IP位址集區，則複本虛擬機器將使用針對相關聯邏輯網路中網路站台所指定的設定連接到 VLAN 識別碼。 虛擬機器將從針對 VM 網路所定義的集區接收其 IP 位址。 如果靜態 IP 位址集區未定義於目標 VM 網路上，則 IP 位址配置將會失敗。 IP 位址集區應建立於您即將用來保護與復原的來源和目標 VMM 伺服器上。
  * **含有 Windows 網路虛擬化的 VM 網路**- 如果使用此設定來設定 VM 網路，就應該針對目標 VM 網路定義靜態集區，而不論是否已將來源 VM 網路設定為使用 DHCP 或靜態 IP 位址集區。 如果您定義 DHCP，目標 VMM 伺服器將用來做為 DHCP 伺服器，並提供來自針對目標 VM 網路定義之集區的 IP 位址。 如果已定義來源伺服器的靜態 IP 位址用法，則目標 VMM 伺服器將配置來自集區的 IP 位址。 在這兩種情況下，如果未定義靜態 IP 位址集區，IP 位址配置將會失敗。


### <a name="prepare-dhcp"></a>準備 DHCP
如果參與測試容錯移轉的虛擬機器會使用 DHCP，則應該在基於測試容錯移轉目的而建立的隔離網路中建立測試 DHCP 伺服器。

### <a name="prepare-active-directory"></a>準備 Active Directory
若要執行測試容錯移轉以進行應用程式測試，您需要在測試環境中具有 Active Directory 生產環境的複本。 如需詳細資訊，請參閱 [Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#test-failover-considerations) 一節。 

### <a name="prepare-dns"></a>準備 DNS
準備 DNS 伺服器來進行測試容錯移轉，如下所示：

* **DHCP**- 如果虛擬機器使用 DHCP，就應該在測試 DHCP 伺服器上更新測試 DNS 的 IP 位址。 如果使用的是 Windows 網路虛擬化的網路類型，VMM 伺服器就會當做 DHCP 伺服器使用。 因此，應該在測試容錯移轉中更新 DNS 的 IP 位址。 在此情況下，虛擬機器將會向相關的 DNS 伺服器註冊其本身。
* **靜態位址**- 如果虛擬機器使用靜態 IP 位址，就應該在測試容錯移轉中更新測試 DNS 伺服器的 IP 位址。 您可能需要以測試虛擬機器的 IP 位址來更新 DNS。 您可以基於此目的來使用下列指令碼範例：

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>執行測試容錯移轉
此程序說明如何針對復原方案執行測試容錯移轉。 或者，您可以在 [虛擬機器]  索引標籤上，針對單一虛擬機器或實體伺服器執行容錯移轉。

![Test Failover](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. 選取 [復原方案]  >  *recoveryplan_name*。 按一下 [容錯移轉] **容錯移轉** > **Test 容錯移轉**中張貼意見或問題。
1. 在 [測試容錯移轉] 刀鋒視窗上，指定虛擬機器在測試容錯移轉後應如何連接到網路。 查看[網路選項](#network-options-in-site-recovery)以了解詳細資訊。
1. 在 [工作]  索引標籤上追蹤容錯移轉進度。 
1. 完成之後，請確認虛擬機器已成功啟動。
1. 完成後，在復原方案上按一下 [清除測試容錯移轉]。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。 這將刪除在測試容錯移轉期間所建立的虛擬機器和網路。 


## <a name="network-options-in-site-recovery"></a>Site Recovery 中的網路選項

當您執行測試容錯移轉時，系統會要求您選取用以測試複本電腦的網路設定。 您有許多選項。  

| **測試容錯移轉選項** | **說明** | **容錯移轉檢查** | **詳細資料** |
| --- | --- | --- | --- |
| **容錯移轉到次要 VMM 站台 - 沒有網路** |請勿選取 VM 網路 |容錯移轉會檢查測試機器是否已建立。<br/><br/>測試虛擬機器將建立在複本虛擬機器所在的相同主機上。 它並不會新增至複本虛擬機器所在的雲端。 |<p>容錯移轉的機器不會連線至任何網路。<br/><br/>該機器可以在建立後連線至 VM 網路 |
| **容錯移轉到次要 VMM 站台 - 透過網路** |選取現有的 VM 網路 |容錯移轉會檢查虛擬機器是否已建立 |測試虛擬機器將建立在複本虛擬機器所在的相同主機上。 它並不會新增至複本虛擬機器所在的雲端。<br/><br/>建立獨立於您生產網路的 VM 網路<br/><br/>如果您是使用以 VLAN 為基礎的網路，建議您在 VMM 中另外建立未用於生產網路的測試專用邏輯網路。 此邏輯網路是用來針對測試容錯移轉的目的建立 VM 網路。<br/><br/>邏輯網路應該要與所有裝載虛擬機器之 Hyper-V 伺服器的其中一個網路介面卡相關聯。<br/><br/>針對 VLAN 邏輯網路，您新增至邏輯網路的網路站台應該要是隔離的。<br/><br/>如果您是使用 Windows 網路虛擬化型邏輯網路，Azure Site Recovery 會自動建立隔離的 VM 網路。 |
| **容錯移轉到次要 VMM 站台 - 建立網路** |暫時性測試網路將根據您在 [邏輯網路]  及其相關網路站台中指定的設定自動建立 |容錯移轉會檢查虛擬機器是否已建立 |如果復原計畫使用多個 VM 網路，請使用這個選項。 如果您使用的是 Windows 網路虛擬化網路，這個選項會使用複本虛擬機器網路中的相同設定 (子網路和 IP 位址集區) 自動建立 VM 網路。 在測試容錯移轉完成後，這些 VM 網路將自動清除。</p><p>測試虛擬機器將建立在複本虛擬機器所在的相同主機上。 它並不會新增至複本虛擬機器所在的雲端。 |

> [!TIP]
> 測試容錯移轉期間提供給虛擬機器的 IP 位址與執行計劃性或非計劃性容錯移轉時所收到的 IP 位址相同 (假設該 IP 位址可用於測試容錯移轉網路)。 如果在測試容錯移轉網路中無法使用相同的 IP 位址，則虛擬機器將會收到可用於測試容錯移轉網路中的另一個 IP 位址。
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>在復原站台上測試容錯移轉到實際執行網路 
當您進行測試容錯移轉時，建議您選擇與在 [網路對應] 中提供的實際執行復原站台網路不同的網路。 但是，如果您真的想驗證容錯移轉的虛擬機器中的端對端網路連線，請注意下列幾點︰

1. 進行測試容錯移轉時，請確定主要虛擬機器已關機。 如果不這麼做，相同的網路中將有兩部虛擬機器以相同的身分識別執行，因而可能造成不良的後果。 
1. 當您清除測試容錯移轉虛擬機器時，您對測試容錯移轉虛擬機器所做的任何變更將會遺失。 這些變更不會複寫回主要虛擬機器。
1. 這種測試會導致實際執行應用程式的停機時間。 進行 DR 訓練時，應要求應用程式的使用者不要使用應用程式。  


## <a name="next-steps"></a>後續步驟
一旦您成功嘗試測試容錯移轉之後，您就可以嘗試執行[容錯移轉](site-recovery-failover.md)。

