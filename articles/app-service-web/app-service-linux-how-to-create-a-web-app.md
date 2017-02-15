---
title: "如何使用 Linux 上的 App Service 建立 Web 應用程式 | Microsoft Docs"
description: "針對 Linux 上的 App Service 建立 Web 應用程式的工作流程。"
keywords: "azure app service, web 應用程式, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d87315ec97631c7ad3f0163aeaa0abde11b7416


---
# <a name="create-a-web-app-with-app-service-on-linux"></a>使用 Linux 上的 App Service 建立 Web 應用程式
## <a name="use-the-azure-portal-to-create-your-web-app"></a>使用 Azure 入口網站建立 Web 應用程式
您可以從 [Azure 入口網站](https://portal.azure.com)開始建立 Linux 上的 Web 應用程式，如下圖所示︰

![在 Azure 入口網站上開始建立 Web 應用程式][1]

接著會開啟 [建立] 刀鋒視窗，如下圖所示︰

![[建立] 刀鋒視窗][2]

1. 命名您的 Web 應用程式。
2. 選擇現有的資源群組或建立新群組。 (請參閱[限制](app-service-linux-intro.md)一節中可用的區域。)
3. 選擇現有的 Azure App Service 方案或建立新方案。 (請參閱[限制](app-service-linux-intro.md)一節中的 App Service 方案附註。)
4. 選擇您想要使用的應用程式堆疊。 您可以從數個版本的 Node.js 和 PHP 中選擇。

建立應用程式後，您可以從應用程式設定中變更應用程式堆疊，如下圖所示︰

![應用程式設定][3]

## <a name="deploy-your-web-app"></a>部署 Web 應用程式
從管理入口網站中選擇 [部署選項] 時，您可以選擇使用本機 Git 或 GitHub 儲存機制來部署您的應用程式。 其餘指示類似於非 Linux Web 應用程式的情況，您可以在我們的[本機 Git 部署](app-service-deploy-local-git.md)或[持續部署](app-service-continuous-deployment.md)文章中依照這些指示來使用 GitHub。

您也可以使用 FTP 將應用程式上傳至您的網站。 您可以從 [診斷記錄檔] 區段取得 Web 應用程式的 FTP 端點，如下圖所示︰

![診斷記錄檔][4]

## <a name="next-steps"></a>後續步驟
* [何謂 Linux 上的 App Service？](app-service-linux-intro.md)
* [在 Linux 上的 Web Apps 中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png



<!--HONumber=Nov16_HO3-->


