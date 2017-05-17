---
title: "使用 Linux 上的 Azure App Service Web 應用程式建立 Ruby 應用程式 | Microsoft Docs"
description: "了解如何使用 Linux 上的 Azure App Service Web 應用程式來建立 Ruby 應用程式。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>使用 Linux 上的 Azure Web 應用程式建立 Ruby 應用程式 - 預覽

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概觀

本教學課程會示範如何在本機建立基本的 Ruby on Rails 應用程式，並將它部署至 Linux 上的 Azure Web 應用程式。

## <a name="prerequisites"></a>必要條件

* 安裝在您開發電腦上的 [Ruby 2.3.3 或更新版本](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller)。
* [Git](https://git-scm.com/downloads) 已安裝在您的開發電腦上
* [有效的 Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)
* 本教學課程是以 Ubuntu 環境的內容所撰寫。 所有系統命令都是 bash 特定。


## <a name="create-a-new-local-rails-application"></a>建立新的本機 rails 應用程式

1. 為新的應用程式建立目錄，並變更至該目錄。

        mkdir ~/workspace
        cd ~/workspace

2. 使用 `ruby -v` 命令將 Ruby 初始化並檢查版本。

    ![Ruby init](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. 使用 `gem install rails` 命令來安裝 rails。

    ![安裝 rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. 使用下列命令來建立名為 **hello-world** 的 rails 應用程式︰

    如果您是使用 Rails 5.1.0 或更新版本中，請包含 `--skip-yarn` 選項，如下列命令中所示︰

        rails new hello-world --skip-yarn

    如需 Rails 5.1.0 之前的版本，請使用下列命令︰

        rails new hello-world 

    ![新增 Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![新增 Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    如果您是使用 Rails 5.1 以上版本，如果未使用 `--skip-yarn` 選項，就會建立一個 package.json。 我們不希望它包含於我們的部署。 或者，您可以刪除 package.json 檔案，或將它新增至目錄中的 .git-ignore 檔案，如下所示︰ 

        # Ignore package.json
        /package.json

5. 變更為 hello-world 目錄，然後啟動伺服器。

        cd hello-world
        rails server

    ![啟動 Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. 使用網頁瀏覽器，瀏覽至 `http://localhost:3000`，在本機測試應用程式。    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>準備 Azure 的應用程式

依預設，Ruby 映像會使用 `-e production` 旗標來執行伺服器。 此環境會要求在 Linux 上設定 Azure Web 應用程式。 容器會負責一部分的設定 (例如，設定 `SECRET_KEY_BASE`)。 您必須設定預設路由。 否則，您瀏覽網站時會收到 404 錯誤。

若要設定預設路由︰

1. 開啟 ~/workspace/hello-world/config/routes.rb 進行編輯。 如以下螢幕擷取畫面所示，新增以下這行。 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. 開啟 ~/workspace/hello-world/app/controllers/application_controller.rb 進行編輯。 如以下螢幕擷取畫面所示，新增以下這行。

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. 現在已設定好您的應用程式。 使用網頁瀏覽器，瀏覽至 `http://localhost:3000`，確認根登陸頁面。

    ![已設定 Hello World](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>在 Azure 上建立 Ruby 網站

1. 瀏覽至 [Azure 入口網站](http://portal.azure.com)，並使用您的訂用帳戶登入。 新增 **Linux 上的 Web 應用程式**，如下列螢幕擷取畫面所示：

    ![在 Linux 上建立 Web 應用程式](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. 隨即開啟 [建立] 刀鋒視窗，如下列螢幕擷取畫面所示︰

    ![[建立] 刀鋒視窗](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. 命名您的 Web 應用程式。
    2. 選擇現有的資源群組或建立新群組。 (請參閱[限制](app-service-linux-intro.md)一節中可用的區域。)
    3. 選擇現有的 Azure App Service 方案或建立新方案。 (請參閱[限制](app-service-linux-intro.md)一節中的 App Service 方案附註。)
    4. 選擇 **Ruby 2.3** 內建執行階段堆疊作為容器設定。
    5. 對 web 應用程式按一下 [釘選到儀表板]。
    6. 按一下 [建立] 。

3. 建立 Web 應用程式後，隨即出現 [概觀] 刀鋒視窗。 請複製新 Web 應用程式的 **URL**，然後在瀏覽器中將它開啟。 隨即出現下列啟動顯示畫面。

    ![啟動顯示畫面](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>部署應用程式

在本教學課程中，我們會使用 Git 將本機 Ruby 應用程式部署至 Azure。

1. 新的 Azure 網站已設定 Git 部署。 將您的 Web 應用程式名稱插入後，瀏覽至下列 URL 就可以找到 Git 部署 URL︰

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    根據您的 web 應用程式名稱，Git URL 具有下列格式︰

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. 執行下列命令，將本機應用程式部署至您的 Azure 網站。

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    確認遠端部署作業報告成功。

    ![部署 Web 應用程式](./media/app-service-linux-ruby-get-started/deployment-success.png)

    如果您看到錯誤訊息指出遠端已掛斷，部署可能仍在進行中。 在此情況下，請在瀏覽器中瀏覽至下列 URL︰

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. 一旦部署完成後，需重新啟動 Web 應用程式進行部署，才會生效。 在 [Azure 入口網站](http://portal.azure.com)中，瀏覽至您 Web 應用程式的 [概觀] 刀鋒視窗。

    按一下工具列上的 [重新啟動]。

    ![重新啟動 Web 應用程式](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. 瀏覽至您的網站，並確認即時更新。 

    當應用程式重新啟動時，嘗試瀏覽網站會導致 HTTP 狀態碼錯誤 503 (伺服器無法使用)。 可能需要幾分鐘才能完全重新啟動。

        http://{your web app name}.azurewebsites.net

    ![更新的 Web 應用程式](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>後續步驟
如需 Linux 上的 Azure App Service Web 應用程式相關資訊，請參閱下列連結。 您也可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

* [在 Linux 上的 App Service 中建立 Web Apps](app-service-linux-how-to-create-web-app.md)
* [如何針對 Linux 上的 App Service 使用自訂 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure App Service Web Apps 中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [Linux 上的 Azure App Service Web Apps 常見問題集](app-service-linux-faq.md)


