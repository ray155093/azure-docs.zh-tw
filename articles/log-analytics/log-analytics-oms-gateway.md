---
title: "使用 OMS 閘道將離線電腦連線到 Operations Management Suite | Microsoft Docs"
description: "使用 OMS 閘道來連線透過 Operations Management Suite 所管理且透過 System Center Operations Manager 所監視的離線電腦，以將資料傳送至 Operations Management Suite 服務。"
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
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>使用 OMS 閘道將離線電腦連線到 Operations Management Suite

透過 Operations Management Suite (OMS) 所管理且透過 System Center Operations Manager (Operations Manager) 所監視的電腦，可以在沒有網際網路存取權時將資料傳送至 Operations Management Suite 服務。 OMS 閘道是一個 HTTP 正向 Proxy，使用 HTTP CONNECT 命令支援 HTTP 通道。 OMS 閘道可以收集資料，並代表離線電腦將它傳送至 Operations Management Suite 服務。  

您可以搭配使用 OMS 閘道︰

* Azure 自動化 Hybrid Runbook Worker。  
* 具有 Microsoft Monitoring Agent 且直接連線到 Operations Management Suite 工作區的 Windows 電腦。
* System Center Operations Manager 2012 SP1 (含更新彙總套件 (UR) 7)、System Center Operations Manager 2012 R2 (含 UR3) 或與 Operations Management Suite 整合的 System Center Operations Manager 2016 管理群組。  

某些 IT 安全性原則不允許將連線網路的電腦連線到網際網路。 這可能適用於銷售點 (POS) 裝置，或適用於支援 IT 服務的伺服器。 但是，您需要將電腦連線到 Operations Management Suite，才能管理和監視它們。 您可以設定離線電腦直接與 OMS 閘道進行通訊。 閘道可代表離線電腦來接收組態以及轉遞資料。  

如果您設定讓 Operations Management Suite 代理程式直接連線到 Operations Management Suite 工作區的電腦，則所有電腦都會改為使用 OMS 閘道進行通訊。 閘道會將資料從代理程式直接傳輸至 Operations Management Suite。 閘道不會分析任何傳輸中的資料。

當您整合 Operations Manager 管理群組與 Operations Management Suite 時，可以設定管理伺服器連線到 OMS 閘道。 伺服器會接收組態資訊，然後根據您設定的解決方案來傳送收集到的資料。 Operations Manager 代理程式會將一些資料 (例如 Operations Manager 警示、組態評估、執行個體空間，以及容量資料) 傳送到管理伺服器。 其他大量資料 (例如 Internet Information Services (IIS) 記錄、效能和安全性事件) 會直接傳送到 OMS 閘道。 

如果 Operations Manager 閘道伺服器部署在周邊網路 (也稱為 DMZ、「隔離區域」和「遮蔽式子網路」) 或其他隔離網路以監視未受信任的系統，則無法與 OMS 閘道通訊。 Operations Manager 閘道伺服器只能向管理伺服器報告。 當您將 Operations Manager 管理群組設定為與 OMS 閘道通訊時，Proxy 組態資訊會自動發佈到每個受代理程式管理並已設定為收集資料以供 Azure Log Analytics 使用的電腦，即使設定是空的也會如此。 會自動發佈 Proxy 組態資訊，即使設定是空的也是如此。    

若要為透過閘道與 Operations Management Suite 通訊的直接連線代理程式或 Operations Management 群組提供高可用性，您可以使用網路負載平衡 (NLB)。 NLB 會將流量重新導向並分散到多個閘道伺服器。 如果某個閘道伺服器故障，系統就會將流量重新導向到其他可用節點。  

建議您在執行 OMS 閘道軟體的電腦上安裝 Operations Management Suite 代理程式。 因此，您可以監視 OMS 閘道，並分析效能或事件資料。 代理程式也可以協助 OMS 閘道識別其需要通訊的服務端點。

每個代理程式必須具備對其閘道的網路連線，代理程式才能自動將資料傳輸到閘道，或從閘道傳輸資料。 建議您不要在網域控制站上安裝閘道。

下圖顯示透過閘道伺服器從直接代理程式流向 Operations Management Suite 的資料流程。 代理程式的 Proxy 組態必須符合 OMS 閘道上設定為與 Operations Management Suite 通訊的連接埠。  

![代理程式直接與 Operations Management Suite 通訊的圖表](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

下圖顯示從 Operations Manager 管理群組流向 Operations Management Suite 的資料流程。   

![Operations Manager 與 Operations Management Suite 通訊的圖表](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>必要條件

您指定要執行 OMS 閘道的電腦必須符合下列需求︰

* Windows 10, Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 或 Windows Server 2008 作業系統
* Microsoft .NET Framework 4.5
* 最小 4 核心處理器
* 最小 8 GB 的記憶體 

### <a name="language-availability"></a>提供的語言

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

## <a name="download-oms-gateway"></a>下載 OMS 閘道

您有三個選項可以取得最新版的 OMS 閘道：

* 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=54443)進行下載。

* 從 Operations Management Suite 入口網站中下載它。 在您登入 Operations Management Suite 工作區之後：

    1. 移至 [設定] > [連線來源] > [Windows 伺服器]。 
    2. 選取 [下載 OMS 閘道]。

* 從 [Azure 入口網站](https://portal.azure.com)進行下載。 在登入後︰  

   1. 在服務清單中，選取 [Log Analytics]。  
   2. 選取工作區。
   3. 在工作區刀鋒視窗上，選取 [一般] 下方的 [快速入門]。
   4. 在 [選擇要連線至工作區的資料來源] 下方，選取 [電腦]。
   5. 在 [直接代理程式] 刀鋒視窗上，選取 [下載 OMS 閘道]。<br><br> ![下載 OMS 閘道](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>安裝 OMS 閘道

若要安裝閘道，請完成下列步驟。 

> [!NOTE]
> 如果您已安裝舊版的閘道 (之前稱為 Log Analytics 轉寄站)，則會將舊版本升級為目前 OMS 閘道版本。
>

1. 在目的地資料夾中，按兩下 **OMS Gateway.msi**。
2. 在 [歡迎] 頁面上，選取 [下一步]。<br><br> ![閘道設定精靈](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. 在 [授權合約] 頁面上，選取 [我接受授權合約中的條款] 以同意 Microsoft 軟體授權條款。 
4. 在 [連接埠和 Proxy 位址] 頁面上：

   1. 輸入要用於閘道的 TCP 通訊埠號碼。 安裝程式會使用此連接埠號碼在 Windows 防火牆上設定輸入規則。 預設值為 8080。
      連接埠號碼的有效範圍是 1 到 65535。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。
   2. 如果閘道安裝所在的伺服器需要透過 Proxy 進行通訊，您也可以選擇輸入閘道需要連線的 Proxy 位址。 例如，**http://myorgname.corp.contoso.com:80**。 如果 Proxy 位址方塊是空的，閘道會嘗試直接連線到網際網路。 如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼。<br><br> ![閘道精靈 Proxy 組態](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. 如果未在電腦上啟用 Microsoft Update，則會出現 [Microsoft Update] 頁面。 您可以選擇在此頁面上啟用它。 否則，請繼續下一個步驟。
6. 在 [目的地資料夾] 頁面上，您可以保留預設資料夾 C:\Program Files\OMS Gateway，或輸入您想要安裝閘道的位置。
7. 在 [準備安裝] 頁面上，選取 [安裝]。 可能會出現要求安裝權限的對話方塊。 選取 [是]。
8. 選取 [完成]。 若要確認服務正在執行，請開啟 Services.msc 嵌入式管理單元，並檢查服務清單中顯示 [OMS 閘道]。 其狀態應該是 [執行中]。<br><br> ![檢查 OMS 閘道服務](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>設定網路負載平衡 
您可以使用 NLB 來設定 OMS 閘道，以獲得高可用性。 您可以使用 Windows Server 網路負載平衡功能或以硬體為基礎的負載平衡器。 為了管理流量，負載平衡器可跨其節點將來自 Operations Management Suite 代理程式或 Operations Manager 管理伺服器的要求連線進行重新導向。 如果某個閘道伺服器故障，系統就會將流量重新導向到其他節點。

若要了解如何設計和部署 Windows Server 2016 網路負載平衡叢集，請參閱[網路負載平衡](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)。  

若要設定 Windows Server 網路負載平衡叢集，請完成下列步驟：  

1. 使用系統管理員帳戶，登入屬於網路負載平衡叢集成員的 Windows 伺服器。  
2. 在伺服器管理員中，開啟網路負載平衡管理員。
3. 選取 [工具]，然後選取 [網路負載平衡管理員]。
4. 若要將 OMS 閘道伺服器連線到已安裝的 Microsoft Monitoring Agent，請以滑鼠右鍵按一下叢集的 IP 位址，然後選取 [新增主機到叢集]。<br><br> ![網路負載平衡管理員：新增主機到叢集](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. 輸入您要連線之閘道伺服器的 IP 位址。<br><br> ![網路負載平衡管理員：新增主機到叢集、連線](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>設定 Operations Management Suite 代理程式和 Operations Manager 管理群組
在本節中，我們將說明如何設定直接連線的 Operations Management Suite 代理程式和 Operations Manager 管理群組。 您也可以設定 Azure 自動化 Hybrid Runbook Worker，以使用 OMS 閘道來與 Operations Management Suite 通訊。  

若要了解在直接連線到 Operations Management Suite 之 Windows 電腦上安裝 Operations Management Suite 代理程式的需求和步驟，請參閱 [將 Windows 電腦連線到 Operations Management Suite](log-analytics-windows-agents.md)。 針對 Linux 電腦，請參閱[將 Linux 電腦連線到 Operations Management Suite](log-analytics-linux-agents.md)。 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>設定 Operations Management Suite 代理程式和 Operations Manager 以將 OMS 閘道作為 Proxy 伺服器

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>設定獨立 Operations Management Suite 代理程式
如需設定代理程式使用 Proxy 伺服器的資訊，請參閱[使用 Microsoft Monitoring Agent 設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)。 在此情況下，Proxy 伺服器就是閘道。 如果您已在網路負載平衡器後方部署多個閘道伺服器，Operations Management Suite 代理程式 Proxy 組態會是網路負載平衡器的虛擬 IP 位址︰<br><br> ![Microsoft Monitoring Agent 內容：Proxy 設定](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>設定 Operations Manager (所有代理程式都會使用相同的 Proxy 伺服器)
設定 Operations Manager 來新增閘道伺服器。 即使設定是空的，Operations Manager Proxy 組態仍會自動套用到所有向 Operations Manager 報告的代理程式。

若要搭配使用 OMS 閘道與 Operations Manager，您必須符合下列需求︰

* 閘道伺服器上必須安裝 Microsoft Monitoring Agent (代理程式版本 **8.0.10900.0** 和更新版本)。 必須針對您想要與之通訊的 Operations Management Suite 代理程式工作區進行設定。
* 閘道必須有網際網路連線，或連線到有網際網路連線的 Proxy 伺服器。

> [!NOTE]
> 如果未指定閘道的值，系統會將空白值推送到所有代理程式。

將 Operations Manager 伺服器連線到 Operations Management Suite 代理程式工作區：

1. 在 Operations Manager 主控台中，移至 [Operations Management Suite] > [連線] > [設定 Proxy 伺服器]。<br><br> ![Operations Manager：設定 Proxy 伺服器](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. 選取 [使用 Proxy 伺服器來存取 Operations Management Suite]。 輸入 OMS 閘道伺服器的 IP 位址，或網路負載平衡器的虛擬 IP 位址。 確定開頭使用 **http://** 前置詞。<br><br> ![Operations Manager：Proxy 伺服器位址](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. 選取 [完成]。 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>設定 Operations Manager (特定代理程式使用 Proxy 伺服器)
對於大型或複雜的環境，您可能只想要讓特定伺服器 (或群組) 使用 OMS 閘道伺服器。 對於這些伺服器，您無法直接更新 Operations Manager 代理程式。 管理群組的全域值會覆寫此值。 相反地，請覆寫用來推送這些值的規則。

> [!NOTE] 
> 您可以使用相同的設定技巧，在環境中執行多個 OMS 閘道伺服器。 例如，您需要以每個區域為基礎來指定特定的 OMS 閘道伺服器。

1. 在 System Center Operations Manager 主控台中，選取 [撰寫] 工作區。  
2. 選取 [規則]，並在 System Center Operations Manager 工具列上，選取 [範圍] 按鈕。 如果此按鈕無法使用，請進行檢查以確定您在 [監視] 窗格中選取的是物件，而非資料夾。 [範圍管理組件物件] 對話方塊會顯示一般會作為目標之類別、群組或物件的清單。 
3. 在 [尋找] 方塊中，輸入[健全狀況服務]。 從清單中選取它。 選取 [確定] 。  
4. 在 Operations Manager 主控台工具列上，搜尋 [Advisor Proxy 設定規則] 規則。 選取 [覆寫]，然後指向 [覆寫特定類別物件的規則: 健全狀況服務]。 從清單中選取特定的物件。 您可以選擇建立自訂群組，而其中包含您想要套用此覆寫之伺服器的健康狀態服務物件。 然後對該群組套用覆寫。
5. 在 [覆寫屬性] 對話方塊中，選取 **WebProxyAddress** 參數旁的 [覆寫] 資料行。 在 [覆寫值] 方塊中，輸入 OMS 閘道伺服器的 URL。 確定開頭使用 **http://** 前置詞。

   >[!NOTE]
   > 您不需要啟用此規則。 此規則是自動使用 System Center Advisor Secure Reference Override 管理組件中的覆寫進行管理。 管理組件的目標是「System Center Advisor 監控伺服器群組」。
   > 

6. 若要指定管理組件，請執行下列其中一項︰
    * 在 [選取目的地管理組件] 清單中，選取管理組件。
    * 若要建立全新且未密封的管理組件，請選取 [新增]。 
7. 選取 [確定] 。 

### <a name="set-up-automation-hybrid-workers"></a>設定自動化混合式背景工作角色
如果您的環境中有 Azure 自動化 Hybrid Runbook Worker，則可以建立手動暫時性解決方式，讓閘道進行支援。

在下列步驟中，您需要知道自動化帳戶所在的 Azure 區域。 找出位置：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 自動化服務。
3. 選取相關的 Azure 自動化帳戶。
4. 在 [位置] 下方，查看區域。<br><br> ![Azure 入口網站：自動化帳戶位置](./media/log-analytics-oms-gateway/location.png)  

使用下列表格來識別每個位置的 URL：

**作業執行階段資料服務 URL**

| 位置 | URL |
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

| 位置 | URL |
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

如果您的電腦是使用更新管理解決方案自動註冊為 Hybrid Runbook Worker 以進行修補，請遵循下列步驟：

1. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的 [允許的主機] 清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 使用下列 Azure PowerShell Cmdlet 重新啟動 OMS 閘道服務：`Restart-Service OMSGatewayService`

如果您的電腦已使用 Hybrid Runbook Worker 註冊 Cmdlet 加入 Azure 自動化，請完成下列步驟：

1. 將代理程式服務註冊 URL 新增到 OMS 閘道上的 [允許的主機] 清單。 例如：`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的 [允許的主機] 清單。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. 重新啟動 OMS 閘道服務： `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet
Cmdlet 可協助您更新 OMS 閘道設定。 使用 PowerShell Cmdlet 之前，請確定：

1. 安裝 OMS 閘道 (MSI)。
2. 開啟 PowerShell 主控台視窗。
3. 若要匯入模組，請輸入此命令：`Import-Module OMSGateway`。 如果未發生錯誤，表示模組已經成功匯入，而您可以使用 Cmdlet。 
4. 輸入 `Get-Module OMSGateway`。
5. 在您使用 Cmdlet 進行變更之後，請重新啟動 OMS 閘道服務。

如果您在步驟 3 發生錯誤，表示模組並未匯入。 如果 PowerShell 找不到模組，則可能會發生此錯誤。 此模組會列在閘道的安裝路徑 C:\Program Files\Microsoft OMS Gateway\PowerShell 中。

| Cmdlet | 參數 | 說明 | 範例 |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |索引鍵 |取得服務組態 |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |位址 <br> 使用者名稱 <br> 密碼 |設定轉送 (上游) Proxy 的位址 (與認證) |1.設定回覆 Proxy 和認證：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.設定不需要驗證的回覆 Proxy：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.清除回覆 Proxy 設定，也就是您不需要回覆 Proxy：`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |取得目前允許的主機 (僅限本機設定的允許的主機，不包括自動下載的允許的主機) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |取得目前允許的用戶端憑證主體 (僅限本機設定的允許的主體，不包括自動下載的允許的主體) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>疑難排解
若要收集閘道所記錄的事件，您需要安裝 Operations Management Suite 代理程式。<br><br> ![事件檢視器：OMS 閘道記錄](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 閘道事件識別碼與描述**

下表列出 OMS 閘道記錄事件的事件識別碼與描述：

| ID | 說明 |
| --- | --- |
| 400 |沒有特定識別碼的任何應用程式錯誤 |
| 401 |錯誤組態。 例如：listenPort = "\<text\>"，而不是整數 |
| 402 |剖析傳輸層安全性 (TLS) 交握訊息時發生例外狀況 |
| 403 |網路錯誤。 例如：無法連線到目標伺服器 |
| 100 |一般資訊 |
| 101 |服務已經啟動 |
| 102 |服務已經停止 |
| 103 |已從用戶端接收到 HTTP CONNECT 命令 |
| 104 |不是 HTTP CONNECT 命令 |
| 105 |目的地伺服器不在允許的清單中，或目的地連接埠不是安全的連接埠 (443) <br> <br> 請確定您閘道伺服器上的 Microsoft Monitoring Agent 代理程式以及與閘道通訊的代理程式連線到相同的 Log Analytics 工作區。 |
| 105 |ERROR TcpConnection – 無效的用戶端憑證: CN=Gateway <br><br> 請確定： <br>    <br> &#149; 您使用的是 OSM 閘道版本 1.0.395.0 或更新版本。 <br> &#149; 您閘道伺服器上的 Microsoft Monitoring Agent 代理程式以及與閘道通訊的代理程式會連線到相同的 Log Analytics 工作區。 |
| 106 |造成 TLS 工作階段變成可疑或被拒絕的任何原因 |
| 107 |已經驗證 TLS 工作階段 |

**要收集的效能計數器**

下表列出可供 OMS 閘道使用的效能計數器。 您可以使用 Windows 效能監視器新增計數器。

| 名稱 | 說明 |
| --- | --- |
| OMS 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| OMS 閘道/錯誤計數 |錯誤數目 |
| OMS 閘道/已連線用戶端 |已連線用戶端數目 |
| OMS 閘道/拒絕計數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![OMS 閘道效能計數器](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>取得協助
在 Azure 入口網站中，當您登入時，可以建立要求，來要求在 OMS 閘道或任何其他 Azure 服務或服務功能上提供協助。
若要要求協助，請選取入口網站右上角的問號符號，然後選取 [新增支援要求]。 完成新的支援要求表單。

![新增支援要求](./media/log-analytics-oms-gateway/support.png)

您也可以在 [Microsoft Azure 意見反應論壇](https://feedback.azure.com/forums/267889)留下與 Operations Management Suite 或 Log Analytics 有關的意見反應。

## <a name="next-steps"></a>後續步驟
若要從 Operations Management Suite 工作區的連線來源來收集資料，您可以[新增資料來源](log-analytics-data-sources.md)。 您可以將日期儲存到 Operations Management Suite 存放庫中。

