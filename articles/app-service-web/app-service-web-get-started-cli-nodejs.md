---
title: "在&5; 分鐘內將您的第一個 Web 應用程式部署到 Azure | Microsoft Docs"
description: "藉由部署範例 App，了解在 App Service 中執行 Web 應用程式有多麼簡單。 快速開始進行真正的開發，並立即查看結果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 65c9bdd9-8763-4c56-8e15-f790992e951e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: d8f3c95852361fd92eada1e6a50d735cc305279d
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>在&5; 分鐘內，將您的第一個 Web 應用程式部署到 Azure
本教學課程將協助您部署您的第一個 Web 應用程式至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。
您可以使用 App Service 來建立 Web 應用程式、[行動應用程式後端](/documentation/learning-paths/appservice-mobileapps/)和 [Web 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)。

您將： 

* 在 Azure App Service 中建立 Web 應用程式。
* 部署範例程式碼 (在 ASP.NET、PHP、Node.js、Java 或 Python 之間選擇)。
* 看見您的程式碼在生產環境中即時執行。
* 以您 [推送 Git 認可](https://git-scm.com/docs/git-push)的相同方式，更新 Web 應用程式。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](app-service-web-get-started-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0](app-service-web-get-started.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="prerequisites"></a>必要條件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI](../xplat-cli-install.md)。
* Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 您可以[試用 App Service](https://azure.microsoft.com/try/app-service/)，而不需要 Azure 帳戶。 建立入門 App，並試用長達一小時。不需要信用卡，也不需簽定合約。
> 
> 

## <a name="deploy-a-web-app"></a>部署 Web 應用程式
將 Web 應用程式部署至 Azure App Service。

1. 開啟新的 Windows 命令提示字元、PowerShell 視窗、Linux Shell 或 OS X 終端機。 執行 `git --version` 和 `azure --version`，確認電腦上已安裝 Git 和 Azure CLI。
   
    ![測試已在 Azure 中針對您的第一個 Web 應用程式安裝 CLI 工具](./media/app-service-web-get-started/1-test-tools.png)
   
    如果您尚未安裝工具，請參閱 [必要條件](#Prerequisites) 以取得下載連結。
2. 如下所示，登入 Azure：
   
        azure login
   
    依照說明訊息進行來繼續登入程序。
   
    ![登入 Azure 以建立第一個 Web 應用程式](./media/app-service-web-get-started/3-azure-login.png)
3. 將 Azure CLI 切換至 ASM 模式，然後設定 App Service 的部署使用者。 稍後您將使用認證來部署程式碼。
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. 切換至工作目錄 (`CD`)，並如下所示複製範例應用程式︰
   
        git clone <github_sample_url>
   
    ![複製 App 範例程式碼，用於您在 Azure 中的第一個 Web 應用程式](./media/app-service-web-get-started/2-clone-sample.png)
   
    對於 &lt;github_sample_url>，使用下列其中一個 URL (視您所需的架構而定)：
   
   * HTML+CSS+JS： [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
   * ASP.NET： [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
   * PHP (CodeIgniter)： [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
   * Node.js (Express)： [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
   * Java： [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
   * Python (Django)： [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

5. 切換至範例 App 的儲存機制。 例如：
   
        cd app-service-web-html-get-started

6. 在 Azure 中，以唯一的應用程式名稱和您稍早設定的部署使用者，建立 App Service 應用程式資源。 出現提示時，指定所需的區域數目。
   
        azure site create <app_name> --git --gitusername <username>
   
    ![在 Azure 中建立第一個 Web 應用程式的 Azure 資源](./media/app-service-web-get-started/4-create-site.png)
   
    您的應用程式現在已建立於 Azure 中。 此外，您目前的目錄也已進行 Git 初始化並連接到新的 App Service 應用程式而成為 Git 遠端。
    您可以瀏覽至應用程式 URL (http://&lt;app_name>.azurewebsites.net) 來查看美麗的預設 HTML 網頁，但現在就讓我們實際將程式碼放在那裡。

7. 將範例程式碼部署至 Azure App，如同使用 Git 推送任何程式碼一樣。 出現提示時，使用您稍早設定的密碼。
   
        git push azure master
   
    ![將程式碼推送至您在 Azure 中的第一個 Web 應用程式](./media/app-service-web-get-started/5-push-code.png)
   
    如果您已使用其中一個語言架構，則會看到不同的輸出。 `git push` 不只將程式碼放在 Azure 中，也會在部署引擎中觸發部署工作。 如果您的專案 (儲存機制) 根目錄中有任何 package.json (Node.js) 或 requirements.txt (Python) 檔案，或您的 ASP.NET 專案中有 packages.config 檔案，則部署指令碼會為您還原必要的封裝。 您也可以 [啟用編輯器延伸模組](web-sites-php-mysql-deploy-use-git.md#composer) ，以在 PHP 應用程式中自動處理 composer.json 檔案。

恭喜，您的應用程式已部署至 Azure App Service。

## <a name="see-your-app-running-live"></a>看見您的應用程式即時執行
若要查看 Azure 中即時執行的應用程式，請從儲存機制中的任何目錄執行此命令︰

    azure site browse

## <a name="make-updates-to-your-app"></a>更新您的應用程式
您現在可以使用 Git 隨時從您的專案 (儲存機制) 根目錄進行推送，以更新即時網站。 您可以使用第一次部署程式碼時的相同方式來執行這項作業。 例如，每次您想要推送已在本機測試的新變更時，只需從專案 (儲存機制) 根目錄執行下列命令︰

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>後續步驟
針對您的語言架構，尋找偏好的開發和部署步驟：

> [!div class="op_single_selector"]
> * [.NET](web-sites-dotnet-get-started.md)
> * [PHP](app-service-web-php-get-started-cli-nodejs.md)
> * [Node.js](app-service-web-nodejs-get-started-cli-nodejs.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> * [Java](web-sites-java-get-started.md)
> 
> 

或者，進一步運用您的第一個 Web 應用程式。 例如：

* 嘗試 [將程式碼部署至 Azure 的其他方法](web-sites-deploy.md)。 例如，若要從您的其中一個 GitHub 儲存機制中部署，只需在 [部署選項] 中改為選取 [GitHub] 而不是 [本機 Git 儲存機制] 即可。
* 加強您 Azure App 的功能。 驗證您的使用者。 根據需求加以調整。 設定一些效能警示。 都只要點幾下滑鼠就能完成。 請參閱 [在您的第一個 Web 應用程式中新增功能](app-service-web-get-started-2.md)。


