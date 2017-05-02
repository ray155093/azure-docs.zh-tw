---
title: "在 Azure App Service 上新增內容傳遞網路 | Microsoft Docs"
description: "在 Azure App Service 上新增內容傳遞網路，以從邊緣節點提供靜態檔案。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>在 Azure App Service 上新增內容傳遞網路

在本教學課程中，您會在 Azure App Service 上新增內容傳遞網路 (CDN)，以在 Edge Server 上公開靜態內容。 您將會建立 CDN 設定檔，此設定檔是最多 10 個 CDN 端點的集合。

內容傳遞網路會在策略性放置的位置上快取靜態 Web 內容，以提供最大輸送量來將內容傳遞給使用者。 使用 CDN 來快取網站資產的優點包括：

* 讓使用者享有更好的效能和使用者經驗，尤其是當使用的應用程式需要反覆存取多次才能載入內容時。
* 可進行大幅調整以更妥善地處理瞬間大量負載 (例如產品上市事件的開始)。
* 透過分散使用者要求以及從 Edge Server 提供內容，傳送至原始來源的流量將會減少。

> [!TIP]
> 檢閱 [Azure CDN POP 位置](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)的最新清單。
>

## <a name="deploy-the-sample"></a>部署範例

若要完成本教學課程，您必須在 Web 應用程式中部署應用程式。 請遵循[靜態 HTML 快速入門](app-service-web-get-started-html.md)來為本教學課程奠定基礎。

## <a name="step-1---login-to-azure-portal"></a>步驟 1 - 登入 Azure 入口網站

首先，開啟您喜愛的瀏覽器並瀏覽至 Azure [入口網站](https://portal.azure.com)。

## <a name="step-2---create-a-cdn-profile"></a>步驟 2 - 建立 CDN 設定檔

按一下左側導覽中的 `+ New` 按鈕，按一下 [Web + Mobile]。 在 [Web + 行動] 類別之下，選取 [CDN]。

指定下列欄位：

| 欄位 | 範例值 | 說明 |
|---|---|---|
| 名稱 | myCDNProfile | CDN 設定檔的名稱。 |
| 位置 | 西歐 | 此為儲存您 CDN 設定檔資訊的所在 Azure 位置。 其不會影響 CDN 端點位置。 |
| 資源群組 | myResourceGroup | 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md#resource-groups) |
| 定價層 | 標準 Akamai | 如需定價層的比較，請參閱 [CDN 概觀](../cdn/cdn-overview.md#azure-cdn-features)。 |

按一下 [建立] 。

從左側導覽開啟資源群組中樞，選取 **myResourceGroup**。 從資源清單中選取 **myCDNProfile**。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>步驟 3 - 建立 CDN 端點

按一下搜尋方塊旁命令中的 [+ 端點]，這會啟動端點建立刀鋒視窗。

指定下列欄位：

| 欄位 | 範例值 | 說明 |
|---|---|
| 名稱 |  | 此名稱會用於存取位於網域 `<endpointname>.azureedge.net` 的快取資源 |
| 原始來源類型 | Web 應用程式 | 選取原始來源類型會為您提供其餘欄位的關聯式功能表。 選取自訂原始來源則會為您提供用於填入原始主機名稱的文字欄位。 |
| 原始主機名稱 | |  下拉式清單會列出您指定之原始來源類型的所有可用原始來源。 如果您選取 [自訂原始來源] 作為您的 [原始來源類型]，您將會輸入自訂原始來源的網域  |

按一下 [新增] 。

將會建立端點，一旦建立內容傳遞網路端點，狀態就會更新為 [執行中]。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>步驟 4 - 從 Azure CDN 提供

CDN 端點現已**執行中**，因此，您應該能夠從 CDN 端點來存取內容。

考慮到我們使用[靜態 HTML 快速入門](app-service-web-get-started-html.md)作為本教學課程的基礎，所以 CDN 上應該有下列資料夾︰`css`、`img`、`js`。

Web 應用程式 URL `http://<app_name>.azurewebsites.net/img/` 和 CDN 端點 URL `http://<endpointname>.azureedge.net/img/` 之間的內容路徑是相同的，這表示您只需要以 CDN 端點網域取代任何靜態內容，即可從 CDN 提供該內容。

在您最愛的網頁瀏覽器中瀏覽至下列 URL，以從 CDN 端點提取我們的第一個影像︰

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

靜態內容在 CDN 上已可供使用，因此您可以將應用程式更新為使用 CDN 端點來將內容傳遞給使用者。

視您網站的建置語言而定，可能有許多架構可協助實現 CDN 後援。 例如，ASP.NET 所提供的[統合和縮製](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn)支援也可實現 CDN 後援功能。

如果您的語言沒有內建的 CDN 容錯回復支援或這項支援的程式庫，您可以使用類似 [FallbackJS](http://fallback.io/) 的 JavaScript 架構，以支援載入[指令碼](https://github.com/dolox/fallback/tree/master/examples/loading-scripts)、[樣式表](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets)和[影像](https://github.com/dolox/fallback/tree/master/examples/loading-images)。

## <a name="step-5---purge-the-cdn"></a>步驟 5 - 清除 CDN

如果您想要讓內容在存留時間 (TTL) 到期之前就先到期，有時候您可能必須強制清除 CDN。

您可以手動清除 Azure CDN，不論是 [CDN 設定檔] 刀鋒視窗或 [CDN 端點] 刀鋒視窗都可讓您這麼做。 如果您從 [設定檔] 頁面選取清除作業，您就必須選取您想要清除哪一個端點。

若要清除內容，請輸入您想要清除的內容路徑。 您可以傳遞完整檔案路徑來清除個別檔案，也可以傳遞路徑區段以從特定資料夾清除並重新整理內容。

在提供了您想要清除的所有內容路徑後，按一下 [清除]。

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>步驟 6 - 對應自訂網域

將自訂網域對應至 CDN 端點可為 Web 應用程式提供統一的網域。

若要將自訂網域對應至 CDN 端點，請在網域註冊機構建立 CNAME 記錄。

> [!NOTE]
> CNAME 記錄是將 `www.contosocdn.com` 或 `static.contosocdn.com` 等來源網域對應至目的地網域的 DNS 功能。

在此案例中，我們會新增 `static.contosocdn.com` 來源網域，而此網域會指向作為 CDN 端點的目的地網域。

| 來源網域 | 目的地網域 |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

從 [CDN 端點概觀] 刀鋒視窗中，按一下 `+ Custom domain` 按鈕。

在 [新增自訂網域] 刀鋒視窗中，於對話方塊內輸入您的自訂網域 (包括子網域)。 例如，以 `static.contosocdn.com` 格式輸入網域名稱。

按一下 [新增] 。

## <a name="step-7---version-content"></a>步驟 7 - 版本內容

在 CDN 端點的左側導覽中，選取 [設定] 標題底下的 [快取]。

[快取] 刀鋒視窗可讓您設定 CDN 該如何處理要求中的查詢字串。

> [!NOTE]
> 如需關於查詢字串快取行為選項的說明，請參閱[使用查詢字串控制 Azure CDN 快取行為](../cdn/cdn-query-string.md)主題。

從查詢字串快取行為的下拉式清單中，選取 [快取每個唯一 URL]。

按一下 [儲存] 。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure CDN](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [在 Azure CDN 自訂網域上啟用 HTTPS](../cdn/cdn-custom-ssl.md)
* [在 Azure CDN 中壓縮檔案以改善效能](../cdn/cdn-improve-performance.md)
* [在 Azure CDN 端點上預先載入資產](../cdn/cdn-preload-endpoint.md)

