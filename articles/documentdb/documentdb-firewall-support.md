---
title: "DocumentDB 防火牆支援 | Microsoft Docs"
description: "了解如何使用 IP 存取控制原則進行 Azure DocumentDB 資料庫帳戶上的防火牆支援。"
keywords: "IP 存取控制，防火牆支援"
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>DocumentDB 防火牆支援
為了保護 Azure DocumentDB 資料庫帳戶中所儲存的資料，DocumentDB 已支援利用強式雜湊式訊息驗證碼 (HMAC) 的密碼型[授權模型](https://msdn.microsoft.com/library/azure/dn783368.aspx)。 現在，除了密碼型授權模型之外，DocumentDB 還支援使用原則驅動的 IP 型存取控制來進行輸入防火牆支援。 這種模型與傳統資料庫系統的防火牆規則十分類似，並提供 DocumentDB 資料庫帳戶的額外安全性層級。 您現在可以使用這個模型，設定只能從一組核准的電腦和 (或) 雲端服務存取 DocumentDB 資料庫帳戶。 透過這些核准的電腦和服務集存取 DocumentDB 資源，仍然需要呼叫者呈現有效的授權權杖。

## <a name="ip-access-control-overview"></a>IP 存取控制概觀
只要要求伴隨有效的授權權杖，預設就可以從公用網際網路存取 DocumentDB 資料庫帳戶。 若要設定 IP 原則型存取控制，使用者必須以 CIDR 形式提供這組 IP 位址或 IP 位址範圍，以作為指定資料庫帳戶的允許用戶端 IP 清單。 套用這個組態之後，伺服器將會封鎖源自此允許清單外部之電腦的所有要求。  下圖說明 IP 型存取控制的連接處理流程。

![顯示 IP 型存取控制之連接程序的圖表](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>從雲端服務的連接
在 Azure 中，雲端服務是使用 DocumentDB 裝載中介層服務邏輯的極常見方式。 若要從雲端服務存取 DocumentDB 資料庫帳戶，必須[連絡 Azure 支援人員](#configure-ip-policy)，以將雲端服務的公用 IP 位址新增至與 DocumentDB 資料庫帳戶相關聯的允許 IP 位址清單。  這確保雲端服務的所有角色執行個體可以存取 DocumentDB 資料庫帳戶。 您可以在 Azure 入口網站中擷取雲端服務的 IP 位址，如下列螢幕擷取畫面所示。 

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示雲端服務的公用 IP 位址](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

當您新增其他角色執行個體來相應放大雲端服務時，這些新的執行個體自動可以存取 DocumentDB 資料庫帳戶，原因它們是相同雲端服務的一部分。

## <a name="connections-from-virtual-machines"></a>從虛擬機器的連接
也可以使用[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)或[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，透過 DocumentDB 裝載中介層服務。  若要設定允許從虛擬機器存取 DocumentDB 資料庫帳戶，必須[連絡 Azure 支援人員](#configure-ip-policy)，以將虛擬機器和 (或) 虛擬機器擴展集的公用 IP 位址設定為 DocumentDB 資料庫帳戶的其中一個允許 IP 位址。 您可以在 Azure 入口網站中擷取虛擬機器的 IP 位址，如下列螢幕擷取畫面所示。

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示虛擬機器的公用 IP 位址](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

當您將其他虛擬機器執行個體新增至群組時，它們即可以自動存取 DocumentDB 資料庫帳戶。

## <a name="connections-from-the-internet"></a>從網際網路的連接
從網際網路上的電腦存取 DocumentDB 資料庫帳戶時，必須將電腦的用戶端 IP 位址或 IP 位址範圍新增至 DocumentDB 資料庫帳戶的允許 IP 位址清單。 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> 設定 IP 存取控制原則
使用 Azure 入口網站向 [Azure 支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 提出要求，以在資料庫帳戶上啟用 IP 存取控制原則。

1. 在 [說明 + 支援][](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 刀鋒視窗中，選取 [新增支援要求]。
2. 在 [新增支援要求] 刀鋒視窗中，選取 [基本]。
3. 在 [基本] 刀鋒視窗中，選取下列項目：
   * **問題類型**：配額
   * **訂用帳戶**：與用來新增 IP 存取控制原則之帳戶相關聯的訂用帳戶。
   * **配額類型**：DocumentDB
   * **支援方案**：配額支援 - 已包含。
4. 在 [問題] 刀鋒視窗中，執行下列動作：
   * **嚴重性**：選取 [C - 最低影響]
   * **詳細資料**︰將下列文字複製至方塊中，並包含您的帳戶名稱和 IP 位址："I would like to enable firewall support for my DocumentDB database account. Database account: *Include account name/s*. Allowed IP address/Ranges: *Include IP address/range in CIDR format, for example 13.91.6.132, 13.91.6.1/24*."
   * 按 [下一步] 。 
5. 在 [連絡資訊] 刀鋒視窗中填寫您的連絡詳細資料，然後按一下 [下一步]。 

收到您的要求之後，應該在 24 小時內啟用 IP 存取控制。 您會在要求完成之後收到通知。

![[說明 + 支援] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![[問題] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>針對 IP 存取控制原則進行疑難排解
### <a name="portal-operations"></a>入口網站作業
啟用 DocumentDB 資料庫帳戶的 IP 存取控制原則，即會封鎖所設定之允許 IP 位址範圍清單外部的電腦對 DocumentDB 資料庫帳戶的所有存取。 透過這個模型，也會封鎖從入口網站瀏覽資料平面作業，確保存取控制的完整性。 

### <a name="sdk-rest-api"></a>SDK & Rest API
基於安全性考量，如果從電腦透過 SDK 或 REST API 的存取不在允許清單上，則會傳回沒有其他詳細資料的一般「404 找不到」回應。 請確認設定 DocumentDB 資料庫帳戶的 IP 允許清單，確保將正確的原則組態套用至 DocumentDB 資料庫帳戶。

## <a name="next-steps"></a>後續步驟
如需網路相關效能秘訣的相關資訊，請參閱[效能秘訣](documentdb-performance-tips.md)。




<!--HONumber=Nov16_HO3-->


