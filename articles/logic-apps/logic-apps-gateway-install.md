---
title: "安裝內部部署資料閘道 - Azure Logic Apps | Microsoft Docs"
description: "藉由安裝內部部署資料閘道來存取邏輯應用程式中的內部部署資料"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b9971117d5f61669a5161a28c96b11b2fd600b61
ms.lasthandoff: 04/14/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>安裝 Azure Logic Apps 的內部部署資料閘道

內部部署資料閘道支援這些連線︰

*   BizTalk Server
*   DB2  
*   檔案系統
*   Informix
*   MQ
*   MySQL
*   Oracle 資料庫 
*   SAP 應用程式伺服器 
*   SAP 訊息伺服器
*   僅限 SharePoint for HTTP，而非 SharePoint for HTTPS
*   SQL Server
*   Teradata

如需這些連線的詳細資訊，請參閱[Azure Logic Apps 的連接器](https://docs.microsoft.com/azure/connectors/apis-list)。

## <a name="installation-and-configuration"></a>安裝和設定

### <a name="requirements"></a>需求

最低：

* .NET 4.5 Framework
* 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

建議使用：

* 8 核心 CPU
* 8 GB 記憶體
* 64 位元版本的 Windows 2012 R2 (或更新版本)

相關考量：

* 請只在本機電腦上安裝內部部署資料閘道。
您不能在網域控制站上安裝閘道。

* 請勿將閘道安裝在可能關閉、進入睡眠狀態，或未連線到網際網路的電腦上，因為閘道無法在這些情況下執行。 此外，透過無線網路的閘道效能可能會受到影響。

* 您只能在 Azure 中使用工作或學校電子郵件地址，因此您可以將內部部署資料閘道關聯至以 Azure Active Directory 為基礎的帳戶。

    如果您使用 Microsoft 帳戶 (例如 @outlook.com)，您可以使用 Azure 帳戶  [建立工作或學校電子郵件地址](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)。

### <a name="install-the-gateway"></a>安裝閘道

1.    [在此下載內部部署資料閘道的安裝程式](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2.    指定 [內部部署資料閘道] 做為模式。

3. 使用 Azure 工作或學校帳戶登入。 

4. 設定新閘道，或者，您可以移轉、還原或取代現有閘道。

    若要設定閘道，請提供閘道名稱和修復金鑰，然後選擇 [設定]。
  
    指定至少包含 8 個字元的修復金鑰，並將此金鑰保存在安全的地方。 如果您想要移轉、還原或取代閘道，您會需要這個金鑰。

    若要移轉、還原或取代現有的閘道，請提供建立閘道時所指定的修復金鑰。

### <a name="restart-the-gateway"></a>重新啟動閘道

閘道會以 Windows 服務的形式來執行，因此和任何其他 Windows 服務一樣，您可以透過多種方式啟動和停止服務。 例如，您可以在閘道執行所在的電腦上以提高的權限開啟命令提示字元，並執行下列其中一個命令︰

* 若要停止服務，請執行此命令：
  
    `net stop PBIEgwService`

* 若要啟動服務，請執行此命令：
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>設定防火牆或 Proxy

若要為閘道提供 Proxy 資訊，請參閱[設定 Proxy 設定](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)。

您可以從 PowerShell 提示字元執行下列命令，確認您的防火牆或 Proxy 是否封鎖連線。 此命令會測試是否能夠連線到 Azure 服務匯流排，並且只會測試網路連線能力，所以此命令與雲端伺服器服務或閘道沒有關係。 此測試有助於判斷您的電腦是否可以實際連線到網際網路。

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

如果您想要更詳細的資訊，請將 **ComputerName** 和 **Port** 值取代為本主題的[設定連接埠](#configure-ports)之下所列的值。

防火牆也可能會封鎖 Azure 服務匯流排到 Azure 資料中心的連線。 若是如此，請核准 (解除封鎖) 您所在區域之資料中心的所有 IP 位址。
您可以 [在這裡取得 Azure IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)清單。

### <a name="configure-ports"></a>設定連接埠
閘道會建立對 Azure 服務匯流排的輸出連線，並與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672 和 9350 到 9354。 閘道不需要輸入連接埠。

深入了解[混合式解決方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

| 網域名稱 | 輸出連接埠 | 描述 |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | 進階訊息佇列通訊協定 (AMQP) | 
| *.servicebus.windows.net | 443、9350-9354 | 透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | 當 Power BI 服務無法連線至閘道時，用來測試網際網路連線能力。 | 

如果您必須核准 IP 位址而非網域，您可以下載並使用 [Microsoft Azure 資料中心 IP 範圍清單](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情況下，Azure 服務匯流排連線會使用 IP 位址而非完整網域名稱來建立。

### <a name="sign-in-accounts"></a>登入帳戶

您可以使用工作或學校帳戶 (也就是您的組織帳戶) 來登入。 如果您註冊 Office 365 供應項目，但是未提供實際的公司電子郵件，您的登入位址看起來可能會像 jeff@contoso.onmicrosoft.com。 您在雲端服務中的帳戶會儲存在 Azure Active Directory (Azure AD) 的租用戶中。 Azure AD 帳戶的 UPN 通常會與電子郵件地址相符。

### <a name="windows-service-account"></a>Windows 服務帳戶

對於 Windows 服務登入認證，內部部署資料閘道會設定為使用 NT SERVICE\PBIEgwService。 根據預設，在您安裝閘道所在之電腦的內容中，閘道有「登入為服務」的權限。

此服務帳戶與用來連線至內部部署資料來源的帳戶不同，也不是您用來登入雲端服務的工作或學校帳戶。

## <a name="how-the-gateway-works"></a>閘道運作方式
當其他人與連線至內部部署資料來源的項目互動時︰

1. 雲端服務會建立查詢，以及資料來源的加密認證，並將查詢傳送至閘道的佇列處理。
2. 服務會分析該查詢，並將要求推送至 Azure 服務匯流排。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連線至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務。 服務接著就會使用結果。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="general"></a>一般

**問**︰我在雲端中的資料來源 (例如 SQL Azure) 是否需要閘道？ <br/>
**答**：否。 閘道只連接至內部部署資料來源。

**問**：實際的 Windows 服務名稱為何？<br/>
**答**：在「服務」中，閘道稱為 Power BI Enterprise Gateway Service。

**問**︰從雲端是否有任何輸入連接連至閘道？ <br/>
**答**：否。 閘道使用連至 Azure 服務匯流排的輸出連接。

**問**︰如果我封鎖輸出連接會如何？ 我需要開啟什麼嗎？ <br/>
**答**：查看閘道使用的連接埠和主機。

**問**︰閘道必須安裝在與資料來源相同的電腦上嗎？ <br/>
**答**：否。 閘道會使用所提供的連線資訊連線至資料來源。 在此請將閘道視為用戶端應用程式。 閘道只需要能夠連線到所提供的伺服器名稱。

**問**︰從閘道向資料來源執行查詢時的延遲為何？ 最佳的架構為何？ <br/>
**答**：若要減少網路延遲，請盡可能靠近資料來源安裝閘道。 如果您可以在實際的資料來源上安裝閘道，此鄰近程度就能將所產生的延遲降到最低。 請一併考量資料中心。 例如，如果您的服務使用美國西部的資料中心，而您在 Azure VM 中裝載 SQL Server，Azure VM 也應該位於美國西部。 此鄰近程度能將延遲降到最低，並避免 Azure VM 的輸出費用。

**問**︰網路頻寬是否有任何需求？ <br/>
**答**︰我們的建議是，您的網路連線要有良好的輸送量。 每個環境都是不同的，而且所傳送的資料量會影響結果。 使用 ExpressRoute 可以協助您確保在內部部署與 Azure 資料中心之間有一定的輸送量等級。

您可以使用第三方工具 Azure 速度測試應用程式，協助您量測輸送量。

**問**︰閘道 Windows 服務可以使用 Azure Active Directory 帳戶執行嗎？ <br/>
**答**：否。 Windows 服務必須要有有效的 Windows 帳戶。 根據預設，此服務會使用服務 SID，NT SERVICE\PBIEgwService 執行。

**問**︰結果如何傳送回雲端？ <br/>
**答**︰結果會透過 Azure 服務匯流排傳送。

**問**︰我的認證儲存在哪裡？ <br/>
**答**：您針對資料來源輸入的認證會以加密方式儲存在閘道雲端服務中。 認證會在內部部署閘道進行解密。

### <a name="high-availabilitydisaster-recovery"></a>高可用性/災害復原
**問**︰有計劃針對閘道啟用高可用性案例嗎？ <br/>
**答**：這些案例正在規劃中，但是還沒有時間表。

**問**︰災害復原有哪些選項？ <br/>
**答**：您可以使用修復金鑰還原或移動閘道。 當您安裝閘道時，請指定修復金鑰。

**問**︰修復金鑰有什麼好處？ <br/>
**答**：修復金鑰可在災害發生後讓您有辦法移轉或復原閘道設定。

## <a name="troubleshooting"></a>疑難排解

**問**︰閘道記錄檔在哪裡？ <br/>
**答**：請參閱本主題稍後的「工具」。

**問**︰如何查看有哪些查詢正要傳送至內部部署資料來源？ <br/>
**答**：您可以啟用查詢追蹤，其中包含要傳送的查詢。 完成疑難排解時，請務必將查詢追蹤變更回原始值。 讓查詢追蹤保持開啟會產生較大的記錄。

您也可以查看資料來源具備的工具是否有追蹤查詢。 例如，您可以使用 SQL Server 和 Analysis Services 的擴充事件或 SQL Profiler。

### <a name="update-to-the-latest-version"></a>更新為最新版本

閘道版本過期時，可能會出現很多問題。 良好的一般作法是確實使用最新版本。 如果有一個月以上未更新閘道，您可以考慮安裝最新版的閘道，並看看是否能重現問題。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>錯誤︰無法將使用者新增到群組。 (-2147463168 PBIEgwService Performance Log Users)

如果您嘗試在不支援的網域控制站上安裝閘道，可能會收到這個錯誤。 請確定您是在非網域控制站的電腦上部署閘道。

## <a name="tools"></a>工具
### <a name="collect-logs-from-the-gateway-configurer"></a>從閘道設定程式收集記錄

您可以收集閘道的數個記錄檔。 一律先從記錄檔開始！

#### <a name="installer-logs"></a>安裝程式記錄檔

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>組態記錄檔

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企業閘道服務記錄檔

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件記錄檔

您可以在 [應用程式及服務記錄] 底下找到資料管理閘道和 PowerBIGateway 記錄。

### <a name="fiddler-trace"></a>Fiddler 追蹤

[Fiddler](http://www.telerik.com/fiddler) 是一個 Telerik 公司開發，用來監視 HTTP 流量的免費工具。 您可以從用戶端電腦使用 Power BI 服務來查看此流量。 此服務可能會顯示錯誤和其他相關資訊。

## <a name="next-steps"></a>後續步驟
    
* [從邏輯應用程式連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)

