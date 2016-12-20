---
title: "管理 Azure 流量管理員設定檔 | Microsoft Docs"
description: "本文會協助您建立、停用、啟用、刪除及檢視 Azure 流量管理員設定檔的歷程記錄。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 7e7de7dc1eca6903403afef03fdd6afb98ff16c9

---

# <a name="manage-an-azure-traffic-manager-profile"></a>管理 Azure 流量管理員設定檔

流量管理員設定檔會使用流量路由方法來控制分散到您雲端服務或網站端點的流量。 本文說明如何建立及管理這些設定檔。

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>使用「快速建立」功能建立流量管理員設定檔

您可以在 Azure 傳統入口網站中使用 [快速建立]，快速建立流量管理員設定檔。 使用「快速建立」可以讓您建立具有基本組態設定的設定檔。 不過，您無法使用 [快速建立] 中的某些設定，例如端點 (雲端服務和網站) 組、容錯移轉流量路由方法的容錯移轉順序，或是監視設定。 建立設定檔之後，您就可以在 Azure 傳統入口網站中設定這些設定。 流量管理員每個設定檔最多支援 200 個端點。 不過，大部分的使用案例僅需要少量的端點。

### <a name="to-create-a-traffic-manager-profile"></a>若要建立流量管理員設定檔

1. **將您的雲端服務和網站部署到您的生產環境中。** 如需雲端服務的詳細資訊，請參閱 [雲端服務](http://go.microsoft.com/fwlink/p/?LinkId=314074)。 如需網站的詳細資訊，請參閱 [網站](http://go.microsoft.com/fwlink/p/?LinkId=393327)。
2. **登入 Azure 傳統入口網站。** 按一下入口網站左下角的 [新增]，依序按一下 [網絡服務] > [流量管理員]，然後按一下 [快速建立]，開始設定您的設定檔。
3. **設定 DNS 首碼。**  為您的流量管理員設定檔提供一個唯一的 DNS 首碼名稱。 您僅能指定流量管理員網域名稱的首碼。
4. **選取訂用帳戶。**  選取適當的 Azure 訂用帳戶。 每個設定檔都與單一訂用帳戶相關聯。 如果您只有一個訂用帳戶，則不會出現此選項。
5. **選取流量路由方法。** 在 [流量路由原則] 中選取流量路由方法。 如需流量路由方法的詳細資訊，請參閱 [關於流量管理員流量路由方法](traffic-manager-routing-methods.md)。
6. **按一下 [建立] 以建立設定檔**。 設定檔組態完成之後，可以在 Azure 傳統入口網站中的 [流量管理員] 窗格找到您的設定檔。
7. **在 Azure 傳統入口網站中設定端點、監視和其他設定。** 使用「快速建立」只會設定基本設定。 必須設定其他設定，例如端點清單和端點容錯移轉順序。

## <a name="disable-enable-or-delete-a-profile"></a>停用、啟用或刪除設定檔

您可以停用現有的設定檔，流量管理員就不會將使用者要求轉介到設定的端點。 當您停用流量管理員設定檔時，設定檔和設定檔內含的資訊將保持不變，而且可在流量管理員介面中進行編輯。  當您重新啟用設定檔時，就會繼續轉介。 當您在 Azure 傳統入口網站中建立流量管理員設定檔時，它會自動啟用。 如果您決定不再需要某個設定檔，可以將它刪除。

### <a name="to-disable-a-profile"></a>停用設定檔

1. 如果您使用的是自訂網域名稱，則變更網際網路 DNS 伺服器上的 CNAME 記錄，它就不會再指向流量管理員設定檔。
2. 流量會停止導向至透過流量管理員設定檔設定的端點。
3. 選取您想要停用的設定檔。 在 [流量管理員] 頁面上，按一下設定檔名稱旁邊的欄反白顯示設定檔。 請注意，按一下設定檔的名稱或名稱旁的箭號會帶開啟設定檔的設定頁面。
4. 選取設定檔之後，按一下頁面底部的 [ **停用** ]。

### <a name="to-enable-a-profile"></a>啟用設定檔

1. 選取您想要停用的設定檔。 在 [流量管理員] 頁面上，按一下設定檔名稱旁邊的欄反白顯示設定檔。 請注意，按一下設定檔的名稱或名稱旁的箭號會帶開啟設定檔的設定頁面。
2. 選取設定檔之後，按一下頁面底部的 [ **啟用** ]。
3. 如果您使用的是自訂網域名稱，請在網際網路 DNS 伺服器上建立 CNAME 資源記錄，以指向流量管理員設定檔的網域名稱。
4. 流量會再次導向至各端點。

### <a name="to-delete-a-profile"></a>刪除設定檔

1. 請確定網際網路 DNS 伺服器上的 DNS 資源記錄所使用的 CNAME 資源記錄，不再指向流量管理員設定檔的網域名稱。
2. 選取您想要停用的設定檔。 在 [流量管理員] 頁面上，按一下設定檔名稱旁邊的欄反白顯示設定檔。 請注意，按一下設定檔的名稱或名稱旁的箭號會帶開啟設定檔的設定頁面。
3. 選取設定檔之後，按一下頁面底部的 [ **刪除** ]。

## <a name="view-traffic-manager-profile-change-history"></a>檢視流量管理員設定檔變更歷程記錄

您可以在 Azure 傳統入口網站的 [管理服務] 中，檢視流量管理員設定檔的變更歷程記錄。

### <a name="to-view-your-traffic-manager-change-history"></a>檢視您的流量管理員變更歷程記錄

1. 在 Azure 傳統入口網站的左窗格中，按一下 [管理服務] 。
2. 在管理服務頁面上，按一下 [ **作業記錄**]。
3. 在 [作業記錄] 頁面上，您可以進行篩選，以檢視流量管理員設定檔的變更歷程記錄。 選取篩選選項之後，按一下勾號以檢視結果。

   * 若要檢視所有設定檔的變更，請選取您的訂用帳戶和時間範圍，然後從 [類型] 捷徑功能表中選取 [流量管理員]。
   * 若要依設定檔名稱進行篩選，請在 [ **服務名稱** ] 欄位中鍵入設定檔名稱，或從捷徑功能表中選取。
   * 若要檢視每個個別變更的詳細資訊，請選取包含您要檢視之變更的資料列，然後按一下頁面底部的 [ **詳細資料** ]。 在 [作業詳細資料] 視窗中，您可以檢視已建立或更新為作業一部分之 API 物件的 XML 表示法。

## <a name="next-steps"></a>後續步驟

* [新增端點。](traffic-manager-endpoints.md)
* [設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)
* [設定循環配置資源路由方法](traffic-manager-configure-round-robin-routing-method.md)
* [設定效能路由方法](traffic-manager-configure-performance-routing-method.md)
* [將公司網際網路網域指向流量管理員網域名稱](traffic-manager-point-internet-domain.md)
* [疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO5-->


