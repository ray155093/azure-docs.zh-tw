---
title: Logic Apps 安裝內部部署資料閘道 | Microsoft Docs
description: 如何安裝內部部署資料閘道用於邏輯應用程式的相關資訊。
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan

---
# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>安裝 Logic Apps 的內部部署資料閘道
## <a name="installation-and-configuration"></a>安裝和設定
### <a name="prerequisites"></a>必要條件
最低：

* .NET 4.5 Framework
* 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

建議使用：

* 8 核心 CPU
* 8 GB 記憶體
* 64 位元版本的 Windows 2012 R2 (或更新版本)

相關考量：

* 您不能在網域控制站上安裝閘道。
* 您不能在可能會被關閉、處於睡眠狀態，或未連線到網際網路的電腦 (例如膝上型電腦) 上安裝閘道，因為閘道無法在上述情況下執行。 此外，透過無線網路的閘道效能可能會受到影響。

### <a name="install-a-gateway"></a>安裝閘道
您可以 [在這裡取得內部部署資料閘道的安裝程式](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

指定 [內部部署資料閘道]  模式、使用工作或學校帳戶登入，然後設定新閘道，或者移轉、還原或取代現有的閘道。

* 若要設定閘道，請輸入閘道的**名稱**和**修復金鑰**，然後按一下或點選 [設定]。
  
    指定至少包含 8 個字元的修復金鑰，並將它保存在安全的地方。 如果您想要移轉、還原或取代其閘道，您會需要這個金鑰。
* 若要移轉、還原或取代現有的閘道，請提供建立閘道時所指定的修復金鑰。

### <a name="restart-the-gateway"></a>重新啟動閘道
閘道會以 Windows 服務執行，如同任何其他 Windows 服務一般，可以使用多種方式啟動和停止。 例如，您可以在閘道執行所在的電腦上以提高的權限開啟命令提示字元並執行其中一個命令︰

* 若要停止服務，請執行此命令：
  
    `net stop PBIEgwService`
* 若要啟動服務，請執行此命令：
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>設定防火牆或 Proxy
如需如何為閘道提供 Proxy 資訊的相關資訊，請參閱 [設定 Proxy 設定](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/)。

您可以從 PowerShell 提示字元執行下列命令，確認您的防火牆或 Proxy 是否被封鎖連線。 這會測試 Azure 服務匯流排的連線能力。 這只會測試網路連線能力，不會使用雲端伺服器服務或閘道進行任何動作。 這有助於判斷您的電腦是否可以實際連線到網際網路。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

結果應該類似此範例。 如果 **TcpTestSucceeded** 不是 True，您可能被防火牆封鎖。

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

如果您想要更詳細的資訊，請將 **ComputerName** 和 **Port** 取代為本主題稍後的[設定連接埠](#configure-ports)之下所列的值。

防火牆也可能會封鎖 Azure 服務匯流排到 Azure 資料中心的連線。 如果是這樣，您可以針對這些資料中心的區域，將所有 IP 位址放入允許清單 (解除封鎖)。 您可以 [在這裡取得 Azure IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)清單。

### <a name="configure-ports"></a>設定連接埠
閘道會建立 Azure 服務匯流排的輸出連接。 閘道會與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672、9350 到 9354。 閘道不需要輸入連接埠。

深入了解[混合式解決方案](../service-bus/service-bus-fundamentals-hybrid-solutions.md)。

| 網域名稱 | 輸出連接埠 | 描述 |
| --- | --- | --- |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |進階訊息佇列通訊協定 (AMQP) |
| *.servicebus.windows.net |443、9350-9354 |透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |如果 Power BI 服務無法連接至閘道，則用來測試網際網路連線能力。 |

如果您需要將 IP 位址而不是網域加入允許清單，您可以下載並使用 [Microsoft Azure Datacenter IP ranges list (Microsoft Azure 資料中心 IP 範圍清單)](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情況下，Azure 服務匯流排會與 IP 位址而非完整的網域名稱進行連接。

### <a name="sign-in-account"></a>登入帳戶
使用者將使用工作或學校帳戶登入。 這是您的組織帳戶。 如果您註冊 Office 365 供應項目，但是未提供實際的公司電子郵件，可能會看起來像 jeff@contoso.onmicrosoft.com. 您的帳戶在雲端服務中會儲存在 Azure Active Directory (AAD) 的租用戶中。 在大部分情況下，AAD 帳戶的 UPN 會與電子郵件地址一致。

### <a name="windows-service-account"></a>Windows 服務帳戶
內部部署資料閘道設定為使用 NT SERVICE\PBIEgwService 做為 Windows 服務的登入認證。 根據預設，它有登入為服務的權限。 這是您安裝閘道所在電腦的內容。

這不是用來連接到內部部署資料來源的帳戶，或是您用來登入雲端服務的工作或學校帳戶。

## <a name="frequently-asked-questions"></a>常見問題集
### <a name="general"></a>一般
**問**：閘道支援何種資料來源？<br/>
**答**：SQL Server (截至本文撰寫時)。

**問**︰我在雲端中的資料來源 (例如 SQL Azure) 是否需要閘道？ <br/>
**答**：否。 閘道只連接至內部部署資料來源。

**問**：實際的 Windows 服務名稱為何？<br/>
**答**：在「服務」中，閘道稱為 Power BI Enterprise Gateway Service。

**問**︰從雲端是否有任何輸入連接連至閘道？ <br/>
**答**：否。 閘道使用連至 Azure 服務匯流排的輸出連接。

**問**︰如果我封鎖輸出連接會如何？ 我需要開啟什麼嗎？ <br/>
**答**：查看閘道使用的連接埠和主機。

**問**︰閘道必須安裝在與資料來源相同的電腦上嗎？ <br/>
**答**：否。 閘道將使用所提供的連接資訊連接至資料來源。 此時請將閘道視為用戶端應用程式。 它只需要能夠連接到所提供的伺服器名稱。

**問**︰從閘道向資料來源執行查詢時的延遲為何？ 最佳的架構為何？ <br/>
**答**：若要減少網路延遲，請盡可能靠近資料來源安裝閘道。 如果您可以在實際的資料來源上安裝閘道，就能將所產生的延遲降到最低。 請一併考量資料中心。 例如，如果您的服務使用美國西部的資料中心，而您在 Azure VM 中裝載 SQL Server，最好就將 Azure VM 放在美國西部。 如此一來能將延遲降到最低，而且避免 Azure VM 的輸出費用。

**問**︰網路頻寬是否有任何需求？ <br/>
**答**：建議您的網路連線要有良好的輸送量。 每個環境都不同，但是傳送的資料量會影響結果。 使用 ExpressRoute 可以協助您確保在內部部署與 Azure 資料中心之間有一定的輸送量等級。

您可以使用協力廠商工具 Azure 速度測試應用程式，協助您量測輸送量。

**問**︰閘道 Windows 服務可以使用 Azure Active Directory 帳戶執行嗎？ <br/>
**答**：否。 Windows 服務必須要有有效的 Windows 帳戶。 根據預設，它會使用服務 SID，NT SERVICE\PBIEgwService 執行。

**問**︰結果如何傳送回雲端？ <br/>
**答**：透過 Azure 服務匯流排進行。 如需詳細資訊，請參閱其運作方式。

**問**︰我的認證儲存在哪裡？ <br/>
**答**：您針對資料來源輸入的認證會以加密方式儲存在閘道雲端服務中。 認證會在閘道內部部署進行解密。

### <a name="high-availability/disaster-recovery"></a>高可用性/災害復原
**問**︰有計劃針對閘道啟用高可用性案例嗎？ <br/>
**答**：我們正在規劃中，但是還沒有時間表。

**問**︰災害復原有哪些選項？ <br/>
**答**：您可以使用修復金鑰還原或移動閘道。 當您安裝閘道時，請指定修復金鑰。

**問**︰修復金鑰有什麼好處？ <br/>
**答**：修復金鑰可以提供方法移轉或在災害後復原閘道設定。

### <a name="troubleshooting"></a>疑難排解
**問**︰閘道記錄檔在哪裡？ <br/>
**答**：請參閱本主題稍後的「工具」。

**問**︰如何查看有哪些查詢正要傳送至內部部署資料來源？ <br/>
**答**：您可以啟用查詢追蹤，其中包含要傳送的查詢。 完成疑難排解時，請務必將它變更回原始值。 啟用查詢追蹤會導致記錄檔變大。

您也可以查看資料來源具備的工具是否有追蹤查詢。 例如，您可以使用 SQL Server 和 Analysis Services 的擴充事件或 SQL Profiler。

## <a name="how-the-gateway-works"></a>閘道運作方式
當使用者與連接至內部部署資料來源的項目互動時︰

1. 雲端服務會建立查詢，以及資料來源的加密認證，並將查詢傳送至閘道的佇列處理。
2. 服務會分析該查詢，並將要求推送至 Azure 服務匯流排。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連接至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務。 服務接著就會使用結果。

## <a name="troubleshooting"></a>疑難排解
### <a name="update-to-the-latest-version"></a>更新為最新版本
閘道版本過期時，可能會出現很多問題。  一般最好的方法就是先確定您使用的是最新版本。  如果有一個月以上未更新閘道，您可能要考慮安裝最新版的閘道，並看看是否能重現問題。

### <a name="error:-failed-to-add-user-to-group.-(-2147463168-pbiegwservice-performance-log-users-)"></a>錯誤︰無法將使用者新增到群組。 (-2147463168 PBIEgwService Performance Log Users)
如果您嘗試在不支援的網域控制站上安裝閘道，可能會收到這個錯誤。 您必須在不是網域控制站的電腦上部署閘道。

## <a name="tools"></a>工具
### <a name="collecting-logs-from-the-gateway-configurator"></a>從閘道設定程式收集記錄檔
您可以收集閘道的數個記錄檔。 一律先從記錄檔開始！

#### <a name="installer-logs"></a>安裝程式記錄檔
`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>組態記錄檔
`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企業閘道服務記錄檔
`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件記錄檔
資料管理閘道和 PowerBIGateway 記錄檔位於「應用程式及服務記錄檔」 之下。

### <a name="fiddler-trace"></a>Fiddler 追蹤
[Fiddler](http://www.telerik.com/fiddler) 是一個 Telerik 公司開發，用來監視 HTTP 流量的免費工具。  您可以使用用戶端電腦的 Power BI 服務來回查看。 這可能可以顯示錯誤和其他相關資訊。

## <a name="next-steps"></a>後續步驟
* [建立 Logic Apps 的內部部署連接](app-service-logic-gateway-connection.md)
* [企業整合功能](app-service-logic-enterprise-integration-overview.md)
* [Logic Apps 連接器](../connectors/apis-list.md)

<!--HONumber=Oct16_HO2-->


