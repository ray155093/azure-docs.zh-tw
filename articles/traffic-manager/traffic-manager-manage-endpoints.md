---
title: "在 Azure 流量管理員中管理端點 | Microsoft Docs"
description: "本文將協助您從 Azure 流量管理員加入、移除、啟用和停用端點。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 52f6d4f3e68e5eb120ee499827cc8549b8e547fd

---

# <a name="add-disable-enable-or-delete-endpoints"></a>新增、停用、啟用或刪除端點

不論網站模式為何，Azure App Service 中的 Web Apps 功能已為資料中心內的網站提供容錯移轉和循環配置資源流量路由功能。 Azure 流量管理員可讓您在不同的資料中心的網站和雲端服務中指定容錯移轉和循環配置資源流量路由。 提供該項功能的首要步驟是將雲端服務或網站端點新增至流量管理員。

> [!NOTE]
> 本文說明如何使用傳統入口網站。 Azure 傳統入口網站僅支援建立和指派雲端服務和 Web 應用程式做為端點。 新的 [Azure 入口網站](https://portal.azure.com)是慣用介面。

您也可以停用屬於流量管理員設定檔一部分的個別端點。 停用端點會將端點做為設定檔的一部分保留，但是設定檔會以好像未包含端點的方式運作。 此動作對於暫時移除處於維護模式、或被重新部署的端點而言很有用。 在重新啟動並執行端點之後，您可以將它啟用。

> [!NOTE]
> 停用端點會與其在 Azure 中的部署狀態無關。 健全的端點將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。 此外，在一個設定檔中停用端點並不會影響它在另一個設定檔中的狀態。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>若要新增雲端服務或網站端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔。 若要開啟 [設定] 頁面，按一下設定檔名稱右側的箭號。
2. 在頁面的頂端，按一下 [端點]  以檢視端點是否已是組態的一部分。
3. 在頁面的底部，按一下 [新增] 以存取 [新增服務端點] 頁面。 根據預設，這個頁面會列出 [服務端點] 下的雲端服務。
4. 若是雲端服務，請在清單中選取雲端服務以將他們做為此設定檔的端點新增。 清除雲端服務名稱會將它從端點清單中移除。
5. 若是網站，請按一下 [服務類型] 下拉式清單，然後選取 [Web 應用程式]。
6. 請在清單中選取網站以將他們做為此設定檔的端點啟用。 清除網站名稱會將它從端點清單中移除。 您只可以在每個 Azure 資料中心 (也稱為「區域」) 內選取一個網站。 當您選取第一個網站時，在相同資料中心內的其他網站會變成無法選取。 並請注意，只有標準網站才會被列出。
7. 選取此設定檔的端點之後，請按一下右下角的核取記號以儲存變更。

> [!NOTE]
> 在使用容錯移轉流量路由方法從設定檔新增或移除端點後，容錯移轉優先權清單可能無法依您想要的方式排序。 您可以在 [組態] 頁面上調整容錯移轉優先順序清單的順序。 如需詳細資訊，請參閱 [設定容錯移轉流量路由](traffic-manager-configure-failover-routing-method.md)。

## <a name="to-disable-an-endpoint"></a>若要停用端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔。 若要開啟 [設定] 頁面，按一下設定檔名稱右側的箭號。
2. 在頁面的頂端，按一下 [ **端點** ] 以檢視包含在組態中的端點。
3. 按一下您要停用的端點，然後按一下頁面底部的 [ **停用** ]。
4. 用戶端會繼續將流量傳送至端點以取得存留時間 (TTL) 持續時間。 您可以在流量管理員設定檔的 [組態] 頁面上變更 TTL。

## <a name="to-enable-an-endpoint"></a>若要啟用端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔。 若要開啟 [設定] 頁面，按一下設定檔名稱右側的箭號。
2. 在頁面的頂端，按一下 [ **端點** ] 以檢視包含在組態中的端點。
3. 按一下您要啟用的端點，然後按一下頁面底部的 [ **啟用** ]。
4. 用戶端會如設定檔指定導向啟用的端點。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>若要新增雲端服務或網站端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔。 若要開啟 [設定] 頁面，按一下設定檔名稱右側的箭號。
2. 在頁面的頂端，按一下 [端點]  以檢視端點是否已是組態的一部分。
3. 在 [端點] 頁面上，按一下您要從設定檔刪除的端點名稱。
4. 按一下頁面底部的 [刪除] 。

## <a name="next-steps"></a>後續步驟

* [管理流量管理員設定檔](traffic-manager-manage-profiles.md)
* [設定路由方法](traffic-manager-configure-routing-method.md)
* [疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)
* [流量管理員的效能考量](traffic-manager-performance-considerations.md)
* [流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO5-->


