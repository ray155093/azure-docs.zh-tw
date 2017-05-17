---
title: "在 Linux 上使用 Azure Web 應用程式連續部署 Docker 中樞 | Microsoft Docs"
description: "如何在 Linux 的 Azure Web 應用程式中設定連續部署。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>在 Linux 上使用 Azure Web 應用程式連續部署 Docker 中樞

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

在本教學課程中，您可以從 [Docker 中樞](https://hub.docker.com)設定自訂容器映像的連續部署。

## <a name="step-1---log-in-to-azure"></a>步驟 1 - 登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>步驟 2 - 啟用 Docker 中樞連續部署

在 **[Azure 入口網站](https://portal.azure.com/)**中，按一下頁面左側的 [App Service] 選項。

按一下您想要設定 Docker 中樞連續部署的應用程式名稱。

在 [應用程式設定] 中，新增名為 `DOCKER_ENABLE_CI` 且包含 `true` 值的應用程式設定。

![將應用程式設定的影像插入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>步驟 3 - 將 web 攔截新增至 Docker 中樞

在您的 [Docker 中樞] 頁面上，依序按一下 [Webhook]、[建立 WEBHOOK]。

![將新增 webhook 1 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

針對 Webhook URL，您必須擁有下列端點︰`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

您可以取得 `publishingusername` 和 `publishingpwd`，方法是使用 Azure 入口網站來下載 Web 應用程式發佈設定檔。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

當更新映像時，Web 應用程式會以新的映像自動進行更新。

## <a name="next-steps"></a>後續步驟
* [什麼是 Linux 上的 Azure Web 應用程式？](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 應用程式中建立應用程式](./app-service-linux-how-to-create-web-app.md)
* [在 Linux 上的 Azure Web 應用程式中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure Web 應用程式中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure Web 應用程式中使用 Ruby](app-service-linux-ruby-get-started.md)
* [如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像](./app-service-linux-using-custom-docker-image.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](./app-service-linux-faq.md) 




