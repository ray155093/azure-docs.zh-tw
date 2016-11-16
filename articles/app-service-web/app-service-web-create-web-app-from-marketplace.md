---
title: "從 Azure Marketplace 建立 Web 應用程式 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站，從 Azure Marketplace 建立新的 WordPress Web 應用程式。"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 73fafeff227061feae1fbc0a1c50e5056116e3ea


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>從 Azure Marketplace 建立 Web 應用程式
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace 提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。 例如，WordPress、Umbraco CMS、Drupal 等。這些 Web 應用程式建置在各種熱門架構上，例如本 WordPress 範例中的 [PHP]、[.NET]、[Node.js]、[Java] 和 [Python] 等等。 若要從 Azure Marketplace 建立 Web 應用程式，您唯一需要的軟體就是用於 [Azure 入口網站]的瀏覽器。

在本教學課程中，您將了解如何：

* 在 Azure App Service 中尋找和建立以 Azure Marketplace 範本為基礎的 Web 應用程式。
* 為新的 Web 應用程式進行 Azure App Service 設定。
* 啟動及管理您的 Web 應用程式。

基於本教學課程的目的，您將會從 Azure Marketplace 部署 WordPress 部落格網站。 當您完成本教學課程中的步驟時，您的專屬 WordPress 網站將在雲端中啟動並執行。

![WordPress Web 應用程式儀表板範例][WordPressDashboard1]

您在本教學課程中部署的 WordPress 網站使用 MySQL 來做為資料庫。 如果您想改為使用 SQL Database 來做為資料庫，請參閱 [專案 Nami]，其也可透過 Azure Marketplace 來取得。

> [!NOTE]
> 若要完成此教學課程，您需要 Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[啟用自己的 Visual Studio 訂閱者權益][啟用]，或是[申請免費試用][免費試用]。
> 
> 如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至 [試用 App Service]。 您可以於該處，在 App Service 中立即建立短期的入門 Web 應用程式 - 不需信用卡，不需任何承諾。
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>在 Azure App Service 中尋找及建立 Web 應用程式
1. 登入 [Azure 入口網站]。
2. 按一下 [新增] 。
   
    ![建立新的 Azure 資源][MarketplaceStart]
3. 搜尋 **WordPress**，然後按一下 [WordPress]。 (如果您想要使用 SQL Database，而不是 MySQL，請搜尋**專案 Nami**。)
   
    ![在 Marketplace 中搜尋 WordPress][MarketplaceSearch]
4. 在閱讀 WordPress 應用程式的描述之後，請按一下 [建立] 。
   
    ![建立 WordPress Web 應用程式][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>為新的 Web 應用程式進行 Azure App Service 設定
1. 建立新的 Web 應用程式之後，[WordPress 設定] 刀鋒視窗隨即會顯示，以供您完成下列步驟︰
   
    ![設定 WordPress Web 應用程式設定][ConfigStart]
2. 在 [Web 應用程式]  方塊中，輸入 Web 應用程式的名稱。
   
    此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net 。 如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。
   
    ![設定 WordPress Web 應用程式名稱][ConfigAppName]
3. 如果您有多個訂用帳戶，請選擇您想要使用的訂用帳戶。
   
    ![設定 Web 應用程式的訂用帳戶][ConfigSubscription]
4. 選取 [資源群組]  或建立新的資源群組。
   
    如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀][ResourceGroups]。
   
    ![設定 Web 應用程式的資源群組][ConfigResourceGroup]
5. 選取 [App Service 方案/位置]  ，或建立新的 App Service 方案/位置。
   
    如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀][AzureAppServicePlans]。
   
    ![設定 Web 應用程式的服務方案][ConfigServicePlan]
6. 按一下 [資料庫]，然後在 [新增 MySQL 資料庫] 刀鋒視窗中，提供設定 MySQL 資料庫所需的值。
   
    a. 輸入新名稱或保留預設名稱。
   
    b. 保留設為 [共用] 的 [資料庫類型]。
   
    c. 選擇與您選擇 Web 應用程式的同一位置。
   
    d. 選擇定價層。 對本教學課程來說，**Mercury** (可免費使用最少的連線數和磁碟空間) 已夠用。
   
    e. 在 [新增 MySQL 資料庫] 刀鋒視窗中，接受法律條款，然後按一下 [確定]。
   
    ![設定 Web 應用程式的資料庫設定][ConfigDatabase]
7. 在 [WordPress] 刀鋒視窗中，接受法律條款，然後按一下 [建立]。
   
    ![完成 Web 應用程式設定，然後按一下 [確定]][ConfigFinished]
   
    Azure App Service 即會建立 Web 應用程式，通常不到一分鐘。 您可以按一下入口網站頁面頂端的鐘圖示查看進度。
   
    ![進度指示器][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>啟動和管理 WordPress Web 應用程式
1. 當您完成建立 Web 應用程式時，請在 Azure 入口網站中瀏覽至您在其中建立應用程式的資源群組，然後您就能看到 Web 應用程式和資料庫。
   
    具有燈泡圖示的額外資源是 [Application Insights][ApplicationInsights]，它會提供 Web 應用程式的監視服務。
2. 在 [資源群組]  刀鋒視窗中，按一下 Web 應用程式列。
   
    ![選取您的 WordPress Web 應用程式][WordPressSelect]
3. 在 [Web 應用程式] 刀鋒視窗中，按一下 [瀏覽] 。
   
    ![瀏覽至您的 WordPress Web 應用程式][WordPressBrowse]
4. 如果系統提示您選取 WordPress 部落格的語言，請選取您想要的語言，然後按一下 [繼續] 。
   
    ![設定 WordPress Web 應用程式的語言][WordPressLanguage]
5. 在 WordPress 的 [歡迎使用] 頁面中，輸入 WordPress 所需的組態資訊，然後按一下 [安裝 WordPress]。
   
    ![設定 WordPress Web 應用程式的設定][WordPressConfigure]
6. 使用您已在 [歡迎使用]  頁面上建立的認證登入。  
7. 您網站的 [儀表板] 頁面便會開啟，並顯示您所提供的資訊。    
   
    ![檢視您的 WordPress 儀表板][WordPressDashboard2]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何從 Azure Marketplace 中建立及部署範例 Web 應用程式。

如需如何使用 App Service Web Apps 的詳細資訊，請參閱頁面左邊上的連結 (適用於寬瀏覽器視窗) 或頁面頂端的連結 (適用於窄瀏覽器視窗)。

如需在 Azure 上開發 WordPress Web 應用程式的詳細資訊，請參閱[在 Azure App Service 上開發 WordPress][WordPressOnAzure]。

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[啟用]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[免費試用]: https://azure.microsoft.com/pricing/free-trial/
[試用 App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure 入口網站]: https://portal.azure.com/
[專案 Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Nov16_HO2-->


