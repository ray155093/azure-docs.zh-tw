<properties
	pageTitle="開始使用 Log Analytics | Microsoft Azure"
	description="您可以使用 Microsoft Operations Management Suite (OMS) 在幾分鐘內啟動並執行 Log Analytics。"
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
	ms.date="04/28/2016"
	ms.author="banders"/>


# 開始使用 Log Analytics

您可以使用 Microsoft Operations Management Suite (OMS) 在幾分鐘內啟動並執行 Log Analytics。在選擇 OMS 工作區的建立方式時，您有兩個選項，就像建立帳戶一樣：

- Microsoft Operations Management Suite 網站
- Microsoft Azure 訂用帳戶

您可以使用 OMS 網站建立 OMS 工作區，也可以使用 Microsoft Azure 訂用帳戶建立 OMS 工作區。這兩個工作區擁有相同的功能。如果您使用 Azure 訂用帳戶，也可以使用該訂用帳戶來存取其他 Azure 服務。不論建立工作區的方法為何，您都會使用 Microsoft 帳戶或組織帳戶來建立工作區。

以下就來看看此程序：

![上架圖表](./media/log-analytics-get-started/oms-onboard-diagram.png)

## 使用 Operations Management Suite 的 3 個註冊步驟

1. 移至 [Operations Management Suite](http://microsoft.com/oms) 網站並按一下 [免費試用]。登入 Microsoft 帳戶 (如 Outlook.com)，或登入貴公司或教育機構所提供，用來搭配 Office 365 或其他 Microsoft 服務的組織帳戶。
2. 提供唯一的工作區名稱。工作區是儲存管理資料的邏輯容器。它提供在不同組織團隊之間分割資料的方法，因為資料為其工作區專有。指定電子郵件地址和資料的儲存區域。![建立工作區及連結訂用帳戶](./media/log-analytics-get-started/oms-onboard-create-workspace-link.png)
3. 接下來，您可以建立新的 Azure 訂用帳戶或連結現有的 Azure 訂用帳戶。如果您想要繼續使用免費試用版，請按一下 [不是現在]。

您已準備好開始使用 Operations Management Suite 入口網站。

您可以參閱[管理 Log Analytics 的存取權](log-analytics-manage-access.md)，以深入了解工作區設定以及將現有 Azure 帳戶與使用 Operations Management Suite 建立之工作區連結的資訊。

## 使用 Microsoft Azure 快速註冊

1. 前往 [Azure 入口網站](https://portal.azure.com)，登入後瀏覽服務清單，然後選取 [Log Analytics (OMS)]。![Azure 入口網站](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. 按一下 [加入]，然後選取下列項目：
    - **OMS 工作區**名稱
    - **訂用帳戶** - 如果您有多個訂用帳戶，請選擇想要與新工作區建立關聯的帳戶。
    - **資源群組**
    - **位置**
    - **定價層** ![quick create](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. 按一下 [建立]，即會在 Azure 入口網站中看到工作區的詳細資料。![工作區詳細資料](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. 按一下 [OMS 入口網站] 連結，即可開啟包含新工作區的 Operations Management Suite 網站。

您現在已經準備就緒，可以開始使用 Operations Management Suite 入口網站。

您可以參閱[管理 Log Analytics 的存取權](log-analytics-manage-access.md)，以深入了解工作區設定以及將使用 Operations Management Suite 建立之現有工作區與 Azure 訂用帳戶連結的資訊。

## 開始使用 Operations Management Suite 入口網站
若要選擇方案及連接要管理的伺服器，請按一下 [設定] 圖格，然後遵循本節的步驟操作。

![開始使用](./media/log-analytics-get-started/oms-onboard-get-started.png)

- **新增方案** - 選取要使用的方案，然後按一下 [Add selected Solutions] (新增選取的方案)。![解決方案](./media/log-analytics-get-started/oms-onboard-solutions.png)
- **連接資料來源** - 選擇收集資料的伺服器環境連接方式：
    - 藉由安裝代理程式來直接連接任何 Windows Server 或用戶端。
    - 使用 System Center Operations Manager 來附加管理群組或整個 Operations Manager 部署。
    - 使用已設定 Windows 或 Linux Azure 診斷 VM 擴充功能的 Azure 儲存體帳戶。![資料來源](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
- **新增記錄檔**：設定至少一個要擴展資料的資料來源，然後選取 [儲存]。針對事件記錄檔，您可以指定訊息類型，包括要監視的錯誤、警告和資訊。    

    ![記錄檔](./media/log-analytics-get-started/oms-onboard-logs.png)


## (選擇性) 藉由安裝代理程式來直接連接伺服器和 Operations Management Suite
1. 針對您要安裝的電腦架構，依序按一下 [設定] 圖格、[連接的來源] 索引標籤，然後按一下 [下載 Windows 代理程式]。您只能將代理程式安裝在 Windows Server 2008 SP1 或更新版本，亦或是 Windows 7 SP1 或更新版本。
2. 將代理程式安裝在一或多部伺服器上。您可以逐一安裝代理程式、使用搭配[自訂指令碼](log-analytics-windows-agents.md)的自動化方法，也可以使用現有的軟體散發解決方案。
3. 在同意授權合約及選擇安裝資料夾之後，請選取 [Connect the agent to Microsoft Azure Operational Insights] (將代理程式連線至 Microsoft Azure Operational Insights)。(OMS 先前稱為 Operational Insights)。![代理程式設定](./media/log-analytics-get-started/oms-onboard-agent.png)

4. 在下一個頁面中，系統會詢問您的工作區識別碼和工作區金鑰。您的工作區 ID 和金鑰會顯示在下載代理程式檔案的畫面上。![代理程式金鑰](./media/log-analytics-get-started/oms-onboard-mma-keys.png) ![附加伺服器](./media/log-analytics-get-started/oms-onboard-key.png)
5. 在安裝期間，您可以按一下 [進階] 以選擇性地設定 Proxy 伺服器及提供驗證資訊。按 [下一步] 按鈕返回工作區資訊畫面。
6. 按 [下一步] 驗證工作區識別碼和金鑰。如果您發現任何錯誤，可以按一下 [上一頁] 予以修正。當工作區識別碼和金鑰通過驗證後，請按一下 [安裝] 來完成代理程式安裝。
7. 重新登入 Operations Management Suite 入口網站，然後按一下 [概觀] 頁面上的 [設定] 磚。當代理程式與 Operations Management Suite 服務通訊時，會出現綠色核取記號圖示。剛開始的時候，這需要大約 5-10 分鐘。

>[AZURE.NOTE] 直接連接 Operations Management Suite 的伺服器目前不支援容量管理和組態評估解決方案。


您也可以將代理程式連接到 System Center Operations Manager 2012 SP1 和更新版本。若要這樣做，請選取 [將代理程式連接至 System Center Operations Manager]。如果您選擇該選項，可以在不需要額外硬體或對管理群組造成額外負荷的情況下將資料傳送到服務。

若要深入了解將代理程式連接到 Operations Management Suite 的資訊，請參閱[將 Windows 電腦連接到 Log Analytics](log-analytics-windows-agents.md)。

## (選擇性) 使用 System Center Operations Manager 連接伺服器

1. 在 Operations Manager 主控台內選取 [管理]。
2. 展開 [Operational Insights] 節點，然後選取 [Operational Insights 連接]。

  >[AZURE.NOTE] 根據您所使用的 SCOM 更新彙總套件而定，您可能會看到 *System Center Advisor*、*Operational Insights* 或 *Operations Management Suite* 節點。

3. 按一下右上方的 [註冊至 Operational Insights] 連結，並遵循指示執行作業。
4. 完成註冊精靈之後，按一下 [新增電腦/群組] 連結。
5. 在 [電腦搜尋] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取要上架到 Log Analytics 的電腦或群組，按一下 [加入]，然後按一下 [確定]。您只要前往 Operations Management Suite 入口網站的 [使用量] 圖格，即可驗證 OMS 服務是否正在接收資料。資料應該會在大約 5-10 分鐘之後出現。

若要深入了解將 Operations Manager 連接到 Operations Management Suite 的資訊，請參閱[將 Operations Manager 連接至 Log Analytics](log-analytics-om-agents.md)。

## (選擇性) 分析 Microsoft Azure 之雲端服務的資料

利用 Operations Management Suite，您可以啟用 Azure 雲端服務診斷來快速搜尋雲端服務和虛擬機器的事件和 IIS 記錄檔。您也可以安裝 Microsoft Monitoring Agent，取得有關 Azure 虛擬機器的額外見解。若要深入了解如何設定 Azure 環境以使用 Operations Management Suite 的資訊，請參閱[將 Azure 儲存體連接至 Log Analytics](log-analytics-azure-storage.md)。


## 後續步驟

- [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)，以加入功能和收集資料。
- 熟悉[記錄搜尋](log-analytics-log-searches.md)以檢視方案所收集的詳細資訊。
- 使用[儀表板](log-analytics-dashboards.md)來儲存及顯示您的自訂搜尋。

<!---HONumber=AcomDC_0504_2016-->