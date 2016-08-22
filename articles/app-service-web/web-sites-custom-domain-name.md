<properties
	pageTitle="將自訂網域名稱對應至 Azure 應用程式"
	description="了解如何在 Azure App Service 中將自訂網域名稱 (虛名網域) 對應至應用程式。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="cephalin"/>

# 將自訂網域名稱對應至 Azure 應用程式

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

這篇文章說明如何在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中將自訂網域名稱對應至您的 Web 應用程式、行動應用程式後端或 API 應用程式。

您的應用程式已隨附唯一的 azurewebsites.net 子網域。例如，如果您的應用程式名稱為 **contoso**，則其網域名稱為 **contoso.azurewebsites.net**。不過，您可以將自訂網域名稱對應至應用程式，以便其 URL (例如 `www.contoso.com`) 反映出您的品牌。

>[AZURE.NOTE] 在 [Azure 論壇](https://azure.microsoft.com/support/forums/)上取得 Azure 專家的協助。如需更高層級的支援，請前往 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## 在 Azure 入口網站中購買新的自訂網域

如果您尚未購買自訂網域名稱，您可以直接在 [Azure 入口網站](https://portal.azure.com)中您的應用程式的設定中進行購買和管理。不論您的應用程式是否使用 [Azure 流量管理員](web-sites-traffic-manager-custom-domain-name.md)，此選項都可讓您輕鬆地將自訂網域對應至您的應用程式。

如需相關指示，請參閱[購買 App Service 的自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)。

## 對應您在外部購買的自訂網域

如果您已從 [Azure DNS](https://azure.microsoft.com/services/dns/) 或從協力廠商提供者購買自訂網域，有三個主要步驟可將自訂網域對應至您的應用程式︰

1. [*(僅限 A 記錄)* 取得應用程式的 IP 位址](#vip)。
2. [建立 DNS 記錄，將您的網域對應至您的應用程式](#createdns)。
    - **何地**︰您的網域註冊機構擁有的管理工具 (例如 Azure DNS、GoDaddy 等)。
    - **為何**︰讓您的網域註冊機構知道解析 Azure 應用程式所需的自訂網域。
1. [為您的 Azure 應用程式啟用自訂網域名稱](#enable)。
    - **何地**：[Azure 入口網站](https://portal.azure.com)。
    - **為何**︰讓您的應用程式知道回應對自訂網域名稱所做的要求。
3. [確認 DNS 傳播](#verify)。

### 您可以對應的網域類型

Azure App Service 可讓您將下列類別的自訂網域對應至您的應用程式。

- **根網域** - 您透過網域註冊機構保留的網域名稱 (通常以 `@` 主機記錄表示)。例如 **contoso.com**。
- **子網域** - 根網域下的任何網域。例如，**www.contoso.com** (以 `www` 主機記錄表示)。您可以將相同根網域的不同子網域對應至 Azure 中不同的應用程式。
- **萬用字元網域** - [最左邊 DNS 標籤為 `*` 的任何子網域](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (例如主機記錄 `*` 和 `*.blogs`)。例如，***.contoso.com**。

### 您可以使用的 DNS 記錄類型

根據您的需求，您可以使用兩種不同的標準 DNS 記錄來對應您的自訂網域︰

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - 直接將自訂網域名稱對應至 Azure 應用程式的虛擬 IP 位址。
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - 將自訂網域名稱對應至您的應用程式的 Azure 網域名稱，**&lt;appname>.azurewebsites.net**。

CNAME 的優點是它會存留在 IP 位址變更中。若您刪除後重新建立應用程式，或是從較高定價層變回**共用**層，則應用程式的虛擬 IP 位址可能會變更。透過此種變更，CNAME 記錄仍然有效，但 A 記錄需要更新。

本教學課程示範使用 A 記錄以及使用 CNAME 記錄的步驟。

>[AZURE.IMPORTANT] 請勿建立根網域的 CNAME 記錄 (也就是「根記錄」)。如需詳細資訊，請參閱[為什麼無法在根網域使用 CNAME 記錄](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)。若要將根網域對應至 Azure 應用程式，請改用 A 記錄。

<a name="vip"></a>
## 步驟 1.(僅限 A 記錄) 取得應用程式的 IP 位址
若要使用 A 記錄對應自訂網域名稱，您需要 Azure 應用程式的 IP 位址。如果您改為使用 CNAME 記錄進行對應，請略過此步驟並移到下一節。

1.	登入 [Azure 入口網站](https://portal.azure.com)。

2.	按一下左側功能表上的 [應用程式服務]。

4.	按一下您的應用程式，然後按一下 [設定] > [自訂網域和 SSL] > [帶出外部網域]。

6.  記下 IP 位址。

    ![對應自訂網域名稱與 A 記錄︰取得 Azure App Service 應用程式的 IP 位址](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  將此入口網站刀鋒視窗保持開啟狀態。您將會在建立 DNS 記錄後立即回到該刀鋒視窗。

<a name="createdns"></a>
## 步驟 2.建立 DNS 記錄

登入網域註冊機構，並使用它們的工具加入 A 記錄或 CNAME 記錄。每個註冊機構的 UI 稍有不同，所以您應該參閱您的提供者的文件。不過，以下有幾個一般方針。

1.	尋找管理 DNS 記錄的頁面。在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。通常可透過檢視您的帳戶資訊，然後尋找**我的網域**之類的連結來找到此連結。
2.	尋找可讓您加入或編輯 DNS 記錄的連結。這可能是 [區域檔案]、[DNS 記錄] 連結或 [進階] 設定連結。
3.  建立記錄並儲存您的變更。
    - [A 記錄的指示在此處](#a)。
    - [CNAME 記錄的指示在此處](#cname)。

<a name="a"></a>
### 建立 A 記錄

若要使用 A 記錄來對應至 Azure 應用程式的 IP 位址，您實際上需要建立一筆 A 記錄和一筆 CNAME 記錄。A 記錄可供 DNS 解析本身使用，而 CNAME 記錄可供 Azure 驗證您是否擁有自訂網域名稱。

A 記錄應該設定如下 (@ 通常代表根網域)︰
 
<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 範例</th>
    <th>主機</th>
    <th>值</th>
  </tr>
  <tr>
    <td>contoso.com (根網域)</td>
    <td>@</td>
    <td>來自<a href="#vip">步驟 1</a> 的 IP 位址</td>
  </tr>
  <tr>
    <td>www.contoso.com (子網域)</td>
    <td>www</td>
    <td>來自<a href="#vip">步驟 1</a> 的 IP 位址</td>
  </tr>
  <tr>
    <td>*.contoso.com (萬用字元)</td>
    <td>*</td>
    <td>來自<a href="#vip">步驟 1</a> 的 IP 位址</td>
  </tr>
</table>

額外的 CNAME 記錄會採用從 awverify.&lt;subdomain>.&lt;rootdomain> 對應至 awverify.&lt;subdomain>.azurewebsites.net 的慣例。CNAME 記錄應該設定如下︰

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 範例</th>
    <th>CNAME 主機</th>
    <th>CNAME 值</th>
  </tr>
  <tr>
    <td>contoso.com (根網域)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (子網域)</td>
    <td>awverify.www</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (萬用字元)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### 建立 CNAME 記錄

如果您使用 CNAME 記錄對應至 Azure 應用程式的預設網域名稱，則不需要像您處理 A 記錄時的額外 CNAME 記錄。

>[AZURE.IMPORTANT] 請勿建立根網域的 CNAME 記錄 (也就是「根記錄」)。如需詳細資訊，請參閱[為什麼無法在根網域使用 CNAME 記錄](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)。若要將根網域對應至 Azure 應用程式，請改用 [A 記錄](#a)。

CNAME 記錄應該設定如下 (@ 通常代表根網域)︰

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 範例</th>
    <th>CNAME 主機</th>
    <th>CNAME 值</th>
  </tr>
  <tr>
    <td>www.contoso.com (子網域)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (萬用字元)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
## 步驟 3.為您的應用程式啟用自訂網域名稱

回到 Azure 入口網站中的 [使用外部網域] 刀鋒視窗 (請參閱[步驟 1](#vip))，您需要將自訂網域的完整網域名稱 (FQDN) 加入至清單。

1.	如果您尚未這麼做，請登入 [Azure 入口網站](https://portal.azure.com)。

2.	在 Azure 入口網站中，按一下左側功能表上的 [應用程式服務]。

4.	按一下您的應用程式，然後按一下 [設定] > [自訂網域和 SSL] > [帶出外部網域]。

2.	將自訂網域的 FQDN 加入至清單 (例如 **www.contoso.com**)。

    ![將自訂網域名稱對應至 Azure 應用程式：加入至網域名稱清單](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure 會嘗試確認您在這裡使用的網域名稱。確定它是您在[步驟 2](#createdns) 中為其建立 DNS 記錄的相同網域名稱。

6.  按一下 [儲存]。

7.  Azure 完成設定新的自訂網域名稱後，請在瀏覽器中瀏覽至您的自訂網域名稱。您現在應會看到您的應用程式執行中以及您的自訂

<a name="verify"></a>
## 確認 DNS 傳播

完成設定步驟之後，可能需要一些時間來傳播變更，視您的 DNS 提供者而定。您可以利用 [http://digwebinterface.com/](http://digwebinterface.com/) (英文) 確認 DNS 傳播是否如預期。前往該網站之後，在文字方塊中指定主機名稱，然後按一下 [Dig]。查看結果以確認最近的變更是否生效。

![將自訂網域名稱對應至 Azure 應用程式：驗證 DNS 傳播](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] 傳播 DNS 項目最多需要 48 小時 (有時候更久)。如果您已正確設定所有項目，仍然需要等待傳播成功。

## 後續步驟
了解如何[在 Azure 中購買 SSL 憑證](web-sites-purchase-ssl-web-site.md)或[使用來自其他位置的 SSL 憑證](web-sites-configure-ssl-certificate.md)來使用 HTTPS 保護自訂網域名稱。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

[開始使用 Azure DNS](../dns/dns-getstarted-create-dnszone.md) [在自訂網域中建立 Web 應用程式的 DNS 記錄](../dns/dns-web-sites-custom-domain.md) [將網域委派給 Azure DNS](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0810_2016---->