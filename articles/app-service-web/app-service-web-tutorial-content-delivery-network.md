---
title: "將 Web 應用程式連線至內容傳遞網路 | Microsoft Docs"
description: "將 Web 應用程式連線至內容傳遞網路，從邊緣節點提供靜態檔案。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a12eab1f7bc4177f659771d58a58f749507c994c
ms.lasthandoff: 04/12/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>將 Web 應用程式連線至內容傳遞網路

在本教學課程中，您將建立 Azure CDN 設定檔與 Azure CDN 端點，以透過 Azure CDN POP 位置從您的 Web 應用程式提供靜態檔案。

> [!TIP]
> 檢閱 [Azure CDN POP 位置](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)的最新清單。
>

## <a name="step-1---login-to-azure-portal"></a>步驟 1 - 登入 Azure 入口網站

首先，開啟您喜愛的瀏覽器並瀏覽至 Azure [管理入口網站](https://portal.azure.com)。

## <a name="step-2---create-a-cdn-profile"></a>步驟 2 - 建立 CDN 設定檔

按一下左側導覽中的 `+ New` 按鈕，按一下 [Web + Mobile]。 在 [Web + 行動] 類別之下，選取 [CDN]。

指定 [名稱]、[位置]、[資源群組]、[定價層]，然後按一下 [建立]。

從左側導覽開啟資源群組中樞，選取 **myResourceGroup**。 從資源清單中選取 **myCDNProfile**。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>步驟 3 - 建立 CDN 端點

按一下搜尋方塊旁邊命令中的 `+ Endpoint`，這會啟動端點建立刀鋒視窗。

指定 [名稱]、[原始類型]、[原始主機名稱]，然後按一下 [新增]。

將會建立端點，一旦建立內容傳遞網路端點，狀態就會更新為 [執行中]。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>步驟 4 - 運用 CDN

現在端點正在執行中，讓我們瀏覽至 Web 伺服器上的靜態檔案，然後將主機名稱從 `http://<app_name>.azurewebsites.net/path/to-static-file` 變更為 `http://<endpoint_name>.azureedge.net/path/to-static-file`，驗證是否可在 POP 伺服器上取得內容。

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>後續步驟


