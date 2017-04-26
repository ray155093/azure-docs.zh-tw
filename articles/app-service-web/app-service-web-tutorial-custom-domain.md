---
title: "將自訂 DNS 名稱對應至 Azure Web 應用程式 | Microsoft Docs"
description: "學習將自訂 DNS 網域名稱 (亦即虛名網域) 新增至 Azure App Service 中的 Web 應用程式、行動裝置應用程式後端或 API 應用程式。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>將自訂 DNS 名稱對應至 Azure Web 應用程式

本教學課程示範如何將自訂 DNS 名稱 (亦即虛名網域) 對應至 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 Web 應用程式、行動裝置應用程式後端或 API 應用程式。 

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

本教學課程循著範例案例，將兩個 DNS 名稱對應至 App Service 中的應用程式︰

- `contoso.com` - 根網域。 您將使用 [A 記錄](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)將它對應至 Azure。 
- `www.contoso.com` - `contoso.com` 的子網域。 您可以使用 [A 記錄](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)或 [CNAME 記錄](https://en.wikipedia.org/wiki/CNAME_record)。

本教學課程也示範如何將[萬用字元 DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record)對應至 App Service (例如 `*.contoso.com`)。

## <a name="before-you-begin"></a>開始之前

由於本教學課程將示範如何將 DNS 名稱對應至 Azure App Service，您對個別網域提供者 (例如 GoDaddy) 的 DNS 設定頁面，必須具有管理存取權。 例如，若要新增 `contoso.com` 和 `www.contoso.com` 的對應，您必須能夠設定 `contoso.com` 的 DNS 項目。

如果您還沒有向網域提供者註冊網域，您隨時都可以[直接從 Azure 購買網域，並將它對應至您的應用程式](custom-dns-web-site-buydomains-web-app.md)。

## <a name="step-1---prepare-your-app"></a>步驟 1 - 準備您的應用程式
若要對應自訂 DNS 名稱，您的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須在**共用**層或更高層。 在此步驟中，您要確定 Azure 應用程式在支援的定價層。

### <a name="log-in-to-azure"></a>登入 Azure

開啟 Azure 入口網站。 

若要這麼做，請以您的 Azure 帳戶登入 [https://portal.azure.com](https://portal.azure.com)。

### <a name="navigate-to-your-app"></a>瀏覽至您的應用程式
按一下左側功能表中的 [App Service]，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/select-app.png)

您已進入應用程式的_刀鋒視窗_ (水平開啟的入口網站頁面)。  

### <a name="check-the-pricing-tier"></a>檢查定價層
在 [概觀] 頁面上 (依預設開啟)，請確認您的應用程式不是在**免費**層。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

**免費**層不支援自訂 DNS。 如果您需要相應增加，請遵循下一節進行。 否則，跳到[步驟 2]()。

### <a name="scale-up-your-app-service-plan"></a>相應增加您的 App Service 方案

若要將您的方案相應增加，請按一下左窗格中的 [相應增加 (App Service 方案)]。

選取您想要擴充達到的層。 例如，選取 [共用]。 準備好時，按一下 [選取]。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

當您看見下列通知時，表示擴充作業已完成。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>步驟 2 - 取得對應資訊

在此步驟中，您會取得稍後建立 DNS 記錄時需要的資訊。 

### <a name="open-the-custom-domain-ui"></a>開啟自訂網域 UI

在應用程式的刀鋒視窗中，按一下功能表中的 [自訂網域]。 

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>複製對應資訊

在 [自訂網域] 頁面中，複製應用程式的 **IP 位址**和 [指派至網站的主機名稱] 下的預設主機名稱。

稍後對應 IP 位址時需要此 IP 位址，對應 CNAME 記錄時需要此預設主機名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>步驟 3 - 新增 DNS 記錄 

在此步驟中，您要新增將自訂 DNS 對應至應用程式所需的 DNS 記錄。 您可以搭配您自己的網域提供者執行此步驟。

### <a name="access-dns-records-with-domain-provider"></a>存取網域提供者中的 DNS 記錄

首先，登入網域提供者的網站。

然後，您需要找出 DNS 記錄的管理頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 一般而言，您應該尋找標示為 [網域名稱]、[DNS] 或 [名稱伺服器管理] 的連結或區域。 通常可透過檢視您的帳戶資訊，然後尋找 **我的網域**之類的連結來找到此連結。 

然後，尋找可讓您管理 DNS 記錄的連結。 此連結可能名為 [區域檔案]、[DNS 記錄] 或 [進階設定]。

下列螢幕擷取畫面是 DNS 記錄頁面看起來可能的一個例子：

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在螢幕擷取畫面範例中，您可以按一下 [新增] 建立記錄。 某些提供者有不同的連結來新增其他記錄類型。 同樣地，請參閱您的提供者文件。

> [!NOTE]
> 對於某些提供者，例如 GoDaddy，您在對 DNS 記錄做出變更後，需要按一下另外的 [儲存變更]連結，變更才會生效。 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>建立 A 記錄

在教學課程範例中，您想要新增根網域 `contoso.com` 的 A 記錄。 

若要將 A 記錄對應至您的應用程式，App Service 實際上需要_兩筆_DNS 記錄︰

- **A** 記錄對應至應用程式的 IP 位址。
- **TXT** 記錄對應至應用程式的預設主機名稱。 此記錄可讓 App Service 驗證您確實擁有您想要對應的自訂網域。

下表顯示您如何為支援的網域類型設定 A 記錄對應 (`@` 通常代表根網域)。 

<table cellspacing="0" border="1">
  <tr>
    <th>網域類型</th>
    <th>記錄類型</th>
    <th>Host</th>
    <th>值</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">根網域<br>(例如，<code>contoso.com</code>)</td>
    <td>具有使用 </td>
    <td>@</td>
    <td>來自<a href="#info">步驟 2</a> 的 IP 位址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td>來自<a href="#info">步驟 2</a> 的預設主機名稱</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">子網域<br>(例如，<code>www.contoso.com</code>)</td>
    <td>具有使用 </td>
    <td>www</td>
    <td>來自<a href="#info">步驟 2</a> 的 IP 位址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td>來自<a href="#info">步驟 2</a> 的預設主機名稱</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">萬用字元網域<br>(例如，<code>*.contoso.com</code>)</td>
    <td>具有使用 </td>
    <td>\*</td>
    <td>來自<a href="#info">步驟 2</a> 的 IP 位址</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td>來自<a href="#info">步驟 2</a> 的預設主機名稱</td>
  </tr>
</table>

在 `contoso.com` 網域範例中，您的 DNS 記錄頁面看起來如同下列螢幕擷取畫面︰

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>建立 CNAME 記錄
在教學課程範例中，您想要新增根網域 `contoso.com` 的 CNAME 記錄。 

不同於[對應 A 記錄](#a)，將 CNAME 記錄對應至您的應用程式不需要任何額外的記錄。

> [!IMPORTANT]
> 建議您使用 CNAME 對應。 如果您刪除又重新建立應用程式，或從專用託管層改回**共用**層，應用程式的虛擬 IP 位址可能會變更。 CNAME 對應在經過此變更後仍然有效，但 A 對應可能因為新的 IP 位址而失效。 
>
> 但是，請_勿_建立根網域的 CNAME 記錄 (也就是「根記錄」)。 如需詳細資訊，請參閱 [為什麼無法在根網域使用 CNAME 記錄](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)。
> 若要將根網域對應至 Azure 應用程式，請改用 [A 記錄](#a) 。
> 
> 

下表顯示您如何為支援的網域類型設定 CNAME 記錄對應。

如下所示設定 CNAME 記錄。

<table cellspacing="0" border="1">
  <tr>
    <th>網域類型</th>
    <th>CNAME 主機</th>
    <th>CNAME 值</th>
  </tr>
  <tr>
    <td>子網域<br>(例如，<code>www.contoso.com</code>)</td>
    <td>www</td>
    <td>來自<a href="#info">步驟 2</a> 的預設主機名稱</td>
  </tr>
  <tr>
    <td>萬用字元網域<br>(例如，<code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td>來自<a href="#info">步驟 2</a> 的預設主機名稱</td>
  </tr>
</table>

在 `www.contoso.com` 網域範例中，您的 DNS 記錄頁面看起來如同下列螢幕擷取畫面︰

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>步驟 4 - 在應用程式中啟用自訂 DNS

您現在已準備好將已設定的 DNS 名稱 (例如 `contoso.com` 和 `www.contoso.com`) 新增至您的應用程式。

回到 Azure 入口網站中的應用程式 [自訂網域] 頁面 (請參閱[步驟 1](#info))，您需要將自訂網域的完整網域名稱 (FQDN) 新增至清單。

### <a name="add-the-a-hostname-to-your-app"></a>將 A 主機名稱新增至您的應用程式

按一下 [新增主機名稱]旁的 **+** 圖示。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

輸入您稍早設定 A 記錄的 FQDN (例如 `contoso.com`)，然後按一下 [驗證]。

如果稍早錯過某個步驟，或在某處打錯字，您在頁面底部會看到驗證錯誤。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/verification-error.png)

否則 [新增主機名稱] 按鈕會啟用。 

請確定 [主機名稱記錄類型] 設為 [A 記錄 (example.com)]。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>將 CNAME 主機名稱新增至您的應用程式

按一下 [新增主機名稱]旁的 **+** 圖示。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

輸入您稍早設定 CNAME 記錄的 FQDN (例如 `www.contoso.com`)，然後按一下 [驗證]。

如果稍早錯過某個步驟，或在某處打錯字，您在頁面底部會看到驗證錯誤。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

否則 [新增主機名稱] 按鈕會啟用。 

請確定 [主機名稱記錄類型] 設為 [A 記錄 (example.com)]。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>步驟 5 - 在瀏覽器中測試

在瀏覽器中，瀏覽至您稍早設定的 DNS 名稱 (`contoso.com` 和 `www.contoso.com`)。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>其他資源

[在 Azure App Service 中購買並設定自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)

