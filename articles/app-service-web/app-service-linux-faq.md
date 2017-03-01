---
title: "Linux 上的 Azure App Service Web Apps 常見問題集 | Microsoft Docs"
description: "Linux 上的 Azure App Service Web Apps 常見問題集。"
keywords: "azure app service, web 應用程式, 常見問題集, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 831ef097027721146531e8d699fe3f67417a57ea
ms.openlocfilehash: b88aa3d0ae89aec81c2b9144fb5de3210a0b8d1e
ms.lasthandoff: 02/18/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Linux 上的 Azure App Service Web Apps 常見問題集 #

隨著 Linux 上的 Azure App Service (目前為預覽版本) 的推出，我們正著手在我們的平台上新增功能和進行改善。 以下是過去幾個月以來，我們的客戶曾經問過的一些常見問題。
如果您有問題，請對文章發表評論，我們會盡速回答。

## <a name="built-in-images"></a>內建映像 ##

**問︰**我想要將平台所提供的內建 Docker 容器進行分岔。 哪裡可以找到這些檔案？

**答︰**所有 Docker 檔案都可以在這裡找到︰https://github.com/azure-app-service。 所有 Docker 容器都可以在這裡找到︰https://hub.docker.com/u/appsvc/。

## <a name="management"></a>管理 ##

**問︰**我在入口網站中按下 [重新啟動] 按鈕，但我的 WebApp 並未重新啟動，為什麼會這樣？

**答︰**我們正著手要在不久的將來啟用 [重設] 按鈕，現在您有兩個選項。
1. 新增或變更虛擬應用程式設定，這會強制您的 Webapp 重新啟動。 
2. 先停止再啟動您的 Webapp。

**問︰**我可以透過 SSH 連接到 VM 嗎？

**答︰**不行，我們會在將來的版本中提供方法讓您透過 SSH 連線到您的應用程式容器。

## <a name="continuous-integration--deployment"></a>連續整合/部署 ##

**問︰**我的 Webapp 在更新 DockerHub 上的映像之後仍然使用舊的 Docker 容器映像？ 您是否支援自訂容器的連續整合/部署？

**答︰**若要重新整理容器，您可以先停止再啟動您的 Web 應用程式，或是變更/新增虛擬應用程式設定來強制重新整理容器，我們會在將來的版本中提供自訂容器的 CI/CD 功能。

## <a name="language-support"></a>語言支援 ##

**問︰**是否支援未編譯的 .NET Core 應用程式？

**答︰**否，您必須部署具有所有相依性的已編譯 .NET Core 應用程式，完整的部署和建置體驗會在將來的版本中推出。

## <a name="built-in-images"></a>內建映像 ##

**問︰**設定執行階段堆疊時，啟動檔案區段中預期的值是什麼

**答︰**若是 Node.Js，您可以指定 PM2 組態檔或指令碼檔案。 若是 .Net Core，您應該指定已編譯的 dll 名稱。 若是 Ruby，您可以指定您想要用來初始化您的應用程式的 Ruby 指令碼。

## <a name="custom-containers"></a>自訂容器 ##

**問︰**我使用我自己的自訂容器。 我的應用程式位於 \home\ 目錄中，但是當我使用 SCM 網站或 ftp 用戶端瀏覽內容時卻找不到檔案。 我的檔案在哪裡？

**答︰**我們在 \home\ 目錄掛接了 SMB 共用，因此會將其中的所有內容覆寫。

**問︰**我想要在我的自訂容器映像上公開多個連接埠。 是否可行？

**答︰**目前不支援。

**問︰**我可以攜帶自己的儲存體嗎？

**答︰**目前不支援。

**問︰**我無法從 SCM 網站瀏覽自訂容器的檔案系統或執行中處理序。 這是為什麼？

**答︰**SCM 網站會在不同的容器中執行，您無法檢查應用程式容器的檔案系統或執行中處理序。

**問︰**我的自訂容器會接聽連接埠 80 以外的連接埠。 如何設定我的應用程式將要求路由至該連接埠？

**答︰**您可以指定應用程式的**連接埠**設定，指定其值為您期望的連接埠號碼。

## <a name="pricing-and-sla"></a>價格和 SLA ##

**問︰**公開預覽期間的價格為何？

**答︰**我們會收取應用程式執行時數一半的費用，加上標準的 Azure App Service 價格，實際上就是標準 Azure App Service 價格打五折。

## <a name="other"></a>其他 ##

**問︰**應用程式設定名稱所支援的字元為何？

**答︰**您只能對應用程式設定使用 A-Z、a-z、0-9 以及底線。

**問︰**我可以在何處提出新功能的要求？

**答︰**您可以在此提交您的想法︰https://aka.ms/webapps-uservoice。 請在想法的標題中加上 [Linux]。

## <a name="next-steps"></a>後續步驟
* [何謂 Linux 上的 App Service？](app-service-linux-intro.md)
* [在 Linux 上的 App Service 中建立 Web Apps](app-service-linux-how-to-create-a-web-app.md)

