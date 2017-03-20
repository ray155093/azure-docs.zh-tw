---
title: "停用、啟用或刪除流量管理員設定檔 | Microsoft Docs"
description: "本文將協助您使用流量管理員設定檔。"
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: f19143e2-2f4f-4ead-bb00-89b95ac92cb4
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3ea5860767cd21d263c0f0126362d3f6d92d58c6
ms.lasthandoff: 11/17/2016


---
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>停用、啟用或刪除設定檔
您可以停用現有的流量管理員設定檔，它就不會將使用者要求轉介到其設定的端點。 當您停用流量管理員設定檔時，設定檔本身和設定檔內含的資訊將保持不變，而且可在流量管理員介面中進行編輯。 當您想要重新啟用該設定檔時，可以在 Azure 入口網站中輕鬆執行，轉介就會恢復。 當您在 Azure 入口網站中建立流量管理員設定檔時，它會自動啟用。

## <a name="to-disable-a-profile"></a>停用設定檔
1. 在您的網際網路 DNS 伺服器上修改 DNS 資源記錄使用適當的記錄類型和指標，指向另一個名稱或網際網路上特定位置的 IP 位址。 也就是在網際網路 DNS 伺服器上變更 DNS 資源記錄，讓它所使用的 CNAME 資源記錄不再指向流量管理員設定檔的網域名稱。
2. 流量將會停止導向至透過流量管理員設定檔設定的端點。
3. 選取您想要停用的設定檔。 若要選取設定檔，請在 [流量管理員] 頁面上，按一下設定檔名稱旁邊的欄反白顯示設定檔。 請不要按設定檔的名稱或名稱旁的箭號，這樣會帶您到設定檔的設定頁面。
4. 選取設定檔之後，按一下頁面底部的 [停用]。

## <a name="to-enable-a-profile"></a>啟用設定檔
1. 選取您想要啟用的設定檔。 若要選取設定檔，請在 [流量管理員] 頁面上，按一下設定檔名稱旁邊的欄反白顯示設定檔。 請不要按設定檔的名稱或名稱旁的箭號，這樣會帶您到設定檔的設定頁面。
2. 選取設定檔之後，按一下頁面底部的 [啟用]。
3. 在您的網際網路 DNS 伺服器上修改 DNS 資源記錄使用 CNAME 記錄類型，這會將公司網域名稱對應到流量管理員設定檔的網域名稱。 如需詳細資訊，請參閱 [將公司網際網路網域指向流量管理員網域](traffic-manager-point-internet-domain.md)。
4. 流量將會再開始導向至各端點。

## <a name="delete-a-profile"></a>刪除設定檔
1. 請確定網際網路 DNS 伺服器上的 DNS 資源記錄所使用的 CNAME 資源記錄，不再指向流量管理員設定檔的網域名稱。
2. 選取您想要刪除的設定檔。 若要選取設定檔，請在 [流量管理員] 頁面上反白顯示設定檔，方法是
3. 按一下設定檔旁邊的資料行。 請不要按設定檔的名稱或名稱旁的箭號，這樣會帶您到設定檔的設定頁面。
4. 選取設定檔之後，按一下頁面底部的 [刪除]。

## <a name="next-steps"></a>後續步驟
[流量管理員 - 停用或啟用端點](disable-or-enable-an-endpoint.md)

[設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)

[設定循環配置資源路由方法](traffic-manager-configure-round-robin-routing-method.md)

[設定效能路由方法](traffic-manager-configure-performance-routing-method.md)

[疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)


