---
title: "在 5 分鐘內將靜態 HTML Web 應用程式建立在 Azure 中 | Microsoft Docs"
description: "藉由部署範例 App，了解在 App Service 中執行 Web 應用程式有多麼簡單。"
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>在 5 分鐘內將靜態 HTML Web 應用程式建立在 Azure 中

本快速入門會逐步解說如何開發基本 HTML+CSS 網站及部署至 Azure。 您將使用 [Azure App Service 方案](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)來執行應用程式，以及使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) 在其中建立 Web 應用程式。 您可使用 Git 將應用程式部署至 Azure。 安裝先決條件後，大約需要 5 分鐘才能完成本教學課程。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>必要條件

建立這個範例之前，請下載並安裝下列元件︰

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下載範例

在終端機視窗中，將範例應用程式存放庫複製到本機電腦：

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>HTML 檢視

瀏覽至包含範例 HTML 的目錄。 在瀏覽器中開啟 *index.html* 檔案。

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

在 `quickStartPlan` App Service 方案中建立 [Web 應用程式](app-service-web-overview.md)。 Web 應用程式會為您的程式碼提供裝載空間，以及提供 URL 讓您檢視已部署的應用程式。

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

此頁面目前作為 Azure App Service Web 應用程式執行︰

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>更新和重新部署應用程式

開啟 *index.html* 檔案。 對標記進行變更。 例如，將 `Hello world!` 變更為 `Hello Azure!`

在 Git 中認可您的變更，然後將程式碼變更推送至 Azure。

```bash
git commit -am "updated HTML"
git push azure master
```

完成部署後，重新整理瀏覽器以查看變更。

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

- 探索範例 [Web Apps CLI 指令碼](app-service-cli-samples.md)。
- 了解如何將[自訂網域名稱](app-service-web-tutorial-custom-domain.md) (例如 contoso.com) 對應至 [App Service 應用程式](app-service-web-tutorial-custom-domain.md)。
