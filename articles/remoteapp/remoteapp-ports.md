---
title: "加入客戶虛擬網路中部署的 Azure RemoteApp 白名單的連接埠和 URL 清單 | Microsoft Docs"
description: "了解您必須設定哪些連接埠及 URL，才能透過 Azure RemoteApp 進行通訊。"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9390af174262e0dd2bb5beb30ae08b3063c1a5e6
ms.lasthandoff: 03/31/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>允許存取客戶虛擬網路中部署的 Azure RemoteApp 的連接埠和 URL 清單
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

如果您要在虛擬網路 (VNET) 中部署 Azure RemoteApp 雲端或混合式集合，請看以下的連接埠資訊。 如需虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。 如果您已經建立網路安全性群組 (NSG) 來限制流向您的集合中虛擬網路資源的流量，請確定可透過虛擬網路上的安全性原則存取並允許使用以下連接埠。 如需網路安全性群組的詳細資訊，請參閱[什麼是網路安全性群組？(NSG)？](../virtual-network/virtual-networks-nsg.md)。

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 子網路必須存取以下端點和 URL：
* *.servicebus.windows.net
* *.servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* 輸出：TCP：443、9351、9352、10101-10175 
* 選用 – UDP：10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 用戶端必須存取以下端點和 URL：
這裡的用戶端指的是使用者用來連線到 Azure RemoteApp 集合中部署應用程式的桌上型電腦、裝置等等。

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (選用的 UDP 連接埠是供此位址使用) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* 輸出：TCP：443  
* 選用 - UDP：3391 


