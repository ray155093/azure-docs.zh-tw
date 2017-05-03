---
title: "Linux 上的 Azure App Service Web Apps 常見問題集 | Microsoft Docs"
description: "Linux 上的 Azure App Service Web Apps 常見問題集。"
keywords: "azure app service, web 應用程式, 常見問題集, linux, oss"
services: app-service
documentationCenter: 
authors: ahmedelnably
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
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d9410448952438d6b9d437b7ca8823d4f196a2d6
ms.lasthandoff: 04/22/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Linux 上的 Azure App Service Web Apps 常見問題集

隨著 Linux 上的 Azure App Service (目前為預覽版) 的推出，我們正著手在我們的平台上新增功能和進行改善。 以下是過去幾個月以來，我們的客戶一直詢問的一些常見問題 (FAQ)。
如果您有問題，請對文章發表評論，我們會盡速回答。

## <a name="built-in-images"></a>內建映像

**問︰**我想要將平台所提供的內建 Docker 容器進行分岔。 哪裡可以找到這些檔案？

**答：**您可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 檔案。 您可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

**問︰**設定執行階段堆疊時，在 [啟動檔案] 區段應該使用哪些值？

**答︰**針對 Node.Js，您需指定 PM2 組態檔或指令碼檔案。 針對 .Net Core，請指定已編譯的 DLL 名稱。 針對 Ruby，您可以指定要用來將應用程式初始化的 Ruby 指令碼。

## <a name="management"></a>管理

**問︰**我在 Azure 入口網站中按下 [重新啟動] 按鈕，但我的 Web 應用程式並未重新啟動。 為什麼會這樣？

**答︰**我們正努力要在近期啟用 [重新啟動] 按鈕。 目前，您有兩個選擇：
- 新增或變更虛擬應用程式設定。 這會強制您的 Web 應用程式重新啟動。
- 先停止再啟動您的 Web 應用程式。

**問：**我是否可以使用「安全殼層」(SSH) 來連線到應用程式容器虛擬機器 (VM)？

**答：**否。 我們會在將來的版本中，提供可使用 SSH 來連線到您應用程式容器的方法。

## <a name="continuous-integrationdeployment"></a>連續整合/部署

**問︰**我的 Web 應用程式在我已更新 Docker Hub 上的映像之後，仍然使用舊的 Docker 容器映像。 您是否支援自訂容器的連續整合/部署？

**答：**您可以將 Web 應用程式先停止再啟動，來重新整理容器。 您也可以變更或新增虛擬應用程式設定，來強制重新整理您的容器。 我們正計劃在未來的版本中，為自訂容器提供連續整合/部署功能。

## <a name="language-support"></a>語言支援

**問︰**是否支援未編譯的 .NET Core 應用程式？

**答：**否。 您必須部署具有所有相依性的已編譯 .NET Core 應用程式。 我們正計劃在未來的版本中，提供完整的部署和建置體驗。

**問︰**您是否支援以 Composer 做為 PHP 應用程式的相依性管理程式？

**答：**否。 您必須部署具有所有相依性的 PHP 應用程式。 我們計劃在未來的版本中提供完整的部署體驗。

## <a name="custom-containers"></a>自訂容器

**問︰**我使用自己的自訂容器。 我的應用程式位於 \home\ 目錄中，但是我使用 [SCM 網站](https://github.com/projectkudu/kudu)或 FTP 用戶端來瀏覽內容時卻找不到檔案。 我的檔案在哪裡？

**答︰**我們在 \home\ 目錄掛接了 SMB 共用。 這會覆寫該處的所有內容。

**問︰**私人登錄伺服器 URL 的格式為何？

**答︰**您需要輸入包括 "http://" 或 "https://" 的完整登錄 URL。

**問︰**私人登錄選項中的映像名稱格式為何？

**答︰**您需要新增包括私人登錄 URL 的完整映像名稱 (例如 myacr.azurecr.io/dotnet:latest) myacr.azurecr.io/dotnet:latest)

**問︰**我想要在我的自訂容器映像上公開多個連接埠。 是否可行？

**答：**目前不支援此做法。

**問︰**我可以攜帶自己的儲存體嗎？

**答：**目前不支援此做法。

**問︰**我無法從 SCM 網站瀏覽自訂容器的檔案系統或執行中處理序。 這是為什麼？

**答︰**SCM 網站是在個別的容器中執行。 您無法檢查應用程式容器的檔案系統或執行中的處理程序。

**問︰**我的自訂容器接聽連接埠 80 以外的連接埠。 如何設定我的應用程式將要求路由至該連接埠？

**答︰**您可以指定一個叫做 [連接埠] 的應用程式設定，為它提供一個預期的連接埠號碼值。

**問︰**我是否需要在我的自訂容器中實作 HTTPS。

**答︰**不需要，平台會在共用的前端處理 HTTPS 終止。

## <a name="pricing-and-sla"></a>價格和 SLA

**問︰**使用公開預覽版時是價格是多少？

**答︰**我們會以標準 Azure App Service 定價，收取您應用程式執行時數一半的費用。 這表示您會獲得標準 Azure App Service 定價的五折優惠。

## <a name="other"></a>其他

**問︰**應用程式設定名稱所支援的字元為何？

**答︰**針對應用程式設定，您只能使用 A-Z、a-z、0-9 以及底線。

**問︰**我可以在何處提出新功能的要求？

**答︰**您可以在 [Web Apps 意見反應論壇 (英文)](https://aka.ms/webapps-uservoice) 提交您的想法。 請在您想法的標題中加上 "[Linux]"。

## <a name="next-steps"></a>後續步驟
* [何謂 Linux 上的 App Service？](app-service-linux-intro.md)
* [在 Linux 上的 App Service 中建立 Web 應用程式](app-service-linux-how-to-create-a-web-app.md)

