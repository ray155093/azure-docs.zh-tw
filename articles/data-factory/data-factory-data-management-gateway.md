<properties 
	pageTitle="Data Factory 的資料管理閘道 | Microsoft Azure"
	description="設定資料閘道器以在內部部署與雲端之間移動資料。使用 Azure Data Factory 中的資料管理閘道器移動資料。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2016" 
	ms.author="spelluru"/>

# 資料管理閘道
資料管理閘道是您必須安裝在內部部署環境中的用戶端代理程式，以便在[複製活動所支援](data-factory-data-movement-activities.md##supported-data-stores)的雲端與內部部署資料存放區之間移動資料。

本文補充[在內部部署與雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文的內容，該文提供建立 Data Factory 管線的逐步解說，以便使用閘道將資料從內部部署 SQL Server 資料庫移至 Azure blob。這篇文章提供有關資料管理閘道的詳細深入資訊。

## Overview

### 資料管理閘道功能
資料管理閘道提供下列功能：

- 在相同 Data Factory 內建立內部部署資料來源和雲端資料來源的模型及移動資料。
- 具有用於監視和管理的單一窗格，可從 Data Factory 刀鋒視窗看見閘道狀態。
- 安全地管理內部部署資料來源的存取權。
	- 不需要變更公司防火牆。閘道器只會使輸出 HTTP 連線開啟網際網路。
	- 利用您的憑證加密內部部署資料存放區的認證。
- 有效率地移動資料 – 資料會以平行方式傳輸，且系統會採用自動重試邏輯，修復間歇性網路問題。

### 命令流程和資料流程
當您使用資料管線中的複製活動，將內部部署資料擷取至雲端以進行進一步的處理，或者匯出雲端中的結果資料回到內部部署資料存放區，複製活動都會在內部使用閘道器將資料從內部部署資料來源傳輸回雲端，反之亦然。

利用資料閘道器進行複製步驟的高層級資料流和摘要如下：![使用閘道器的資料流](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	資料開發人員會使用 [Azure 入口網站](https://portal.azure.com)或 [PowerShell Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx)，為 Azure Data Factory 建立新的閘道器。
2.	資料開發人員會藉由指定閘道，建立內部部署資料存放區的連結服務。在設定連結服務資料的過程中，開發人員會使用 [設定認證] 應用程式來指定驗證類型和認證。[設定認證] 應用程式對話方塊將會與資料存放區進行通訊，以測試要儲存認證的連線與閘道。
3. 在雲端中儲存認證之前，閘道會利用與閘道 (由資料開發人員提供) 相關聯的憑證加密認證。
4. Data Factory 服務會和閘道進行通訊，以透過使用共用 Azure 服務匯流排佇列的控制通道，進行工作的排程和管理。必須開始複製活動作業時，Data Factory 會將要求和認證資訊一起排入佇列。輪詢佇列之後，閘道器隨即啟動。
5.	閘道會利用相同的憑證解密認證，然後利用適當的驗證類型和認證連接到內部部署資料存放區。
6.	閘道器會根據複製活動在資料管線中的設定方式，將資料從內部部署存放區複製到雲端儲存體，或從雲端儲存體複製到內部部署資料存放區。請注意：在這個步驟中，閘道器會透過安全 (HTTPS) 通道直接與以雲端為基礎的儲存體服務 (例如 Azure Blob、Azure SQL 等) 通訊。

### 使用閘道的考量
- 「資料管理閘道」的單一執行個體可用於多個內部部署資料來源，但是請注意，**單一閘道器執行個體只會繫結至一個 Azure 資料處理站**，不能與另一個資料處理站共用。
- 單一電腦上**只能安裝一個資料管理閘道的執行個體**。假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道器個別安裝在兩部內部部署電腦上。
- 雖然閘道器不需要和資料來源位在相同的電腦上，但越接近資料來源可縮短閘道器連線到資料來源的時間。建議您安裝閘道器的電腦不同於裝載內部部署資料來源的電腦，如此閘道器才不會與資料來源爭奪資源。
- 您可以有「多個閘道器在不同電腦上，但連接至相同的內部部署資料來源」。例如，您可能有兩個閘道器用於服務兩個 Data Factory，但相同的內部部署資料來源都向這兩個 Data Factory 註冊。
- 若您已在電腦上安裝用於 **Power BI** 案例的閘道器，請於另一台電腦上安裝另一個用於 Azure Data Factory 的閘道器。
- 您必須**使用閘道器，即使您使用 ExpressRoute 也一樣**。
- 您應該將資料來源視為內部部署資料來源 (亦即在防火牆後面)，即使您使用 **ExpressRoute** 和**使用閘道**來建立服務與資料來源之間的連接也一樣。
- 您必須**使用閘道**，即使資料存放區位於 **Azure IaaS VM** 上的雲端中。

## 安裝

### 必要條件
- 支援的**作業系統**版本包括 Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。目前不支援在網域控制站上安裝資料管理閘道。
- 建議閘道器電腦的「組態」至少為 2 GHz、4 核心、8 GB RAM 和 80 GB 磁碟。
- 如果主機電腦休眠，閘道器即無法回應資料要求。因此，安裝閘道器之前，請先在電腦上設定適當的「電源計劃」。如果電腦已設定為休眠，安裝閘道器時會提示訊息。
- 您必須是電腦上的系統管理員，才能成功安裝和設定「資料管理閘道」。您可以將其他使用者加入至 [資料管理閘道使用者] 本機 Windows 群組。此群組的成員可以使用「資料管理閘道組態管理員」工具來設定閘道器。

因為複製活動執行會以特定的頻率發生，在電腦上的資源使用量 (CPU、記憶體) 也會遵循與尖峰和閒置時間相同的模式。資源使用率也仰賴要移動的資料量。當多個複製工作正在進行中時，您將觀察到資源使用量會在尖峰時段增加。雖然上述情況是組態下限，根據資料移動的特定負載，您的組態最好還是要具備比上述組態下限還要多的資源。

### 安裝選項
可以用下列方式安裝資料管理閘道：

- 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載 MSI 安裝套件。MSI 也可用來將現有的資料管理閘道升級至最新的版本，並保留所有設定。
- 按一下 [手動設定] 底下的 [下載並安裝資料閘道] 連結，或 [快速安裝] 之下的 [直接安裝在此電腦上]。如需使用快速安裝的逐步指示，請參閱[在內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文。手動步驟會帶您前往下載中心，而下一節會提供從下載中心下載並安裝閘道的指示。

### 安裝最佳作法：
1.	為閘道器設定主機電腦上的電源計劃，使電腦不休眠。如果主機電腦休眠，閘道器即無法回應資料要求。
2.	您應該備份與閘道器相關聯的憑證。

### 從下載中心安裝閘道
1. 瀏覽至 [Microsoft 資料管理閘道下載頁面](https://www.microsoft.com/download/details.aspx?id=39717)。
2. 按一下 [下載]，選取適當的版本 (**32 位元**與**64 位元**)，然後按 [下一步]。
3. 直接執行 **MSI** 或將其儲存至您的硬碟並執行。
4. 在 [歡迎] 頁面上，選取 [語言]，按 [下一步]。
5. **接受**使用者授權合約，然後按 [下一步]。
6. 選取要安裝閘道的**資料夾**，然後按 [下一步]。
7. 在 [準備安裝] 頁面上，按一下 [安裝]。
8. 按一下 [完成] 來完成安裝。
9. 從 Azure 入口網站取得金鑰。如需逐步指示，請參閱下一節。
10. 在您的電腦上執行的 [資料管理閘道組態管理員] 的 [註冊閘道] 頁面上，將金鑰貼到文字中，(選擇性) 按一下 [顯示閘道金鑰] 核取方塊以查看金鑰文字，然後按一下 [註冊]。

### 使用金鑰註冊閘道

#### 如果您尚未在入口網站中建立邏輯閘道
遵循[在內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文中的逐步解說步驟，以在入口網站中建立新的閘道並從 [設定] 刀鋒視窗取得金鑰。

#### 如果您已經在入口網站中建立邏輯閘道
1. 在 Azure 入口網站中，瀏覽至 [Data Factory] 刀鋒視窗，然後按一下 [連結服務] 圖格。

	![Data Factory 刀鋒視窗](media/data-factory-data-management-gateway/data-factory-blade.png)
2. 在 [連結服務] 刀鋒視窗中，選取您在入口網站中建立的邏輯**閘道**。

	![邏輯閘道](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. 在 [資料閘道] 刀鋒視窗中，按一下 [下載並安裝資料閘道]。

	![入口網站中的下載連結](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. 在 [設定] 刀鋒視窗中，按一下 [重新建立金鑰]。在仔細閱讀警告訊息後，請按一下 [是]。

	![重新建立索引鍵](media/data-factory-data-management-gateway/recreate-key-button.png)
4. 按一下金鑰文字旁的 [複製] 按鈕，將金鑰複製到剪貼簿。
	
	![複製金鑰](media/data-factory-data-management-gateway/copy-gateway-key.png)

### 系統匣圖示/通知
下圖顯示您會看到的某些系統匣圖示。

![系統匣圖示](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

如果您將游標放在系統匣圖示/通知訊息上，您會在快顯視窗中看到閘道/更新作業的狀態詳細資料。

### 連接埠和防火牆
有兩個您需要考量的防火牆：在組織的中央路由器上執行的**公司防火牆**，以及在已安裝閘道的本機電腦上設定為精靈的 **Windows 防火牆**。

![防火牆](./media/data-factory-data-management-gateway/firewalls.png)

在公司防火牆層級，您需要設定下列網域和輸出連接埠：

| 網域名稱 | 連接埠 | 說明 |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443、80 | 透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) | 
| *.servicebus.windows.net | 9350-9354, 5671 | 透過 TCP 的選擇性服務匯流排轉送 | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

Windows 防火牆層級通常會啟用這些輸出連接埠。如果沒有，您可以在閘道電腦上相應地設定網域和連接埠。

#### 將資料從來源資料存放區複製到接收資料存放區

您必須確定公司防火牆、閘道機器上的 Windows 防火牆和資料存放區本身都已適當啟用防火牆規則。這可讓閘道成功連接到來源和接收器。您必須為複製作業中的每個相關資料存放區啟用規則。

例如，若要**從內部部署資料存放區複製到 Azure SQL Database 接收器或「Azure SQL 資料倉儲」接收器**，您必須在 Windows 防火牆與公司防火牆的連接埠 **1433** 上都允許輸出 **TCP** 通訊，且必須設定 Azure SQL Server 的防火牆設定，將閘道機器的 IP 位址新增至允許的 IP 位址清單。


### Proxy 伺服器設定
根據預設，資料管理閘道會採用 Internet Explorer 中的 Proxy 設定，並使用預設認證來存取它。如果這不符合您的需求，您可以依照下列方式進一步設定 **Proxy 伺服器設定**，以確保閘道能夠連接到 Azure Data Factory：

1.	安裝資料管理閘道後，請在 [檔案總管] 中安全地複製 “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”，以備份原始檔案。
2.	以系統管理員身分啟動 Notepad.exe，並開啟文字檔 “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”。您會看見 system.net 的預設標記，如下所示：

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	然後，您可以在該父標記內加入 Proxy 伺服器詳細資料，例如 Proxy 位址：

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	在 Proxy 標記內可以有其他屬性，用以指定必要的設定，例如 scriptLocation。請參閱 [Proxy 項目 (網路設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 以了解語法。

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. 將組態檔儲存到原始位置中，然後重新啟動資料管理閘道服務，以取得變更。您可以從 [開始] > [Services.msc]，或是從 [資料管理閘道組態管理員] > 按一下 [停止服務] 按鈕，然後按一下 [啟動服務]，來執行這項操作。如果服務未啟動，可能因為在已編輯的應用程式組態檔中加入了不正確的 XML 標記語法。

除了以上幾點以外，您也必須確定 Microsoft Azure 包含在公司的白名單中。如需有效的 Microsoft Azure IP 位址清單，可從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=41653)下載。

#### 防火牆和 Proxy 伺服器相關問題的可能徵兆：
如果發生如下的錯誤，有可能是原因防火牆或 Proxy 伺服器的組態不正確，而使資料管理閘道無法連接到 Azure Data Factory 驗證其本身。請參閱上一節，以確保您的防火牆和 Proxy 伺服器皆正確設定。

1.	當您嘗試註冊閘道器時，您會收到下列錯誤：「無法註冊閘道器金鑰。再次嘗試註冊閘道器金鑰之前，請確認資料管理閘道已處於連線狀態，且已啟動資料管理閘道主機服務。」
2.	當您開啟組態管理員時，您會看到「已中斷連接」或「連接中」狀態。檢視 Windows 事件記錄檔時，在 [事件檢視器] > [應用程式和服務記錄檔] > [資料管理閘道] 中，您會看到錯誤訊息，例如「無法連接到遠端伺服器」或「資料管理閘道的元件沒有回應，將會自動重新啟動。元件名稱：閘道。」

### 開啟用於認證加密的連接埠 8050 
當您在「Azure 入口網站」中設定內部部署連結服務時，**設定認證**應用程式會使用輸入連接埠 **8050** 將認證轉送至閘道 (本文稍後將有詳細說明)。閘道設定期間，資料管理閘道安裝預設會在閘道電腦上開啟此連接埠。
 
如果您使用協力廠商的防火牆，則可以手動開啟連接埠 8050。如果您在閘道設定期間遇到防火牆問題，您可以嘗試使用下列命令來安裝閘道，而不需要設定防火牆。

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

如果您選擇不要在閘道機器上開啟連接埠 8050，則若要設定內部部署連結服務，您將必須使用**設定認證**應用程式以外的機制來設定資料存放區認證。例如，您可以使用 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell Cmdlet。如需了解如何設定資料存放區認證，請參閱[設定認證和安全性](#set-credentials-and-securityy)一節。

## 更新 
根據預設，資料管理閘道會在有更新版本的閘道時自動進行更新。在所有排定的工作完成前，閘道不會進行更新。更新作業完成後，閘道才會處理後續的工作。如果更新失敗，閘道會回復為舊版本。

您會在入口網站的閘道屬性刀鋒視窗、「資料管理閘道組態管理員」首頁，以及系統匣的通知訊息中看到排定的更新時間。

「資料管理閘道組態管理員」的 [首頁] 索引標籤會顯示更新排程，以及上次安裝/更新閘道的時間。

![排程更新](media/data-factory-data-management-gateway/UpdateSection.png)

您可以選擇立即安裝更新，或等候閘道於排定時間自動更新。例如，下列螢幕擷取畫面顯示的是「資料管理閘道組態管理員」中所顯示的通知訊息以及 [更新] 按鈕，按一下此按鈕即可立即安裝更新。

![DMG 組態管理員中的更新](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

系統匣中的通知訊息看起來就像下面這樣：

![系統匣訊息](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

您會在系統匣中看到更新作業 (手動或自動) 的狀態。當您下次開啟「資料管理閘道組態管理員」時，您會在通知列上看到指出閘道已更新的訊息，以及連往[新功能主題](data-factory-gateway-release-notes.md)的連結。

### 停用/啟用自動更新功能
您可以執行下列動作來停用/啟用自動更新功能：

1. 在閘道電腦上啟動 Windows PowerShell。
2. 切換至 C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript 資料夾。
3. 執行下列命令，將自動更新功能關閉 (停用)。

		.\GatewayAutoUpdateToggle.ps1  -off

4. 若要將它重新開啟：
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## 組態管理員 
安裝閘道後，您可以用下列方式啟動 [資料管理閘道組態管理員]：

- 在 [搜尋] 視窗中，輸入**資料管理閘道器**以存取這個公用程式。
- 在資料夾 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** 中執行 **ConfigManager.exe**
 
### 首頁
首頁可讓您執行下列作業︰

- 檢視閘道的狀態；不論閘道是否連接至雲端服務。
- 在入口網站中使用金鑰進行**註冊**。
- 在閘道電腦上**停止**後啟動 [資料管理閘道主機服務]。
- 在一天的特定時間**更新排程**。
- 檢視閘道**上次更新**時的日期。

### 設定頁面
[設定] 頁面可讓您執行下列作業︰

- 檢視、變更及匯出閘道所使用的**憑證**。此憑證用來加密資料來源認證。
- 變更端點的 **HTTPS 連接埠**。閘道會開啟一個連接埠，以便設定資料來源認證。
- 端點的**狀態**
- 檢視 [SSL 憑證] 用於入口網站與閘道之間的 SSL 通訊，以設定資料來源的認證。

### 診斷頁面
[診斷] 頁面可讓您執行下列作業︰

- 啟用詳細資訊**記錄**、在事件檢視器中檢視記錄檔，以及在失敗時將記錄檔傳送給 Microsoft。
- **測試連線** (對資料來源的連線)。

### Help page
[說明] 頁面會顯示以下資訊：

- 閘道的簡短說明。
- 版本號碼
- 線上說明、隱私權聲明及授權合約的連結。

## 疑難排解

- 您可以在 Windows 事件記錄檔的閘道器記錄檔中找到詳細資訊。您可以使用 **應用程式及服務記錄檔** > **資料管理閘道**下的 Windows **事件檢視器** 找到它們，而疑難排解閘道器的相關問題會在事件檢視器中尋找錯誤層級事件。
- 如果閘道在您**變更憑證**之後停止運作，請使用「Microsoft 資料管理閘道組態管理員」工具或「服務」控制台小程式來重新啟動 (停止後再啟動) **資料管理閘道服務**。如果您仍然看到錯誤，您可能必須提供資料管理閘道器服務使用者的明確權限，以存取憑證管理員 (certmgr.msc) 中的憑證。該服務的預設使用者帳戶為：**NT Service\\DIAHostService**。
- 如果**認證管理員**應用程式無法在您按一下 Data Factory 編輯器中的 [加密] 按鈕時**加密**認證，請確認您是在已安裝閘道的機器上執行此應用程式。如果不是，請在閘道機器上執行應用程式，然後嘗試加密認證。
- 如果您看到與資料存放區連接或驅動程式相關的錯誤，請在閘道機器上啟動**資料管理閘道組態管理員**，切換到 [診斷] 索引標籤，為 [使用此閘道來測試與內部部署資料來源的連接] 群組中的欄位選取或輸入適當的值，然後按一下 [測試連接]，以確認您是否可以使用連接資訊和認證從閘道機器連接到內部部署資料來源。如果在安裝驅動程式後測試連接仍然失敗，請重新啟動閘道器以讓它取得最新的變更。

	![測試連接](./media/data-factory-data-management-gateway/TestConnection.png)

### 將閘道記錄檔傳送給 Microsoft
如因閘道問題而需要連絡 Microsoft 支援服務，系統可能會要求共用您的閘道記錄檔。閘道的發行可讓您透過在閘道組態管理員上點選按鈕兩次，輕鬆地共用所需的閘道記錄檔。

1. 切換至閘道組態管理員的 [診斷] 索引標籤。
 
	![資料管理閘道 - 診斷索引標籤](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. 按一下 [傳送記錄檔] 連結，以查看下列對話方塊。

	![資料管理閘道 - 傳送記錄檔](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (選擇性) 按一下 [檢視記錄檔] 以在事件檢視器檢閱記錄檔。
4. (選擇性) 按一下 [隱私權] 以檢閱 Microsoft 線上服務隱私權聲明。
3. 一旦您滿意即將上傳的內容，請按一下 [傳送記錄檔]，將過去 7 天的記錄檔傳送給 Microsoft 進行疑難排解。您應該會看到傳送記錄檔作業的狀態，如下所示。

	![資料管理閘道 - 傳送記錄檔狀態](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. 作業完成後，您會看到一個對話方塊，如下所示。
	
	![資料管理閘道 - 傳送記錄檔狀態](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. 請記下**報告識別碼**並與 Microsoft 支援服務共用。報告識別碼是用來尋找為了進行疑難排解所上傳的閘道記錄檔。報告識別碼也會儲存在事件檢視器，供您參考。查看事件識別碼 "25" 即可找到它，並檢查日期和時間。
	
	![資料管理閘道 - 傳送記錄檔報告識別碼](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### 在閘道主機電腦上封存閘道記錄檔
在某些情況下，您會有閘道問題但無法直接共用閘道記錄檔︰

- 當使用者以手動方式安裝閘道和註冊閘道時；
- 當使用者嘗試在組態管理員上使用重新產生的金鑰註冊閘道時；
- 當使用者嘗試傳送記錄檔，但閘道主機服務無法連線時；

在這類情況下，您可以將閘道記錄檔儲存為 zip 檔案，並在稍後連絡 Microsoft 支援服務時共用。例如，如果您在註冊閘道時收到如下所示的錯誤︰

![資料管理閘道 - 註冊錯誤](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

按一下 [封存閘道] 記錄檔連結以封存和儲存記錄檔，然後與 Microsoft 支援服務共用 zip 檔案。

![資料管理閘道 - 封存記錄檔](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## 在電腦之間移動閘道
本節提供將閘道器用戶端從一台電腦移至另一台電腦的步驟。

2. 在入口網站中，瀏覽至 **Data Factory 首頁**，然後按一下 [連結服務] 磚。

	![資料閘道連結](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. 在 [連結服務] 刀鋒視窗的 [資料閘道器] 區段中選取您的閘道器。
	
	![[連結服務] 刀鋒視窗與所選取的閘道器](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. 在 [資料閘道器] 刀鋒視窗中，按一下 [下載並安裝資料閘道器]。
	
	![下載閘道器連結](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. 在 [設定] 刀鋒視窗中，按一下 [下載並安裝資料閘道器]，並遵循指示將資料閘道器安裝在電腦上。

	![設定刀鋒視窗](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. 讓 [Microsoft 資料管理閘道器組態管理員]保持開啟。
 
	![組態管理員](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. 在入口網站的 [設定] 刀鋒視窗中，按一下命令列上的 [重新建立金鑰]，然後按一下警告訊息中的 [是]。按一下金鑰文字旁的**複製按鈕**，將金鑰複製到剪貼簿。請注意，一旦重新建立索引鍵，舊電腦上的閘道器便會停止運作。
	
	![重新建立索引鍵](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. 將**金鑰**貼到電腦上 [資料管理閘道器組態管理員] 之 [註冊閘道器] 頁面的文字方塊中。(選擇性) 按一下 [顯示閘道器金鑰] 核取方塊以查看金鑰文字。
 
	![複製金鑰與註冊](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. 按一下 [註冊] 透過雲端服務註冊閘道器。
10. 在 [設定] 索引標籤上，按一下 [變更] 以選取舊閘道所使用的憑證，輸入**密碼**，然後按一下 [完成]。
 
	![指定憑證](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	您可以執行下列步驟從舊閘道器中匯出憑證：啟動舊電腦上的 [資料管理閘道器組態管理員]，切換到 [憑證] 索引標籤，按一下 [匯出] 按鈕，然後遵循指示進行。
10. 成功註冊閘道器後，閘道器組態管理員首頁上的 [註冊] 應會設定為 [已註冊]，[狀態] 會設定為 [已啟動]。

## 加密認證 
若要在 Data Factory 編輯器中加密認證，請執行下列作業︰

1. 啟動**閘道電腦**上的網頁瀏覽器，瀏覽至 [Azure 入口網站](http://portal.azure.com)，視需要搜尋您的 Data Factory，在 [DATA FACTORY] 刀鋒視窗中開啟 Data Factory，然後按一下 [製作和部署] 來啟動 Data Factory 編輯器。
1. 在樹狀檢視中按一下現有的**連結服務**，以查看其 JSON 定義或建立需要「資料管理閘道」(例如︰SQL Server 或 Oracle) 的新連結服務。
2. 在 JSON 編輯器中，為 **gatewayName** 屬性輸入閘道的名稱。
3. 在 **connectionString** 中輸入**資料來源**屬性的伺服器名稱。
4. 在 **connectionString** 中輸入**初始目錄**屬性的資料庫名稱。
5. 在命令列上按一下 [加密] 按鈕，啟動 Click Once **認證管理員**應用程式。您應該會看見 [設定認證] 對話方塊。![設定認證對話方塊](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. 在 [設定認證] 對話方塊中，執行下列動作：
	1.	選取您要 Data Factory 服務用來連接到資料庫的**驗證**。
	2.	在 [使用者名稱] 設定中輸入可存取資料庫的使用者名稱。
	3.	在 [密碼] 設定中輸入使用者的密碼。
	4.	按一下 [確定] 以加密認證並關閉對話方塊。
5.	您現在應該會在 **connectionString** 中看到 **encryptedCredential** 屬性。
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

如果您從閘道器電腦以外的另一台電腦存取入口網站，您必須確定「認證管理員」應用程式可以連接到閘道器電腦。如果應用程式無法連接閘道器電腦，它將不允許您設定資料來源的認證，以及測試資料來源的連接。

當您使用從 Azure 入口網站啟動的**設定認證**應用程式來設定內部部署資料來源的認證時，入口網站會使用您在閘道機器上**資料管理閘道組態管理員**的 [憑證] 索引標籤中指定的憑證來加密認證。

如果您要尋找以 API 為基礎的方法來加密認證，可以使用 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell Cmdlet 來加密認證。此 cmdlet 會使用閘道器設定用來加密認證的憑證。您可以加密這個 Cmdlet 傳回的認證，並將其新增至 JSON 檔案的 **connectionString** 的 **EncryptedCredential** 項目中，此 JSON 檔案是您將搭配 [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) Cmdlet 使用或在入口網站之 Data Factory 編輯器的 JSON 程式碼片段中使用的檔案。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

設定認證只能使用 Data Factory 編輯器，沒有其他方法。如果您使用編輯器建立 SQL Server 連結服務，並且以純文字輸入認證，認證就會使用 Data Factory 服務所擁有的憑證加密，而不使用閘道器設定要使用的憑證。雖然這種方法在某些情況下可能快一點，但也比較不安全。因此，建議您只在開發/測試用途才採用此方法。


## PowerShell Cmdlet 
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。

1. 在系統管理員模式下啟動 **Azure PowerShell**。
2. 請執行下列命令並輸入您的 Azure 認證，登入您的 Azure 帳戶。

	Login-AzureRmAccount
2. 使用 **New-AzureRmDataFactoryGateway** Cmdlet 來建立邏輯閘道器，如下所示：

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**範例命令和輸出**：


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. 在 Azure PowerShell 中，切換到資料夾：「C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript」，然後執行與區域變數 **$Key** 相關聯的 **RegisterGateway.ps1** 指令碼 (如下列命令所示)，將您電腦上安裝的用戶端代理程式，註冊到您稍早建立的邏輯閘道。

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	您可以使用 IsRegisterOnRemoteMachine 參數在遠端電腦上註冊閘道器。範例：
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. 您可以使用 **Get-AzureRmDataFactoryGateway** Cmdlet 取得 Data Factory 中的閘道器器清單。當 [狀態] 顯示為 [線上] 時，表示您的閘道器已就緒可供使用。

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

您可以使用 **Remove-AzureRmDataFactoryGateway** Cmdlet 移除閘道器，並使用 **Set-AzureRmDataFactoryGateway** Cmdlet 更新閘道器的說明。如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。

### 使用 PowerShell 列出閘道器

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### 使用 PowerShell 移除閘道器
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## 後續步驟
- 如需閘道的詳細資訊，請參閱[資料管理閘道](data-factory-data-management-gateway.md)文章。

<!---HONumber=AcomDC_0810_2016---->