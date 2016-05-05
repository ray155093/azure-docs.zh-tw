<properties
  pageTitle="Azure IoT Suite 常見問題集 | Microsoft Azure"
  description="IoT 套件的常見問題集"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="04/22/2016"
  ms.author="araguila"/>
   
# IoT 套件的常見問題集

### 在 Azure 入口網站中刪除資源群組，與在 azureiotsuite.com 中對預先設定解決方案按一下 [刪除] 之間的差異為何？

- 如果您在 [azureiotsuite.com][lnk-azureiotsuite] 中刪除預先設定的方案，則會刪除在建立預先設定的方案時所佈建的所有資源；如果您新增了其他資源到資源群組，這些資源也會一併刪除。 

- 如果您在 [Azure 入口網站中][lnk-azure-portal]刪除資源群組，您只刪除了該資源群組中的資源；您也必須在 [Azure 傳統入口網站][lnk-classic-portal]中刪除與預先設定解決方案相關聯的 Azure Active Directory 應用程式。

### 我可以在一個訂用帳戶中佈建多少個 DocumentDB 執行個體？

5 個。您可以建立 [Azure 支援票證][link-azuresupportticket]來提高此限制，但根據預設，每一個訂用帳戶只可以佈建 5 個 DocumentDB 執行個體。因此，您只可以在一個指定的訂用帳戶中最多佈建 5 個遠端監視預先設定的方案。

### 我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？

2 個。您只可以在一個訂用帳戶中建立 2 個免費 Bing 地圖 API。根據預設，遠端監視解決方案會隨著免費 Bing 地圖 API 一起佈建。因此，您只可以在一個訂用帳戶中最多佈建 2 個遠端監視方案。

### 我有使用靜態地圖的遠端監視解決方案部署，如何加入互動式 Bing 地圖？ 
1. 從 [Azure 入口網站][lnk-azure-portal]取得 Bing Maps API for Enterprise QueryKey： 
 1. 在 [Azure 入口網站][lnk-azure-portal]中瀏覽至您的 Bing Maps API for Enterprise 所在的資源群組。
 2. 按一下 [所有設定]，然後 [金鑰管理]。 
 3. 您會注意到兩個金鑰 ︰MasterKey 和 QueryKey。複製 QueryKey 的值。

     > [AZURE.NOTE] 沒有 Bing Maps API for Enterprise 帳戶嗎？ 按一下 [+ 新增]，搜尋 Bing Maps API for Enterprise 並依照提示執行，在 [Azure 入口網站][lnk-azure-portal]中建立一個帳戶。

2. 從 [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] 下拉最新的程式碼。

3. 遵循儲存機制的 /docs/ 資料夾中的命令列部署指引，執行本機或雲端部署。

4. 執行本機或雲端部署之後，在您的根資料夾中尋找部署期間所建立的 *.user.config。在文字編輯器中開啟這個檔案。

5. 變更下列這一行以包含您從 QueryKey 複製的值︰
   
  `<setting name="MapApiQueryKey" value="" />`


### 如何刪除 AAD 租用戶？

請參閱 Eric Golpe 的部落格文章：[刪除 Azure AD 租用戶的逐步解說][lnk-delete-aad-tennant]。

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0427_2016-->