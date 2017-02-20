---
title: "使用現有的內部部署 Proxy 伺服器與 Azure AD | Microsoft Docs"
description: "涵蓋如何使用現有的內部部署 Proxy 伺服器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>使用現有的內部部署 Proxy 伺服器

本文說明如何設定應用程式 Proxy 連接器以使用輸出 Proxy 伺服器。 它適用於具有現有 Proxy 之網路環境的客戶。

我們先來看看這些主要部署案例︰
* 設定連接器以略過您的內部部署輸出 Proxy。
* 設定要使用輸出 Proxy 來存取 Azure AD 應用程式 Proxy 的連接器。

如需連接器運作方式的詳細資訊，請參閱 [如何為內部部署應用程式提供安全的遠端存取](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)。

## <a name="configure-your-connectors"></a>設定連接器

核心連接器服務會使用 Azure 服務匯流排來處理一些 Azure AD 應用程式 Proxy 服務的基礎通訊。 服務匯流排會伴隨其他組態需求。

有關解決 Azure AD 連線問題的詳細資訊，請參閱[如何疑難排解 Azure AD 應用程式 Proxy 連線問題](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems)。 

## <a name="configure-the-outbound-proxy"></a>設定輸出 Proxy

如果您的環境中有輸出 Proxy，請確定已適當地設定進行安裝的帳戶可使用輸出 Proxy。 因為安裝程式在進行安裝之使用者的內容中執行，可以使用 Microsoft Edge 或其他網際網路瀏覽器來完成此作業。 

若要使用 Microsoft Edge 設定 Proxy 設定，請移至 [設定]、[檢視進階設定]、[開啟 Proxy 設定]、[手動 Proxy 設定]。 將 [使用 Proxy 伺服器] 設定為 [開啟]，核取 [不使用本機內部網路位址的 Proxy 伺服器]，然後變更位址和連接埠，以反映您的本機 Proxy 伺服器。

填寫如下方 [選項] 方塊中所示的必要 Proxy 設定。

 ![AzureAD 略過本機位址](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>略過輸出 Proxy

根據預設，連接器用來進行輸出要求的基礎 OS 元件如果在環境中啟用，會使用 Web Proxy 自動探索 (WPAD) 自動嘗試尋找網路上的 Proxy 伺服器。

這通常會藉由執行 DNS 查閱 wpad.domainsuffix 來運作。 如果這在 DNS 中解決，則會對 wpad.dat 的 IP 位址提出 HTTP 要求，該值將為您環境中的 Proxy 組態指令碼。 然後連接器將使用這個來選取輸出 Proxy 伺服器。 不過，連接器流量可能會因為 Proxy 上所需的其他組態設定而仍然不會通過。 

在下一節中，我們將討論輸出 Proxy 上要將流量通過所需的設定步驟。 但是首先，讓我們說明設定連接器以略過您內部部署 Proxy，確保它會使用直接連線至 Azure 服務的方式。 這是建議的執行方式 (只要您的網路原則允許)，因為這表示您有一個要維護的較少組態。

若要停用連接器的輸出 Proxy 使用，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增下列程式碼範例中所示的 [system.net] 區段︰

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
若要確保連接器更新程式服務也會略過 Proxy，對位於 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 的 ApplicationProxyConnectorUpdaterService.exe.config 檔案進行類似的變更。

請確定您在需要還原至預設.config 檔案的情況下複製原始的檔案。 

## <a name="use-the-outbound-proxy-server"></a>使用輸出 Proxy 伺服器

如上所述，在某些客戶環境中，會要求所有輸出流量通過輸出 Proxy，沒有例外狀況。 如此一來，略過 Proxy 不是選項。

您可以設定連接器流量以過輸出 proxy，如下所示。

 ![AzureAD 略過本機位址](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

由於僅有輸出流量，不需要設定連接器之間的負載平衡，或透過您的防火牆設定內部存取。

在任何情況下，您將需要執行下列步驟：
1. 設定連接器和更新程式服務以通過輸出 Proxy。
2. 設定 Proxy 設定以允許 Azure AD 應用程式 Proxy 服務的連接。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步驟 1：設定連接器和相關服務以通過輸出 Proxy

如上所述，若 WPAD 在環境中啟用並正確地設定，連接器會自動探索輸出 Proxy 伺服器，並嘗試使用它。 不過，您可以明確地設定連接器以通過輸出 Proxy。 

若要這樣做，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增下列程式碼範例中所示的 [system.net] 區段︰

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>變更 _proxyserver:8080_ 以反映您的本機 Proxy 伺服器名稱或 IP 位址與其正在接聽的連接埠。
>

接著，您必須設定連接器更新程式服務來使用 Proxy，方法為對位於 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 的檔案進行類似變更。

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步驟 2︰設定 Proxy 以允許來自連接器與相關服務的流量通過

在輸出 Proxy 有 4 個層面需要考量：
* Proxy 輸出規則
* Proxy 驗證
* Proxy 連接埠
* SSL 審查

#### <a name="2a-proxy-outbound-rules"></a>2.A：Proxy 輸出規則
允許存取下列端點以取得連接器服務的存取權︰

* *.msappproxy.net 
* *.servicebus.microsoft.net 

針對初始註冊，允許存取下列端點︰

* login.windows.net 
* login.microsoftonline.com。 

連接器服務所使用的基礎服務匯流排控制通道另外需要特定 IP 位址的連線。 請注意，我們與服務匯流排團隊合作將改為完整的 FQDN。 不過，現在有兩個選項︰ 
 
* 允許連接器輸出存取所有目的地，或
* 允許連接器輸出存取列於 https://www.microsoft.com/en-gb/download/details.aspx?id=41653 的 Azure 資料中心 IP 範圍

>[!NOTE]
>使用 Azure 資料中心 IP 範圍清單的挑戰是它會更新每週，因此您必須適當放置處理程序，以確保您的存取規則會隨之更新。
>

#### <a name="2b-proxy-authentication"></a>2.B：Proxy 驗證

目前不支援 Proxy 驗證。 目前的建議是允許連接器匿名存取網際網路目的地。

#### <a name="2c-proxy-ports"></a>2.C：Proxy 連接埠

連接器可使用 CONNECT 方法根據連接進行輸出 SSL。 這基本上會透過輸出 Proxy 設定通道。 有些 Proxy 伺服器依預設只允許輸出通道至標準 SSL 連接埠 (例如 443)。 如果是這種情況，Proxy 伺服器必須設定為允許其他連接埠的通道。

設定 Proxy 伺服器以允許非標準 SSL 連接埠 8080、9090、9091 和 10100-10120 通道。

>[!NOTE]
>當服務匯流排在 HTTPS 上執行時，它會使用連接埠 443。 不過，根據預設，服務匯流排會嘗試導向 TCP 連線，且僅在直接連線失敗時，才會改為使用 HTTPS。 
> 

若要確認服務匯流排流量也會傳送輸出 Proxy 伺服器，您必須確定連接器無法直接連接到連接埠 9350、9352 和 5671 的 Azure 服務。

#### <a name="2d-ssl-inspection"></a>2.D：SSL 審查
請勿使用 SSL 審查進行連接器流量，因為這樣會導致連接器流量的問題。 

這樣就大功告成了。 現在您應該會看到所有的流量通過 Proxy。 如果您遇到問題，下列疑難排解步驟應該有幫助。

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>疑難排解連接器 Proxy 問題和服務連線問題

找出並疑難排解連接器連接問題的單一最佳方式，是在啟動連接器服務時，在連接器服務上進行網路擷取。 這可能是令人怯步的工作，因此讓我們看看關於擷取及篩選網路追蹤的快速提示。

您可以使用您選擇的監視工具。 基於本文的目的，我們可以使用 Microsoft Network Monitor 3.4，您可以在 https://www.microsoft.com/en-us/download/details.aspx?id=4865 下載。

以下我們所使用的範例與篩選器僅限網路監視器，但原則可以套用到任何分析工具。

### <a name="take-a-capture-using-microsoft-network-monitor"></a>使用 Microsoft Network Monitor 擷取

若要開始擷取，開啟網路監視器，並按一下新的擷取。 然後按 [開始] 按鈕以開始。

 ![AzureAD 網路監視器](./media/application-proxy-working-with-proxy-servers/network-capture.png)

完成擷取之後，按一下 [停止] 按鈕以結束擷取。

### <a name="take-a-capture-of-connector-traffic"></a>擷取連接器流量

如需初始的疑難排解，執行下列步驟︰ 

1. 從 services.msc，停止 Microsoft AAD 應用程式 Proxy 連接器服務 (如下所示)。
2. 啟動網路擷取。
3. 開始使用 Microsoft AAD 應用程式 Proxy 連接器服務。
4. 啟動網路擷取。

 ![AzureAD 網路監視器](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>查看 Proxy 伺服器要求的連接器

現在，您有網路擷取，準備好要進行篩選。 查看追蹤的關鍵在於了解如何篩選擷取。

這個的其中一個篩選條件如下所示 (其中 8080 是 Proxy 服務連接埠)︰ 

(http.Request 或 http.Response) 和 tcp.port==8080

如果您在 [顯示篩選器] 視窗中輸入此篩選器並選取 [套用]，它會根據篩選條件篩選擷取的流量。

上述的篩選條件只會顯示 HTTP 要求和往返 Proxy 連接埠的回應。 針對連接器設定為使用 Proxy 伺服器的連接器啟動，這會顯示如下︰

 ![AzureAD 網路監視器](./media/application-proxy-working-with-proxy-servers/http-requests.png)

您現在特別尋找顯示我們所談的 Proxy 伺服器之 CONNECT 要求。 成功時，您會收到 HTTP OK (200) 回應。

如果您看到其他回應碼，例如 407 或 502，這表示 Proxy 需要驗證或由於某些其他原因不允許流量。 此時您會與您的 Proxy 伺服器支援團隊合作。

### <a name="identify-failed-tcp-connection-attempts"></a>識別失敗的 TCP 連線嘗試

您可能感興趣的其他常見案例是當連接器嘗試直接連線但是失敗。 

另一個可協助您更容易找出這個的網路監視器篩選條件是︰

property.TCPSynRetransmit

SYN 封包是傳送到建立 TCP 連線的第一個封包。 如果這不會傳回回應，則會重新嘗試 SYN。 上述的篩選器可讓您查看任何重新傳輸的 SYN。 然後，您可以查詢以查看這些是否對應至任何與連接器相關的流量。 

下列範例會示範失敗的服務匯流排連接埠 9352 連線嘗試︰

 ![AzureAD 網路監視器](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

如果您看到類似上述的回應，這表示連接器嘗試直接與 Azure 服務匯流排服務交談。 如果您希望該連接器直接連線至 Azure 服務，則這清楚表示有網路/防火牆問題。

>[!NOTE]
>如果您已設定為使用 Proxy 伺服器，這可能是服務匯流排在切換之前嘗試直接 TCP 連線，以嘗試透過 HTTPS 連線，所以請記得這一點。
>

網路追蹤分析並非適合每個人。 但它可以是非常有用的工具，能快速取得您網路運作情況的資訊。 

如果您繼續遭遇連接器連線問題，請與我們的支援小組建立票證，他們很高興能協助您進行進一步疑難排解。

如需有關解決應用程式 Proxy 連接器錯誤的詳細資訊，請參閱[疑難排解應用程式 Proxy](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot)。

## <a name="next-steps"></a>後續步驟

[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)<br>
[如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


