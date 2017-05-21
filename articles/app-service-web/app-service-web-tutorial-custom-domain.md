---
title: "將現有的自訂 DNS 名稱對應至 Azure Web Apps | Microsoft Docs"
description: "學習將現有的自訂 DNS 網域名稱 (虛名網域) 新增至 Azure App Service 中的 Web 應用程式、行動裝置應用程式後端或 API 應用程式。"
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
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017


---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>將現有的自訂 DNS 名稱對應至 Azure Web Apps

本教學課程會示範如何將現有的自訂 DNS 名稱對應至 [Azure Web Apps](app-service-web-overview.md)。 

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 CNAME 記錄來對應子網域 (例如，`www.contoso.com`)
> * 使用 A 記錄來對應根網域 (例如，`contoso.com`)
> * 使用 CNAME 記錄來對應萬用字元網域 (例如，`*.contoso.com`)
> * 使用指令碼來自動對應網域

您可以使用 **CNAME 記錄**或 **A 記錄**將自訂 DNS 名稱對應至 App Service。

> [!NOTE]
> 我們建議您對所有自訂 DNS 名稱使用 CNAME，但根網域除外 (例如 `contoso.com`)。 
> 
> 

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要網域提供者 (例如 GoDaddy) 的 DNS 登錄存取權，以及網域組態的編輯權限。 

例如，若要為 `contoso.com` 和 `www.contoso.com` 新增 DNS 項目，您必須有權設定 `contoso.com` 根網域的 DNS 設定。 

> [!NOTE]
> 如果您沒有現有的網域名稱，請考慮遵循 [App Service 網域教學課程](custom-dns-web-site-buydomains-web-app.md)的作法，使用 Azure 入口網站來購買網域。 
>
>

## <a name="prepare-your-app"></a>準備您的應用程式
若要對應自訂的 DNS 名稱，[App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須是付費層 (**共用**、**基本**、**標準**或**進階**)。 在此步驟中，您要確定 App Service 應用程式位於支援的定價層。

### <a name="sign-in-to-azure"></a>登入 Azure

開啟 Azure 入口網站。 若要這麼做，請以您的 Azure 帳戶登入 [https://portal.azure.com](https://portal.azure.com)。

### <a name="navigate-to-your-app"></a>瀏覽至您的應用程式
按一下左側功能表中的 [應用程式服務]，然後按一下應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/select-app.png)

您已進入 App Service 應用程式的管理刀鋒視窗 (_刀鋒視窗_：水平開啟的入口網站頁面)。  

### <a name="check-the-pricing-tier"></a>檢查定價層

在應用程式刀鋒視窗的左側導覽中，捲動到 [設定] 區段，然後選取 [相應增加 (App Service 方案)]。

![相應增加功能表](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

請檢查以確定您的應用程式不是位於**免費**層。 會以深藍色方塊醒目顯示應用程式目前的層。 

![檢查定價層](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

**免費**層不支援自訂 DNS。 如果您需要相應增加，請遵循下一節進行。 否則，請關閉 [選擇定價層] 刀鋒視窗，然後跳至 [對應 CNAME 記錄][](#cname) 或 [對應 A 記錄][](#a)。

### <a name="scale-up-your-app-service-plan"></a>相應增加您的 App Service 方案

選取任一個非免費層 (**共用**、**基本**、**標準**或**進階**)。 

按一下 [選取] 。

![檢查定價層](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

當您看見下列通知時，表示擴充作業已完成。

![擴充作業確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>對應 CNAME 記錄

在教學課程範例中，您想要新增 `www` 子網域 (`www.contoso.com`) 的 CNAME 記錄。 

### <a name="access-dns-records-with-domain-provider"></a>存取網域提供者中的 DNS 記錄

首先，登入網域提供者的網站。

尋找管理 DNS 記錄的頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。 

通常可透過檢視您的帳戶資訊，然後尋找 **我的網域**之類的連結來找到此連結。 然後，尋找可讓您管理 DNS 記錄的連結。 此連結可能名為 [區域檔案]、[DNS 記錄] 或 [進階設定]。

下列螢幕擷取畫面是 DNS 記錄頁面的範例：

![DNS 記錄頁面範例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在螢幕擷取畫面範例中，您可以按一下 [新增] 建立記錄。 某些提供者有不同的連結來新增其他記錄類型。 同樣地，請參閱您的提供者文件。

> [!NOTE]
> 對於某些提供者 (例如 GoDaddy)，您必須按一下另外的 [儲存變更] 連結，才會讓 DNS 記錄的變更生效。 
>
>

### <a name="create-the-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄以將子網域對應至應用程式的預設主機名稱 (`<app_name>.azurewebsites.net`)。

對於 `www.contoso.com` 網域範例，您的 CNAME 記錄應該將 `www` 名稱指向 `<app_name>.azurewebsites.net`。

您的 DNS 記錄頁面看起來如同下列螢幕擷取畫面︰

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>在應用程式中啟用 CNAME 記錄對應

您現在已準備好將已設定的 DNS 名稱新增至您的應用程式。

在應用程式刀鋒視窗的左側導覽中，按一下 [自訂網域]。 

![[自訂網域] 功能表](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在應用程式的 [自訂網域] 刀鋒視窗中，您需要在清單中新增自訂的完整 DNS 名稱 (`www.contoso.com`)。

按一下 [新增主機名稱]旁的 **+** 圖示。

![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

輸入您稍早設定 CNAME 記錄的完整網域名稱 (例如 `www.contoso.com`)，然後按一下 [驗證]。

否則 [新增主機名稱] 按鈕會啟用。 

請確定 [主機名稱記錄類型] 設為 [CNAME 記錄 (example.com)]。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![將 DNS 名稱新增至應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。

![CNAME 記錄已新增](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

如果稍早錯過某個步驟，或在某處打錯字，您在頁面底部會看到驗證錯誤。

![驗證錯誤](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>對應 A 記錄

在教學課程範例中，您想要新增根網域 `contoso.com` 的 A 記錄。 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>複製應用程式的 IP 位址

若要對應 A 記錄，您需要應用程式的外部 IP 位址。 您可以在 [自訂網域] 刀鋒視窗中找到這個 IP 位址。

在應用程式刀鋒視窗的左側導覽中，按一下 [自訂網域]。 

![[自訂網域] 功能表](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在 [自訂網域] 頁面中，複製應用程式的 IP 位址。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>存取網域提供者中的 DNS 記錄

首先，登入網域提供者的網站。

尋找管理 DNS 記錄的頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。 

通常可透過檢視您的帳戶資訊，然後尋找 **我的網域**之類的連結來找到此連結。 然後，尋找可讓您管理 DNS 記錄的連結。 此連結可能名為 [區域檔案]、[DNS 記錄] 或 [進階設定]。

下列螢幕擷取畫面是 DNS 記錄頁面的範例：

![DNS 記錄頁面範例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在螢幕擷取畫面範例中，您可以按一下 [新增] 建立記錄。 某些提供者有不同的連結來新增其他記錄類型。 同樣地，請參閱您的提供者文件。

> [!NOTE]
> 對於某些提供者 (例如 GoDaddy)，您必須按一下另外的 [儲存變更] 連結，才會讓 DNS 記錄的變更生效。 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>建立 A 記錄

若要將 A 記錄對應至您的應用程式，App Service 實際上需要**兩筆**DNS 記錄︰

- **A** 記錄對應至應用程式的 IP 位址。
- **TXT** 記錄對應至應用程式的預設主機名稱 `<app_name>.azurewebsites.net`。 此記錄可讓 App Service 驗證您確實擁有您想要對應的自訂網域。

對於 `www.contoso.com` 網域範例，請根據下表建立 A 和 TXT 記錄 (`@` 通常代表根網域)。 

| 記錄類型 | Host | 值 |
| - | - | - |
| 具有使用  | `@` | 來自[複製應用程式的 IP 位址](#info)的 IP 位址 |
| TXT | `@` | `<app_name>.azurewebsites.net` |

您的 DNS 記錄頁面看起來應該如同下列螢幕擷取畫面︰

![[DNS 記錄] 頁面](./media/app-service-web-tutorial-custom-domain/a-record.png)

### <a name="enable-the-a-record-mapping-in-your-app"></a>在應用程式中啟用 A 記錄對應

您現在已準備好將已設定的 DNS 名稱新增至您的應用程式。

回到 Azure 入口網站中的應用程式 [自訂網域] 頁面，您需要在清單中新增自訂的完整 DNS 名稱 (`contoso.com`)。

按一下 [新增主機名稱]旁的 **+** 圖示。

![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

輸入您稍早設定 A 記錄的完整網域名稱 (例如 `contoso.com`)，然後按一下 [驗證]。

否則 [新增主機名稱] 按鈕會啟用。 

請確定 [主機名稱記錄類型] 設為 [A 記錄 (example.com)]。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![將 DNS 名稱新增至應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。

![A 記錄已新增](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

如果稍早錯過某個步驟，或在某處打錯字，您在頁面底部會看到驗證錯誤。

![驗證錯誤](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>對應萬用字元網域

您也可以將[萬用字元 DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (例如 `*.contoso.com`) 對應至您的 App Service 應用程式。 

這裡的步驟示範如何使用 CNAME 記錄來對應萬用字元網域 `*.contoso.com`。 

### <a name="access-dns-records-with-domain-provider"></a>存取網域提供者中的 DNS 記錄

首先，登入網域提供者的網站。

尋找管理 DNS 記錄的頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。 

通常可透過檢視您的帳戶資訊，然後尋找 **我的網域**之類的連結來找到此連結。 然後，尋找可讓您管理 DNS 記錄的連結。 此連結可能名為 [區域檔案]、[DNS 記錄] 或 [進階設定]。

下列螢幕擷取畫面是 DNS 記錄頁面的範例：

![DNS 記錄頁面範例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

在螢幕擷取畫面範例中，您可以按一下 [新增] 建立記錄。 某些提供者有不同的連結來新增其他記錄類型。 同樣地，請參閱您的提供者文件。

> [!NOTE]
> 對於某些提供者 (例如 GoDaddy)，您必須按一下另外的 [儲存變更] 連結，才會讓 DNS 記錄的變更生效。 
>
>

### <a name="create-the-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄以將萬用字元名稱對應至應用程式的預設主機名稱 (`<app_name>.azurewebsites.net`)。

對於 `*.contoso.com` 網域範例，您的 CNAME 記錄應該將 `*` 名稱指向 `<app_name>.azurewebsites.net`。

您的 DNS 記錄頁面看起來如同下列螢幕擷取畫面︰

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>在應用程式中啟用 CNAME 記錄對應

您現在可以新增任何與萬用字元名稱相符的子網域。

對於 `*.contoso.com` 萬用字元範例，您現在可以新增 `sub1.contoso.com` 和 `sub2.contoso.com`。 

在應用程式刀鋒視窗的左側導覽中，按一下 [自訂網域]。 

![[自訂網域] 功能表](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

按一下 [新增主機名稱]旁的 **+** 圖示。

![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

為子網域輸入與萬用字元網域相符的完整網域名稱 (例如 `sub1.contoso.com`)，然後按一下 [驗證]。

否則 [新增主機名稱] 按鈕會啟用。 

請確定 [主機名稱記錄類型] 設為 [CNAME 記錄 (example.com)]。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![將 DNS 名稱新增至應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。

您可以再次按一下 **+** 圖示，以新增另一個與萬用字元網域相符的主機名稱。

例如，使用上述相同步驟來新增 `sub2.contoso.com`。

![CNAME 記錄已新增](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>在瀏覽器中測試

在瀏覽器中，瀏覽至您稍早設定的 DNS 名稱 (`contoso.com` 和 `www.contoso.com`)。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview)，透過指令碼將自訂網域的管理作業自動化。 

### <a name="azure-cli"></a>Azure CLI 

下列命令會在 App Service 應用程式中新增所設定的自訂 DNS 名稱。 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

如需詳細資訊，請參閱[將自訂網域對應至 Web 應用程式](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

下列命令會在 App Service 應用程式中新增所設定的自訂 DNS 名稱。 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

如需詳細資訊，請參閱[將自訂網域指派給 Web 應用程式](scripts/app-service-powershell-configure-custom-domain.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 CNAME 記錄來對應子網域
> * 使用 A 記錄來對應根網域
> * 使用 CNAME 記錄來對應萬用字元網域
> * 使用指令碼來自動對應網域

前進到下一個教學課程，以了解如何對它繫結自訂的 SSL 憑證。

> [!div class="nextstepaction"]
> [將現有的自訂 SSL 憑證繫結至 Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

