---
title: "Azure IoT Suite 常見問題集 | Microsoft Docs"
description: "IoT 套件的常見問題集"
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 69d4439a20fec8caaabdb43444cabd81f7d1b7c8
ms.openlocfilehash: 6245c2a0f73ff63e3bfb5fe16112f89ed0efdf30


---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT 套件的常見問題集
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 入口網站中刪除資源群組，與在 azureiotsuite.com 中對預先設定解決方案按一下 [刪除] 之間的差異為何？
* 如果您在 [azureiotsuite.com][lnk-azureiotsuite] 中刪除預先設定的解決方案，將會刪除您在建立該預先設定解決方案時所佈建的一切資源。 將額外資源新增到資源群組時，也會一併刪除這些資源。 
* 如果您在 [Azure 入口網站][lnk-azure-portal]中刪除資源群組，則只會刪除該資源群組中的資源。 您還必須在 [Azure 傳統入口網站][lnk-classic-portal]中刪除與預先設定的解決方案關聯的 Azure Active Directory 應用程式。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 IoT 中樞執行個體？
10 個。 您可以建立 [Azure 支援票證][link-azuresupportticket]來提高此限制，但每一個訂用帳戶預設只能佈建 10 個「IoT 中樞」，如 [Azure 訂用帳戶限制][link-azuresublimits]所述。 結果就是，由於每個預先設定的解決方案會佈建一個新的「IoT 中樞」，因此在一個指定的訂用帳戶中，您最多只能佈建 10 個預先設定的解決方案。 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 DocumentDB 執行個體？
50 個。 您可以建立 [Azure 支援票證][link-azuresupportticket]來提高此限制，但每一個訂用帳戶預設只能佈建 50 個 DocumentDB 執行個體。 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？
2 個。 您只可以在 Azure 訂用帳戶中針對 Enterprise 方案建立兩個內部交易層級 1 Bing 地圖。 根據預設，遠端監視方案會隨著內部交易層級 1 方案一起佈建。 因此，您只可以在一個訂用帳戶中最多佈建&2; 個遠端監視方案。

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>我有使用靜態地圖的遠端監視解決方案部署，如何加入互動式 Bing 地圖？
1. 從 [Azure 入口網站][lnk-azure-portal]取得 Bing Maps API for Enterprise QueryKey： 
   
   1. 在 [Azure 入口網站][lnk-azure-portal]中瀏覽至 Bing Maps API for Enterprise 所在的資源群組。
   2. 依序按一下 [所有設定] 和 [金鑰管理]。 
   3. 您可以看到兩個金鑰︰**MasterKey** 和 **QueryKey**。 請複製 **QueryKey** 的值。
      
      > [!NOTE]
      > 沒有 Bing Maps API for Enterprise 帳戶嗎？ 請在 [Azure 入口網站][lnk-azure-portal]中按一下 [+ 新增]、搜尋 Bing Maps API for Enterprise，然後依照提示來建立一個帳戶。
      > 
      > 
2. 從 [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] 下拉最新的程式碼。
3. 依照儲存機制的 /docs/ 資料夾中的命令列部署指引，執行本機或雲端部署。 
4. 執行本機或雲端部署之後，在您的根資料夾中尋找部署期間所建立的 *.user.config。 在文字編輯器中開啟這個檔案。 
5. 變更下列這一行，使其包含從 **QueryKey** 複製的值︰ 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我有 Microsoft Azure for DreamSpark，是否可以建立預先設定的解決方案？
目前，您無法使用 [Microsoft Azure for DreamSpark][lnk-dreamspark] 帳戶來建立預先設定的解決方案。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶][lnk-30daytrial]，此帳戶可讓您建立預先設定的解決方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何刪除 AAD 租用戶？
請參閱 Eric Golpe 的部落格文章：[刪除 Azure AD 租用戶的逐步解說 (英文)][lnk-delete-aad-tennant]。

### <a name="next-steps"></a>後續步驟
您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [預先設定的預防性維護解決方案概觀][lnk-predictive-overview]
* [從頭建立 IoT 安全性][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Jan17_HO1-->


