---
title: "將現有的自訂 SSL 憑證繫結至 Azure Web Apps | Microsoft Docs"
description: "了解如何將自訂 SSL 憑證繫結至 Azure App Service 中的 web 應用程式、行動裝置應用程式後端或 API 應用程式。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5bbdd1db655c080b4372f6728bb47207757209e4
ms.lasthandoff: 04/27/2017


---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>將現有的自訂 SSL 憑證繫結至 Azure Web Apps

本教學課程會示範如何將您從受信任憑證授權單位購買的自訂 SSL 憑證繫結至 [Azure Web Apps](app-service-web-overview.md)。 當您完成時，將可以在自訂 DNS 網域的 HTTPS 端點存取 web 應用程式。

![Web 應用程式與自訂 SSL 憑證](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!TIP]
> 如果您需要自訂 SSL 憑證，可以直接在 Azure 入口網站取得，並將它繫結至 web 應用程式。 遵循 [App Service 憑證教學課程](web-sites-purchase-ssl-web-site.md)。 
>
> 

## <a name="before-you-begin"></a>開始之前
在依照本教學課程進行之前，請先確定您已完成下列項目：

- [建立 App Service 應用程式](/azure/app-service/)
- [將自訂 DNS 名稱對應至 Web 應用程式](web-sites-custom-domain-name.md)
- 取得受信任憑證授權單位所核發的 SSL 憑證

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 憑證的需求

若要在 App Service 中使用您的憑證，則您的憑證必須符合以下所有的需求︰

* 由受信任的憑證授權單位簽署
* 以受密碼保護的 PFX 檔案形式匯出
* 包含長度至少為 2048 位元的私密金鑰
* 包含憑證鏈結中的所有中繼憑證

> [!NOTE]
> **橢圓曲線密碼編譯 (ECC) 憑證** 可搭配 App Service 使用，但不在本文討論範圍內。 請洽詢您的憑證授權單位，了解建立 ECC 憑證的確切步驟。
> 
>

## <a name="prepare-your-web-app"></a>準備您的 Web 應用程式
若要將自訂 SSL 憑證繫結至您的 web 應用程式，您的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須為**基本**、**標準**或**進階**層。 在此步驟中，您要確定 Web 應用程式在支援的定價層。

### <a name="log-in-to-azure"></a>登入 Azure

開啟 Azure 入口網站。 若要這麼做，請以您的 Azure 帳戶登入 [https://portal.azure.com](https://portal.azure.com)。

### <a name="navigate-to-your-web-app"></a>瀏覽至您的 Web 應用程式
按一下左側功能表中的 [應用程式服務]，然後按一下 Web 應用程式的名稱。

![選取 Web 應用程式](./media/app-service-web-tutorial-custom-ssl/select-app.png)

您已進入 Web 應用程式的管理刀鋒視窗 (_刀鋒視窗_：水平開啟的入口網站頁面)。  

### <a name="check-the-pricing-tier"></a>檢查定價層

在您 Web 應用程式刀鋒視窗的左側導覽中，捲動到 [設定] 區段，然後選取 [相應增加 (App Service 方案)]。

![相應增加功能表](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

請檢查以確定您的 web 應用程式不在**免費**或**共用** 層中。 系統會以深藍色方塊醒目顯示 Web 應用程式目前的層。 

![檢查定價層](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

不支援**免費**和**共用**層中的自訂 SSL。 如果您需要相應增加，請遵循下一節進行。 否則，請關閉 [選擇定價層] 刀鋒視窗，然後跳至 [將 SSL 憑證上傳並繫結][](#upload)。

### <a name="scale-up-your-app-service-plan"></a>相應增加您的 App Service 方案

選取**基本****標準**或**高階**層的其中一個。 

按一下 [選取] 。

![選擇定價層](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

當您看見下列通知時，表示擴充作業已完成。

![相應增加通知](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>繫結 SSL 憑證

您已準備好將 SSL 憑證上傳至您的 web 應用程式。 

### <a name="export-certificate-to-pfx"></a>將憑證匯出為 PFX

您必須使用與憑證要求共同產生的私密金鑰，將您的自訂 SSL 憑證匯出。

如果您是使用 OpenSSL 產生憑證要求，則已建立私密金鑰。 若要將您的憑證匯出為 PFX，執行下列命令︰

```bash
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
```

如果您使用 IIS 或 `Certreq.exe` 來產生憑證要求，請先將您的憑證安裝至本機電腦，然後遵循[將包含私密金鑰的憑證匯出](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)的步驟將它匯出為 PFX。

### <a name="upload-your-ssl-certificate"></a>上傳 SSL 憑證

若要上傳 SSL 憑證，請按一下 web 應用程式左側導覽列中的 [SSL 憑證]。

按一下 [上傳憑證]。

在 [PFX 憑證檔案] 中，選取您的 PFX 檔案。 在 [憑證密碼] 中，輸入您將 PFX 檔案匯出時所建立的密碼。

按一下 [上傳] 。

![Upload certificate](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

當 App Service 完成上傳您的憑證時，它會出現在 [SSL 憑證] 頁面。

![Certificate uploaded](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>繫結 SSL 憑證

您現在應該會在 [SSL 憑證]  頁面中看到您上傳的憑證。

在 [SSL 繫結] 區段中，按一下 [新增繫結]。

在 [新增 SSL 繫結] 刀鋒視窗中，使用下拉式清單選取要保護的網域名稱，以及要使用的憑證。 

在 **SSL 類型**中，選擇使用**[伺服器名稱指示 (SNI) ](http://en.wikipedia.org/wiki/Server_Name_Indication)**還是以 IP 為基礎的 SSL。
   
- **以 SNI 為基礎的 SSL** - 可能會新增多個以 SNI 為基礎的 SSL 繫結。 此選項可允許多個 SSL 憑證保護同一個 IP 位址上的多個網域。 大多數現代化的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI (可在[伺服器名稱指示](http://wikipedia.org/wiki/Server_Name_Indication)找到更完整的瀏覽器支援資訊)。
- **以 IP 為基礎的 SSL** - 只會新增一個以 IP 為基礎的 SSL 繫結。 此選項只允許一個 SSL 憑證保護專用的公用 IP 位址。 若要保護多個網域，您必須全部使用相同的 SSL 憑證來保護它們。 這是 SSL 繫結的傳統選項。 

按一下 [新增繫結]。

![繫結 SSL 憑證](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

當 App Service 完成上傳您的憑證時，它會出現在 [SSL 繫結] 頁面。

![繫結至 web 應用程式的憑證](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>將 IP SSL 的 A 記錄重新對應

如果您不使用 web 應用程式中以 IP 為基礎的 SSL，請跳至[測試自訂網域的 HTTPS](#test)。 

根據預設，web 應用程式會使用共用的公用 IP 位址。 當您將以 IP 為基礎的 SSL 與憑證繫結時，App Service 會為 Web 應用程式建立新的專用 IP 位址。

如果您已將 A 記錄對應至 web 應用程式，請使用這個新的專用 IP 位址來更新您的網域登錄。

已將 Web 應用程式的**自訂網域**頁面更新為新的專用 IP 位址。 [複製此 IP 位址](app-service-web-tutorial-custom-domain.md#info)，然後[將 A 記錄重新對應](app-service-web-tutorial-custom-domain.md#create-the-a-record)到這個新的 IP 位址。

<a name="test"></a>

## <a name="test-https"></a>測試 HTTPS
現在只剩下確定 HTTPS 是否能用在您的自訂網域。 在各種瀏覽器中，瀏覽至 `https://<your.custom.domain>` 以查看它是否能提供您的 web 應用程式。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> 如果您的 web 應用程式出現憑證驗證錯誤，您可能使用了自我簽署憑證。
>
> 如果不是，在您將憑證匯出為 PFX 檔案時，可能遺漏了中繼憑證。 
>
>

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>強制使用 HTTPS
如果您仍然想允許 web 應用程式的 HTTP 存取，請略過此步驟。 

App Service 不會強制使用 HTTPS，因此任何使用者仍可以使用 HTTP 存取您的 Web 應用程式。 若要強制 web 應用程式使用 HTTPS，您可以在 web 應用程式的 `web.config` 檔案中定義重寫規則。 無論 Web 應用程式語言架構為何，App Service 應用程式都會使用這個檔案。

> [!NOTE]
> 有語言特定的要求重新導向。 ASP.NET MVC 可使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選，而非 `web.config` 中的重寫規則 (請參閱[將安全的 ASP.NET MVC 5 應用程式部署到 Web 應用程式](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md))。
> 
> 

如果您是 .NET 開發人員，應該很熟悉這個檔案。 它會在您解決方案的根目錄。

此外，如果您是使用 PHP、Node.js、Python 或 Java 進行開發，我們有可能會在 App Service 中代表您產生這個檔案。

遵循[使用 FTP/S 將應用程式部署至 Azure App Service](app-service-deploy-ftp.md) 中的指示，連線到 Web 應用程式的 FTP 端點。 

此檔案應該位於 `/home/site/wwwroot`。 如果沒有，請在此資料夾中建立 `web.config`，並包含下列 XML：

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule TAG FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

針對現有的 `web.config`，您只需要將整個 `<rule>` 標記複製到您 `web.config` 的 `configuration/system.webServer/rewrite/rules` 元素中。 如果您的 `web.config` 中有其他 `<rule>` 標記，請將複製的 `<rule>` 標記放在其他 `<rule>` 標記之前。

每當使用者向 web 應用程式要求 HTTP 時，此規則就會將 HTTP 301 (永久重新導向) 傳回 HTTPS 通訊協定。 例如，它會從 `http://contoso.com` 重新導向至 `https://contoso.com`。

如需 IIS URL Rewrite 模組的詳細資訊，請參閱 [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite) (英文) 文件。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview)，透過指令碼將 web 應用程式的 SSL 繫結自動化。

### <a name="azure-cli"></a>Azure CLI

下列命令會將匯出的 PFX 檔案上傳，並取得憑證指紋。 

```bash
thumprint=$(az appservice web config ssl upload --certificate-file <path_to_PFX_file> \
--certificate-password <PFX_password> --name <app_name> --resource-group <resource_group_name> \
--query thumbprint --output tsv)
```

下列命令會使用上述命令的指紋來新增以 SNI 為基礎的 SSL 繫結。

```bash
az appservice web config ssl bind --certificate-thumbprint $thumbprint --ssl-type SNI \
--name <app_name> --resource-group <resource_group_name>
```

### <a name="azure-powershell"></a>Azure PowerShell

下列命令會將匯出的 PFX 檔案上傳，並新增以 SNI 為基礎的 SSL 繫結。

```PowerShell
New-AzureRmWebAppSSLBinding -WebAppName <app_name> -ResourceGroupName <resource_group_name> -Name <dns_name> `
-CertificateFilePath <path_to_PFX_file> -CertificatePassword <PFX_password> -SslState SniEnabled
```
## <a name="more-resources"></a>其他資源
* [Microsoft Azure 信任中心](/support/trust-center/security/)
* [Azure 網站中解除鎖定的設定選項](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [啟用診斷記錄](web-sites-enable-diagnostic-log.md)
* [在 Azure App Service 中設定 Web 應用程式](web-sites-configure.md)

