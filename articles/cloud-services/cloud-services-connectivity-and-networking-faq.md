---
title: "Microsoft Azure 雲端服務之連線能力和網路服務問題的常見問題集 | Microsoft Docs"
description: "本文列出 Microsoft Azure 雲端服務之連線能力和網路服務的相關常見問題集。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017


---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 雲端服務之連線能力和網路服務問題：常見問題集 (FAQ)

本文包含 [Microsoft Azure 雲端服務](https://azure.microsoft.com/services/cloud-services)之連線能力和網路服務問題的相關常見問題集。 您也可以參閱 [雲端服務 VM 大小頁面](cloud-services-sizes-specs.md) 以取得大小資訊。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>無法在多 VIP 的雲端服務中保留 IP
首先，請確定您想要保留其 IP 的虛擬機器執行個體已開啟。 其次，請確定您會將保留的 IP 同時用於預備與生產部署。 **勿** 於部署正在升級時變更設定。

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>當我有 NSG 時，應如何設定遠端桌面？
將規則加入到 NSG，以允許連接埠 **3389** 和 **20000** 上的流量。  遠端桌面使用者連接埠 **3389**。  系統已為雲端服務執行個體進行負載平衡，因此您無法直接控制要連線的執行個體。  *RemoteForwarder* 與 *RemoteAccess* 代理程式會管理 RDP 流量，並允許用戶端傳送 RDP Cookie 並指定要連線的個別執行個體。  RemoteForwarder 與 RemoteAccess 代理程式要求您必須開啟連接埠 20000，這在您使用 NSG 的情況下可能是封鎖的。

## <a name="can-i-ping-a-cloud-service"></a>是否可偵測雲端服務？

否，不能使用一般的 "ping"/ICMP 通訊協定來進行。 不允許透過 Azure Load Balancer 進行 ICMP 通訊協定。

若要測試連線能力，建議您進行連接埠偵測。 當 Ping.exe 使用 ICMP 時，諸如 PSPing、Nmap 及 telnet 等其他工具可讓您測試對特定 TCP 通訊埠的連線。

如需詳細資訊，請參閱[使用連接埠偵測而非 ICMP 來測試 Azure VM 連線能力](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>如何避免接收來自未知 IP 位址的成千上萬個叫用，表示對雲端服務的某一種惡意攻擊？
Azure 會實作多層的網路安全性，可保護其平台服務免於遭受分散式阻斷服務 (DDoS) 攻擊。 Azure DDoS 防禦系統是屬於 Azure 的連續監視流程，會透過滲透測試持續進行改良。 此 DDoS 防禦系統的設計，不僅可承受來自外部的攻擊，還能承受來自其他 Azure 租用戶的攻擊。 如需詳細資料，請參閱 [Microsoft Azure 網路安全性](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)。

您也可以建立啟動工作，選擇性地封鎖一些特定的 IP 位址。 如需詳細資訊，請參閱[封鎖特定 IP 位址](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>當我嘗試 RDP 到我的雲端服務執行個體時，會收到這個訊息：「使用者帳戶已過期」。
當您略過 RDP 設定中所設定的到期日時，可能會收到「此使用者帳戶已過期」的錯誤訊息。 您可以從入口網站中變更到期日，方法是遵循下列步驟：
1. 登入 Azure 管理主控台 ( https://manage.windowsazure.com )，瀏覽至您的雲端服務，然後選取 [設定] 索引標籤。
2. 選取 [遠端]。
3. 變更「到期日」的日期，然後儲存設定。

您現在應該能夠 RDP 到您的電腦。

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>為什麼負載平衡器並未平均平衡流量？
如需如何內部負載平衡器運作方式的相關資訊，請參閱 [Azure Load Balancer 的新分散模式](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)。

所用的分配演算法是 5 個 Tuple (來源 IP、來源連接埠、目的地 IP、目的地連接埠、通訊協定類型) 的雜湊，將流量對應至可用的伺服器。 它只在傳輸工作階段內提供綁定。 相同 TCP 或 UDP 工作階段中的封包會被導向至負載平衡端點後面的相同資料中心 IP (DIP) 執行個體。 當用戶端關閉並重新開啟連線或從相同的來源 IP 啟動新的工作階段時，來源連接埠便會變更，進而導致流量進入不同的 DIP 端點。

