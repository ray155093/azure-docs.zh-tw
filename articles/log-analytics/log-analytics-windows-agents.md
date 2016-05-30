<properties
	pageTitle="將 Windows 電腦連接到 Log Analytics | Microsoft Azure"
	description="本文說明使用自訂版本的 Microsoft 監視代理程式 (MMA) 將內部部署基礎結構中的 Windows 電腦直接連接到 OMS 的步驟。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="banders"/>


# 將 Windows 電腦連接到 Log Analytics

本文說明使用自訂版本的 Microsoft Monitoring Agent (MMA) 將內部部署基礎結構中的 Windows 電腦直接連接到 OMS 工作區的步驟。您必須安裝並連接所有您想要上架至 OMS 之電腦的代理程式，以便讓它們將資料傳送給 OMS，以及在 OMS 入口網站中檢視及處理此資料。每個代理程式可以向多個工作區報告。

您可以使用安裝程式、命令列、或 Azure 自動化中的期望狀態設定 (DSC) 來安裝代理程式。

在有網際網路連線的電腦上，代理程式會使用網際網路連線將資料傳送給 OMS。若電腦沒有網際網路連線，您可以使用 Proxy 或 OMS Log Analytics 轉寄站。

使用 3 個簡單步驟可直接將 Windows 電腦連接至 OMS︰

1. 下載代理程式安裝檔案
2. 使用您選擇的方法安裝代理程式
3. 設定代理程式，或視需要新增額外的工作區

下圖顯示您安裝並設定代理程式之後您的 Windows 電腦和 OMS 之間的關聯性。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## 系統需求和所需的設定
在安裝或部署代理程式之前，請檢閱下列詳細資料，以確保您符合必要需求。

- 您只能將 OMS MMA 安裝在執行 Windows Server 2008 SP1 或更新版本亦或是 Windows 7 SP1 或更新版本的電腦上。
- 您將需要 OMS 訂用帳戶。如需其他資訊，請參閱 [Log Analytics 入門](log-analytics-get-started.md)。
- 每一部 Windows 電腦都必須能夠使用 HTTPS 連線到網際網路。此連線可以是直接連線、透過 Proxy 連線、或透過 OMS Log Analytics 轉寄站連線。
- OMS MMA 可以安裝在獨立電腦、伺服器和虛擬機器上。如果您想要將 Azure 託管的虛擬機器連接至 OMS，請參閱[將 Azure 儲存體連接至 Log Analytics](log-analytics-azure-storage.md)。
- 代理程式必須使用 TCP 連接埠 443 來收送各種資源。如需詳細資訊，請參閱[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)。

## 從 OMS 下載代理程式安裝檔案
1. 在 OMS 入口網站的 [概觀] 頁面中，按一下 [設定] 圖格。按一下頂端的 [**連接的來源**] 索引標籤。![連接的來源索引標籤](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. 在 [直接附加電腦] 下，按一下適用於您電腦處理器類型的 [下載 Windows 代理程式] 下載安裝檔案。
3. 按一下 [工作區識別碼] 右邊的複製圖示，然後將識別碼貼入記事本中。
4. 按一下 [主要索引鍵] 右邊的複製圖示，然後將識別碼貼入記事本中。![複製工作區識別碼和主索引鍵](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## 使用安裝程式安裝代理程式
1. 在您想要管理的電腦上執行安裝程式以安裝代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。
3. 閱讀 [授權條款] 頁面上的授權，然後按一下 [我接受]。
4. 在 [目的地資料夾] 頁面中，變更或保留預設的安裝資料夾，然後按一下 [下一步]。
5. 在 [代理程式安裝程式選項] 頁面中，您可以選擇將代理程式連接至 Azure Log Analytics (OMS)、Operations Manager，或者，如果您想要稍後再設定代理程式可以選擇保留空白。按 [下一步]。   
    - 如果您選擇連接至 Azure Log Analytics (OMS)，請將您在上一個程序複製到 [記事本] 的內容貼到 [工作區識別碼] 和 [工作區索引鍵 (主索引鍵)]，然後按 [下一步]。![貼上工作區識別碼和主索引鍵](./media/log-analytics-windows-agents/connect-workspace.png)
    - 如果您選擇連接到 Operations Manager，請輸入 [管理群組名稱]、[管理伺服器] 名稱、[管理伺服器連接埠]，然後按一下 [下一步]。在 [代理程式動作帳戶] 頁面上，選擇本機系統帳戶或本機網域帳戶，然後按一下 [下一步]。![管理群組設定](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![代理程式動作帳戶](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. 在 [準備好安裝] 頁面上，檢閱您的選擇，然後按一下 [安裝]。
7. 在 [設定成功] 頁面中，按一下 [完成]。
8. 完成時，[Microsoft 監視代理程式] 會出現在 [控制台] 中。您可以檢閱您的設定，並確認代理程式已連接到 Operational Insights (OMS)。當連接到 OMS，代理程式會顯示訊息︰**Microsoft Monitoring Agent 已成功連接到 Microsoft Operations Management Suite 服務。**

## 使用命令列安裝代理程式
- 修改，然後搭配使用下列範例與命令列來安裝代理程式。

    >[AZURE.NOTE] 如果您想要升級代理程式，您需要使用 Log Analytics 指令碼 API。請參閱下一節來升級代理程式。

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## 使用指令碼升級代理程式和加入工作區
您可以參考下列 PowerShell 範例，使用 Log Analytics 指令碼 API 來升級代理程式和加入工作區。

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[AZURE.NOTE] 如果您先前使用過命令列或指令碼來安裝或設定代理程式，`EnableAzureOperationalInsights` 會換成 `AddCloudWorkspace`。

## 使用 Azure 自動化中的 DSC 安裝代理程式

>[AZURE.NOTE] 此程序和指令碼範例不會升級現有的代理程式。

1. 從 [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 將 xPSDesiredStateConfiguration DSC 模組匯入 Azure 自動化。  
2.	建立 Azure 自動化的 *OPSINSIGHTS\_WS\_ID* 和 *OPSINSIGHTS\_WS\_KEY* 變數資產。將 *OPSINSIGHTS\_WS\_ID* 設定為您的 OMS Log Analytics 工作區識別碼，將 *OPSINSIGHTS\_WS\_KEY* 設定為您工作區的主索引鍵。
3.	使用以下指令碼並將其儲存為 MMAgent.ps1
4.	修改並使用下列範例來使用 Azure 自動化中的 DSC 安裝代理程式。使用 Azure 自動化介面或 Cmdlet 將 MMAgent.ps1 匯入 Azure 自動化。
5.	指派節點至設定。在 15 分鐘內，節點會檢查其設定，且 MMA 會被推送至節點。

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## 手動設定代理程式，或新增額外的工作區
如果您已安裝代理程式但尚未設定，或是您希望代理程式向多個工作區報告，您可以使用下列資訊來啟用或重新設定代理程式。設定代理程式之後，它會註冊代理程式服務，並將獲得所需的組態資訊及包含解決方案資訊的管理組件。

1. 安裝 Microsoft Monitoring Agent 之後，開啟 [控制台]。
2. 開啟 [Microsoft Monitoring Agent]，然後按一下 [Azure Log Analytics (OMS)] 索引標籤。   
3. 按一下 [加入] 以開啟 [加入 Log Analytics 工作區] 方塊。
4. 針對您要新增的工作區，將您在上一個程序複製到 [記事本] 的內容貼到 [工作區識別碼] 和 [工作區索引鍵 (主索引鍵)]，然後按一下 [確定]。![設定 Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

代理程式從所監視的電腦收集資料之後，受 OMS 監視的電腦數目會出現在 OMS 入口網站的 [設定] 中 [連接的來源] 索引標籤下的 [連接的伺服器]。


## 停用代理程式
1. 安裝代理程式之後，開啟 [**控制台**]。
2. 開啟 Microsoft Monitoring Agent，然後按一下 [Azure Log Analytics (OMS)] 索引標籤。
3. 選取工作區，然後按一下 [移除]。對其他所有工作區重複此步驟。


## (選擇性) 將代理程式設定為向 Operations Manager 管理群組報告

如果您在 IT 基礎結構中使用 Operations Manager，您也可以使用 MMA 代理程式做為 Operations Manager 代理程式。

### 將 MMA 代理程式設定為向 Operations Manager 管理群組報告
1.	在已安裝代理程式的電腦上，開啟 [控制台]。
2.	開啟 [Microsoft Monitoring Agent]，然後按一下 [Operations Manager] 索引標籤。![Microsoft 監視代理程式 Operations Manager 索引標籤](./media/log-analytics-windows-agents/om-mg01.png)
3.	如果 Operations Manager 伺服器已與 Active Directory 整合，請按一下 [自動更新來自 AD DS 的管理群組指派]。
4.	按一下 [加入] 以開啟 [加入管理群組] 對話方塊。![Microsoft 監視代理程式新增管理群組](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.	在[管理群組名稱] 方塊中，輸入管理群組的名稱。
6.	在 [主要管理伺服器] 方塊中，輸入主要管理伺服器的電腦名稱。
7.	在 [管理伺服器連接埠] 方塊中，輸入 TCP 連接埠號碼。
8.	在 [代理程式動作帳戶] 頁面下，選擇本機系統帳戶或本機網域帳戶。
9.	按一下 [確定] 關閉 [加入管理群組] 對話方塊，然後按一下 [確定] 關閉 [Microsoft Monitoring Agent 內容] 對話方塊。

## (選擇性) 將代理程式設定為使用 OMS Log Analytics 轉寄站

如果您有未連線到網際網路的伺服器或用戶端，您仍然可以使用 OMS Log Analytics 轉寄站讓它們將資料傳送至 OMS。當您使用轉寄站時，代理程式的所有資料會都透過可存取網際網路的單一伺服器傳送。轉寄站會將資料直接從代理程式傳輸到 OMS，不會對傳輸的任何資料做分析。

若要深入了解轉寄站，包括安裝與設定，請參閱 [OMS Log Analytics 轉寄站](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder)。

如需如何設定代理程式使用 Proxy 伺服器 (此案例中是 OMS 轉寄站)，請參閱[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)。

## (選擇性) 設定 Proxy 和防火牆設定
如果您環境中的 Proxy 伺服器或防火牆有網際網路存取上的限制，請參閱[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)，讓代理程式可與 OMS 服務通訊。

## 後續步驟

- [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)，以加入功能和收集資料。
- 如果您的組織使用 Proxy 伺服器或防火牆，請[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)，讓代理程式可與 Log Analytics 服務通訊。

<!---HONumber=AcomDC_0518_2016-->