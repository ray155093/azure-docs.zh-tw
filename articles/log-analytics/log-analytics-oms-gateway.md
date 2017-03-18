---
title: "使用 OMS 閘道將電腦和裝置連線到 OMS | Microsoft Docs"
description: "將您受 OMS 管理的裝置與受 Operations Manager 監視的電腦與 OMS 閘道連線，以在它們沒有網際網路存取時傳送資料給 OMS 服務。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: de2e6d201ba54774697356e1cd55c4881664a401
ms.lasthandoff: 02/28/2017


---
# <a name="connect-computers-and-devices-without-internet-access-to-oms-using-the-oms-gateway"></a>在無網際網路存取下使用 OMS 閘道將電腦和裝置連線到 OMS

此文件說明您受 OMS 管理的裝置與受 System Center Operations Manager (SCOM) 監視的電腦如何在它們沒有網際網路存取時傳送資料給 OMS 服務。 OMS 閘道可以收集資料並代表它們將資料傳送給 OMS 服務。

閘道是一個 HTTP 正向 Proxy，使用 HTTP CONNECT 命令支援 HTTP 通道。 當閘道在執行 Windows 的 4 核心 CPU、16 GB 伺服器上執行時，最多可以處理 2000 個同時連線至 OMS 的裝置。

例如，您的企業或大型組織可能有具備網路連線但沒有網際網路連線的伺服器。 另一個範例是，您可能有許多銷售點 (POS) 裝置，但沒有直接監視它們的方法。 而在另一個範例中，Operations Manager 可使用 OMS 閘道做為 Proxy 伺服器。 在這些範例中，OMS 閘道可以將來自那些伺服器或 POS 裝置上所安裝代理程式的資料傳輸到 OMS。

所有代理程式資料都是透過具備網際網路連線的單一電腦傳送，而不是由每個獨立代理程式直接將資料傳送給 OMS，且需要直接網際網路連線。 該電腦則是您安裝及使用閘道的所在。 在這個案例中，您可以在您想要收集資料的任何電腦上安裝代理程式。 然後閘道會將資料直接從代理程式傳輸到 OMS—閘道不會分析所傳輸的任何資料。

您必須將 OMS 代理程式安裝在也安裝了閘道的電腦上。 這麼做可讓您監視 OMS 閘道，並分析其安裝所在之伺服器的效能或事件資料。 此外，該代理程式也可協助 OMS 閘道識別其需要通訊的服務端點。

代理程式必須具備網際網路存取，才能將資料上傳到 OMS。 每個代理程式也必須具備對其閘道的網路連線，代理程式才能自動將資料傳輸到閘道，或從閘道傳輸資料。 為求最佳效果，請不要在也是網域控制站的電腦上安裝閘道。

以下是顯示從直接代理程式到 OMS 的資料流程的圖表。

![直接代理程式圖表](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

以下是顯示從 Operations Manager 到 OMS 的資料流程的圖表。

![Operations Manager 圖表](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>提供的語言

OMS 閘道有下列語言版本︰

- 中文 (簡體)
- 中文 (繁體)
- 捷克文
- 荷蘭文
- English
- 法文
- 德文
- 匈牙利文
- 義大利文
- 日文
- 韓文
- 波蘭文
- 葡萄牙文 (巴西)
- 葡萄牙文 (葡萄牙)
- 俄文
- 西班牙文 (國際)

## <a name="download-the-oms-gateway"></a>下載 OMS 閘道

有三種方法可以取得 OMS 閘道的安裝程式檔案。

### <a name="microsoft-download-center"></a>Microsoft 下載中心

- 從 [Microsoft 下載中心](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi)最新版 OMS 閘道。

### <a name="oms-portal"></a>OMS 入口網站

1.    登入 OMS 工作區。
2.    選取 [設定] > [連接的來源] > [Windows 伺服器]。
3.    按一下 [下載 OMS 閘道]。


### <a name="azure-portal"></a>Azure 入口網站

1. 移至 [Azure 入口網站](https://portal.azure.com)並登入，瀏覽服務清單，然後選取 [Log Analytics]。
2. 選取工作區。
3. 在 [工作區] 刀鋒視窗的 [一般] 下方，按一下 [快速入門]。
4. 在 [選擇用來連線到工作區的資料來源] 下方，按一下 [電腦]。
4. 在 [直接代理程式] 刀鋒視窗中，按一下 [下載 OMS 閘道]。  
    ![下載 OMS 閘道](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>安裝 OMS 閘道
安裝此閘道會取代您已安裝的舊版本閘道 (Log Analytics 轉寄站)。

先決條件：.Net Framework 4.5、Windows Server 2012 R2 SP1 和更新版本


1. 若要開始安裝，請按兩下 **OMS Gateway.msi**。
2. 在 [歡迎] 頁面上，按一下 [下一步]。  
    ![閘道設定精靈](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. 在 [授權合約] 頁面上，選取 [我接受授權合約中的條款] 以同意 EULA，然後按一下 [下一步]。
4. 在連接埠和 Proxy 位址頁面上：
   1. 輸入要用於閘道的 TCP 連接埠號碼。 安裝程式會從 Windows 防火牆開啟此連接埠號碼。 預設值為 8080。
      連接埠號碼的有效範圍是 1 到 65535。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。
   2. 如果閘道安裝所在的伺服器需要使用 Proxy，您也可以選擇輸入閘道需要連線的 Proxy 位址。 例如，`http://myorgname.corp.contoso.com:80` 如果空白，閘道將會嘗試直接連線到網際網路。 否則，閘道會連線到 Proxy。 如果您的 Proxy 伺服器需要驗證，請輸入您的使用者名稱與密碼。  
       ![閘道精靈 Proxy 組態](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. 按一下 [下一步]
5. 如果您沒有啟用 Microsoft Update，會顯示 Microsoft Update 頁面，您可以在其中選擇啟用 Microsoft Update。 選擇想要的選項，然後按一下 [下一步]。 否則，請繼續下一個步驟。
6. 在 [目的地資料夾] 頁面上，保留預設資料夾 c:\ProgramFiles\OMS 或輸入您想要安裝閘道的位置，然後按一下 [下一步]。
7. 在 [準備安裝] 頁面上，按一下 [安裝]。 可能會顯示 [使用者帳戶控制] 要求安裝權限。 如果有顯示，請按一下 [是]。
8. 安裝完成後，請按一下 [完成]。 您可以透過開啟 services.msc 嵌入式管理單元來確認服務正在執行，並確認服務清單中已顯示 [OMS 閘道]。  
    ![服務 – OMS 閘道](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>在裝置上安裝代理程式
如有需要，請參閱[將 Windows 電腦連線到 Log Analytics](log-analytics-windows-agents.md) 以了解如何安裝直接連線代理程式的相關資訊。 此文章說明您可以如何使用安裝精靈或使用命令列來安裝代理程式。

## <a name="configure-oms-agents"></a>設定 OMS 代理程式
請參閱[使用 Microsoft Monitoring Agent 設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)了解設定代理程式來使用 Proxy 伺服器 (此案例為閘道) 的相關資訊。

Operations Manager 代理程式會透過管理伺服器傳送一些資料，例如 Operations Manager 警示、組態評估、執行個體空間，以及容量資料。 其他大量資料 (例如 IIS 記錄、效能及安全性) 會直接傳送到 OMS 閘道。 請參閱[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)來取得透過每個通道所傳送資料的完整清單。

> [!NOTE]
> 如果您打算搭配網路負載平衡使用閘道，請參閱[選擇性設定網路負載平衡](#optionally-configure-network-load-balancing)。
>
>

## <a name="configure-a-scom-proxy-server"></a>設定 SCOM Proxy 伺服器
您可以設定 Operations Manager 來將閘道加入做為 Proxy 伺服器使用。 當您更新 Proxy 組態時，Proxy 組態會自動套用到向 Operations Manager 回報的所有代理程式。

若要使用閘道來支援 Operations Manager，您需要：

* 在閘道伺服器上安裝 Microsoft Monitoring Agent (代理程式版本 – **8.0.10900.0** 與更新版本)，並針對您想要與之通訊的 OMS 工作區進行設定。
* 閘道必須有網際網路連線，或連線到有網際網路連線的 Proxy 伺服器。

### <a name="to-configure-scom-for-the-gateway"></a>為閘道設定 SCOM
1. 開啟 Operations Manager 主控台並在 [Operations Management Suite] 底下，按一下 [連線]，然後按一下 [設定 Proxy 伺服器]：  
    ![Operations Manager – 設定 Proxy 伺服器](./media/log-analytics-oms-gateway/scom01.png)
2. 選取 [使用 Proxy 伺服器來存取 Operations Management Suite]，然後輸入 OMS 閘道伺服器的 IP 位址。 確定您已在開頭使用 `http://` 前置詞：  
    ![Operations Manager – Proxy 伺服器位址](./media/log-analytics-oms-gateway/scom02.png)
3. 按一下 [完成] 。 您的 Operations Manager 伺服器已經連線到您的 OMS 工作區。

## <a name="configure-network-load-balancing"></a>設定網路負載平衡
您可以透過建立叢集，使用網路負載平衡設定閘道以取得高可用性。 叢集會管理來自您代理程式的流量，方法是在其節點間重新導向來自 Microsoft Monitoring Agent 的要求的連線。 如果閘道伺服器故障，流量就會被重新導向到其他節點。

1. 開啟網路負載平衡管理員，然後建立叢集。
2. 在新增閘道之前先用滑鼠右鍵按一下叢集，然後選取 [叢集內容]。 設定叢集來讓叢集有自己的 IP 位址：  
    ![網路負載平衡管理員 – 叢集 IP 位址](./media/log-analytics-oms-gateway/nlb01.png)
3. 若要連線已安裝 Microsoft Monitoring Agent 的 OMS 閘道伺服器，請用滑鼠右鍵按一下叢集的 IP 位址，然後按一下 [新增主機到叢集]。  
    ![網路負載平衡管理員 – 新增主機到叢集](./media/log-analytics-oms-gateway/nlb02.png)
4. 輸入您要連線之閘道伺服器的 IP 位址：  
    ![網路負載平衡管理員 – 新增主機到叢集：連線](./media/log-analytics-oms-gateway/nlb03.png)
5. 在沒有網際網路連線的電腦上，請確定您在設定 [Microsoft Monitoring Agent 內容] 時是使用叢集的 IP 位址：  
    ![Microsoft Monitoring Agent 內容 – Proxy 設定](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>設定自動 Hybrid Worker
如果您的環境中有自動化 Hybrid Worker，下列步驟可提供手動、暫時性解決方式來設定閘道以支援它們。

在下列步驟中，您需要知道自動化帳戶所在的 Azure 區域。 找出位置：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 自動化服務。
3. 選取適當的 Azure 自動化帳戶。
4. 在 [位置] 底下檢視其區域。  
    ![Azure 入口網站 – 自動化帳戶位置](./media/log-analytics-oms-gateway/location.png)

使用下列表格來識別每個位置的 URL：

**作業執行階段資料服務 URL**

| **位置** | **URL** |
| --- | --- |
| 美國中北部 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亞 |ase-jobruntimedata-prod-su1.azure-automation.net |

**代理程式服務 URL**

| **位置** | **URL** |
| --- | --- |
| 美國中北部 |ncus-agentservice-prod-1.azure-automation.net |
| 西歐 |we-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞 |ase-agentservice-prod-1.azure-automation.net |

如果您的電腦是使用更新管理方案自動註冊為 Hybrid Worker 以進行修補，請使用下列步驟：

1. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 使用以下 PowerShell Cmdlet 重新啟動 OMS 閘道服務：`Restart-Service OMSGatewayService`

如果您的電腦已使用 Hybrid Worker 註冊 Cmdlet 加入 Azure 自動化，請使用下列步驟：

1. 將代理程式服務註冊 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如：`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. 重新啟動 OMS 閘道服務。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet
Cmdlet 可以協助您完成更新 OMS 閘道的組態設定時需執行的作業。 在您使用它們之前，請務必先：

1. 安裝 OMS 閘道 (MSI)。
2. 開啟 PowerShell 視窗。
3. 若要匯入模組，請輸入此命令：`Import-Module OMSGateway`
4. 如果上一個步驟沒有發生錯誤，就表示模組已經成功匯入，且可以使用 Cmdlet。 輸入 `Get-Module OMSGateway`
5. 在您使用 Cmdlet 進行變更之後，請確定您已重新啟動閘道服務。

如果您在步驟 3 發生錯誤，表示模組並未匯入。 當 PowerShell 找不到模組時，就可能會發生錯誤。 您可以在閘道的安裝路徑中找到它：C:\Program Files\Microsoft OMS Gateway\PowerShell。

| **Cmdlet** | **參數** | **描述** | **範例** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務的組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |金鑰 |取得服務的組態 |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |位址 <br> 使用者名稱 <br> 密碼 |設定轉送 (上游) Proxy 的位址 (與認證) |1.設定回覆 Proxy 和認證：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.設定不需要驗證的回覆 Proxy：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.清除回覆 Proxy 設定，也就是不需要回覆 Proxy：`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |取得目前允許的主機 (僅限本機設定的允許的主機，不包括自動下載的允許的主機) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |取得目前允許的用戶端憑證主體 (僅限本機設定的允許的主體，不包括自動下載的允許的主體) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>疑難排解
您必須在已安裝閘道的電腦上安裝 OMS 代理程式。 接著，您可以使用代理程式來收集由閘道所記錄的事件。

![事件檢視器 – OMS 閘道記錄](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 閘道事件識別碼與描述**

以下表格顯示 OMS 閘道記錄事件的事件識別碼與描述。

| **識別碼** | **描述** |
| --- | --- |
| 400 |沒有特定識別碼的任何應用程式錯誤 |
| 401 |錯誤組態。 例如：listenPort = "text"，而不是整數 |
| 402 |剖析 TLS 交握訊息時發生例外狀況 |
| 403 |網路錯誤。 例如：無法連線到目標伺服器 |
| 100 |一般資訊 |
| 101 |服務已經啟動 |
| 102 |服務已經停止 |
| 103 |已從用戶端接收到 HTTP CONNECT 命令 |
| 104 |不是 HTTP CONNECT 命令 |
| 105 |目的地伺服器不在允許的清單中，或目的地連接埠不是安全的連接埠 (443) <br> <br> 請確定您閘道伺服器上的 MMA 代理程式和與閘道通訊的代理程式，是連線到相同的 Log Analytics 工作區。 |
| 105 |ERROR TcpConnection – 無效的用戶端憑證: CN=Gateway <br><br> 請確定： <br>    <br> &#149; 您是使用版本號碼為 1.0.395.0 或更新版本的閘道。 <br> &#149; 您閘道伺服器上的 MMA 代理程式和與閘道通訊的代理程式，是連線到相同的 Log Analytics 工作區。 |
| 106 |造成 TLS 工作階段變成可疑或被拒絕的任何原因 |
| 107 |已經驗證 TLS 工作階段 |

**要收集的效能計數器**

以下表格顯示可供 OMS 閘道使用的效能計數器。 您可以使用效能監視器新增計數器。

| **名稱** | **描述** |
| --- | --- |
| OMS 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| OMS 閘道/錯誤計數 |錯誤數目 |
| OMS 閘道/已連線用戶端 |已連線用戶端數目 |
| OMS 閘道/拒絕計數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![OMS 閘道效能計數器](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>取得協助
當您已經登入 Azure 入口網站時，您可以建立要求，來要求在 OMS 閘道或任何其他 Azure 服務或服務功能上提供協助。
若要要求協助，請按一下入口網站右上角的問號符號，然後按一下 [新增支援要求]。 然後，完成新的支援要求表單。

![新增支援要求](./media/log-analytics-oms-gateway/support.png)

您也可以在 [Microsoft Azure 意見反應論壇](https://feedback.azure.com/forums/267889)留下和 OMS 或 Log Analytics 有關的意見反應。

## <a name="next-steps"></a>後續步驟
* [新增資料來源](log-analytics-data-sources.md) 來從您 OMS 工作區的 [連接的來源] 收集資料，並將它儲存在 OMS 存放庫中。

