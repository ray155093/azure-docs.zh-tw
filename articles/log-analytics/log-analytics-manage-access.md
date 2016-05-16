<properties
	pageTitle="管理對 Log Analytics 的存取 | Microsoft Azure"
	description="使用使用者、帳戶、OMS 工作區和 Azure 帳戶的各種系統管理工作，來管理對 Log Analytics 的存取。"
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

# 管理對 Log Analytics 的存取

若要管理對 Log Analytics 的存取，您將使用使用者、帳戶、OMS 工作區和 Azure 帳戶的各種系統管理工作。若要在 Operations Management Suite (OMS) 中建立新的工作區，請選擇工作區名稱，並建立工作區與您帳戶的關聯，然後選擇地理位置。工作區本質上是一個容器，包含帳戶資訊和帳戶的簡單組態資訊。您或組織的其他成員可能會使用多個 OMS 工作區來管理從所有或部分 IT 基礎結構收集而來的不同資料。

[開始使用 Log Analytics](log-analytics-get-started.md) 文章會向您說明如何快速啟動和執行，本文章的其餘部分則更詳細地描述管理對 OMS 的存取所需的一些動作。

雖然您可能一開始不需要執行每個管理工作，但是下列各節將涵蓋您可以使用的所有常用工作︰

- 管理帳戶和使用者
- 將群組加入現有的工作區
- 判斷您需要的工作區數目
- 將現有的工作區連結到 Azure 訂用帳戶
- 將工作區升級為付費資料方案
- 變更資料方案類型
- 新增 Azure Active Directory 組織到現有的工作區
- 控制對 OMS Log Analytics 資源的存取
- 關閉 OMS 工作區

## 管理帳戶和使用者
您可以使用 [設定] 頁面中的 [帳戶] 索引標籤來管理帳戶和使用者。您可以在這裡執行下列各節中的工作。

![管理使用者](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

### 新增使用者到現有的工作區

使用下列步驟，將使用者或群組加入 OMS 工作區。使用者或群組就能夠檢視與此工作區關聯的所有警示並處理警示。

>[AZURE.NOTE] 如果您想要從 Azure Active Directory 組織帳戶加入使用者或群組，必須先確定您已經建立 OMS 帳戶與 Active Directory 網域的關聯。請參閱[新增 Azure Active Directory 組織到現有的工作區](#add-an-azure-active-directory-organization-to-an-existing-workspace)。

#### 新增使用者到現有的工作區
1. 在 OMS 中，按一下 [設定] 圖格。
2. 按一下 [帳戶] 索引標籤。
3. 在 [管理使用者] 區段中，選擇要新增的帳戶類型：[組織帳戶] 或 [Microsoft 帳戶]。
    - 如果您選擇 [Microsoft 帳戶]，請輸入與該 Microsoft 帳戶相關聯的使用者電子郵件地址。
    - 如果您選擇 [組織帳戶]，您可以輸入使用者或群組的部分名稱或電子郵件別名，系統將會顯示使用者和群組的清單。選取使用者或群組。

    >[AZURE.NOTE] 為了獲得最佳的效能結果，請將與單一 OMS 帳戶相關聯的 Active Directory 群組數目限制為兩個：一個給系統管理員，一個給使用者。使用太多群組可能會影響 Log Analytics 的效能。

7. 選擇要新增的使用者或群組類型：[系統管理員] 或 [使用者]。
8. 按一下 [新增]。

  如果您新增 Microsoft 帳戶，系統將會傳送一封加入工作區的邀請至您提供的電子郵件。使用者依照邀請中的指示加入 OMS 之後，使用者就可以檢視此 OMS 帳戶的警示和帳戶資訊，而且您將能夠在 [設定] 頁面的 [帳戶] 索引標籤上檢視使用者資訊。如果您加入組織帳戶，使用者就能夠立即存取 Log Analytics。![邀請電子郵件](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

### 編輯現有的使用者類型

您可以為與您的 OMS 帳戶相關聯的使用者變更帳戶角色。您有下列角色選項：

 - *管理員*：可以管理使用者、檢視和處理所有警示，以及新增和移除伺服器

 - *使用者*：可以檢視和處理所有警示，以及新增和移除伺服器

#### 編輯帳戶
1. 在 OMS 之 [帳戶] 索引標籤的 [設定] 頁面上，為使用者選取您要變更的角色。
2. 按一下 [確定]。

## 從 OMS 工作區移除使用者

使用下列步驟，從 OMS 工作區移除使用者。請注意，這不會關閉使用者的工作區，而會移除使用者與工作區之間的關聯。如果使用者與多個工作區相關聯，該使用者還是能夠登入 OMS。

### 從工作區移除使用者

1. 在 OMS 之 [帳戶] 索引標籤的 [設定] 頁面上，按一下您要移除之使用者名稱旁邊的 [移除]。
2. 按一下 [確定] 確認您要移除該使用者。


## 將群組加入現有的工作區

1.	遵循上面＜將使用者加入現有的工作區＞中的步驟 1 -4。
2.	在 [選擇使用者/群組] 下方，選取 [群組]。![將群組加入現有的工作區](./media/log-analytics-manage-access/add-group.png)
3.	輸入您想要加入之群組的顯示名稱或電子郵件地址。
4.	在清單結果中選取群組，然後按一下 [加入]。

## 判斷您需要的工作區數目

在 Azure 管理入口網站中，一個工作區被視為是一項 Azure 資源。

您可以建立新的工作區，或連結到您先前使用 System Center Operations Manager 所開啟的現有工作區，但是您尚未與 Azure 訂用帳戶關聯 (必須關聯才能計費)。

工作區代表資料在 OMS 入口網站中進行收集、彙總、分析以及呈現的層級。您可以選擇有多個工作區，區分來自不同環境和系統的資料；每個 Operations Manager 管理群組 (和所有其代理程式) 或個別 VM/代理程式可以分別只與一個工作區連線。

每個工作區可以有多個相關聯的使用者帳戶，而每個使用者帳戶 (Microsoft 帳戶或組織帳戶) 可以存取多個 OMS 工作區。

根據預設，用來建立工作區的 Microsoft 帳戶或組織帳戶會變成工作區的管理員。然後管理員可以邀請其他 Microsoft 帳戶或從其 Azure Active Directory 挑選使用者。

## 將現有的工作區連結到 Azure 訂用帳戶

您可以從 [microsoft.com/oms](https://microsoft.com/oms) 網站建立工作區。不過，這些工作區有某些限制，如果您使用免費帳戶，最明顯的限制是一天最多上傳 500MB 的資料。若要對此工作區進行變更，您必須**將您現有的工作區連結到 Azure 訂用帳戶**。

>[AZURE.IMPORTANT] 如果要連結工作區，您的 Azure 帳戶必須已經能夠存取您想要連結的工作區。換句話說，您用來存取 Azure 入口網站的帳戶必須與您用來存取 OMS 工作區的帳戶**相同**。如果不是，請參閱[新增使用者到現有的工作區](#add-a-user-to-an-existing-workspace)。

1.	登入 [Azure 入口網站](http://portal.azure.com)。
2.	瀏覽 **Log Analytics (OMS)**，然後加以選取。
3.	您將會看到現有工作區清單。按一下 [加入]。![工作區清單](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.	在 [OMS 工作區] 下方，按一下 [或連結現有的]。![連結現有的](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.	按一下 [進行必要設定]。![進行必要設定](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.	您將會看到尚未連結到您 Azure 帳戶的工作區清單。選取工作區。![選取工作區](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.	需要時，您可以變更下列項目的值：
    - 訂閱
    - 資源群組
    - 位置
    - 定價層 ![變更值](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.	按一下 [建立]。工作區現在已連結到您的 Azure 帳戶。

>[AZURE.NOTE] 如果您看不到想要連結的工作區，則您的 Azure 訂用帳戶沒有使用 OMS 網站建立之 OMS 工作區的存取權。您必須使用 OMS 網站從您的 OMS 工作區內部授與此帳戶的存取權。若要這樣做，請參閱[新增使用者到現有的工作區](#add-a-user-to-an-existing-workspace)。



## 將工作區升級為付費資料方案

OMS 有三種工作區資料方案類型：**免費**、**標準**和**進階**。如果您使用*免費*方案，有可能會達到 500MB 的資料容量。此時您將需要將工作區升級為 '**隨用隨付方案**'，才能收集超過此限制的資料。您隨時都可以轉換方案類型。如需 OMS 定價的詳細資訊，請參閱[定價詳細資料](https://www.microsoft.com/zh-TW/server-cloud/operations-management-suite/pricing.aspx)。

>[AZURE.IMPORTANT] 工作區方案只有在*連結*到 Azure 訂用帳戶時才能變更。如果您在 Azure 中建立了工作區，或者如果您*已經*連結了工作區，可以忽略此訊息。如果您是使用 [OMS 網站](http://www.microsoft.com/oms)建立工作區，就必須依照[將現有的工作區連結到 Azure 訂用帳戶](#link-an-existing-workspace-to-an-azure-subscription)的步驟。

### 使用 OMS Add On for System Center 的權利

OMS Add On for System Center 提供 OMS Log Analytics 進階方案的權利 (如 [OMS 定價](https://www.microsoft.com/zh-TW/server-cloud/operations-management-suite/pricing.aspx)中所述)。

如果您購買 OMS Add On for System Center，則 Microsoft 帳戶團隊或轉銷商會建立 OMS 附加元件與包含您 Azure 採購之 Enterprise 合約的關聯。OMS 附加元件會建立合約的權利，而且任何 Azure 訂用帳戶都可以利用該權利。例如，這可讓您的多個 OMS 工作區使用 OMS 附加元件的權利。

若要確保將 OMS 工作區的使用量套用到 OMS 附加元件的權利，您需要︰

1. 將 OMS 工作區連結到 Azure 訂用帳戶，這個 Azure 訂用帳戶屬於同時包含 OMS 附加元件採購和 Azure 訂用帳戶使用量的 Enterprise 合約
2. 選取工作區的進階方案

當您檢閱 Azure 或 OMS 入口網站中的使用量時，不會看到 OMS 附加元件權利。不過，您可以看到企業版入口網站中的權利。

如果需要變更 OMS 工作區所連結的 Azure 訂用帳戶，則可以使用 Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) Cmdlet。

### 透過 Enterprise 合約使用 Azure 承諾

如果您選擇使用 OMS 元件的獨立定價，則需要分開支付 OMS 的每個元件，而且使用量將出現在您的 Azure 帳單上。

如果您已經根據 Enterprise 合約預付特定金額的 Azure 使用量，則使用 OMS 時將使用預付的使用量。若要使用 OMS Log Analytics 的 Azure 承諾定價，OMS 工作區所連結的訂用帳戶必須是 Azure Enterprise 合約的一部分。

如果需要變更 OMS 工作區所連結的 Azure 訂用帳戶，則可以使用 Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) Cmdlet。



### 將工作區變更為付費資料方案

1.	登入 [Azure 入口網站](http://portal.azure.com)。
2.	瀏覽 **Log Analytics (OMS)**，然後加以選取。
3.	您將會看到現有工作區清單。選取工作區。![工作區清單](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.	在 [設定] 下方，按一下 [定價層]。![定價層](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.	在 [定價層] 下方，選取資料方案，然後按一下 [選取]。![選取方案](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.	重新整理您在 Azure 入口網站中的檢視時，會看到所選取方案的 [定價層] 已更新。![更新定價層](./media/log-analytics-manage-access/manage-access-change-plan04.png)

現在，您可以收集超過「免費」資料上限的資料。


## 新增 Azure Active Directory 組織到現有的工作區

您可以建立 Operational Insights (OMS) 工作區與 Azure Active Directory 網域的關聯。這樣可讓您直接將使用者從 Active Directory 加入 OMS 工作區，而不需要個別的 Microsoft 帳戶。

### 新增 Azure Active Directory 組織到現有的工作區

1. 在 OMS 的 [設定] 頁面上，按一下 [帳戶]，然後按一下 [工作區資訊]。  
2. 檢閱組織帳戶的資訊，然後按一下 [加入組織]。![加入組織](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. 輸入您 Azure Active Directory 網域之系統管理員的身分識別資訊。之後，您將會看到通知，指出您的工作區連結到 Azure Active Directory 網域。![連結的工作區通知](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## 控制對 OMS Log Analytics 資源的存取

可以在 2 個位置控制如何將 OMS 工作區的存取權授與使用者：

- 對於 OMS 入口網站的存取權，這是在 OMS 入口網站內進行管理，而且這與使用者是否存取資源所在的 Azure 訂用帳戶不同。
- 對於 PowerShell 和直接 REST API 存取，是使用 Azure RBAC 在 Azure 內進行管理。

如果您已經將 OMS 入口網站存取權授與使用者，而非它所連結的 Azure 訂用帳戶，則在使用者登入 OMS 入口網站時，自動化、備份和 Site Recovery 方案圖格將不會顯示使用者的任何資料。

若要允許所有使用者看到這些解決方案中的資料，請確定他們至少有連結至 OMS 工作區之自動化帳戶、備份保存庫和 Site Recovery 保存庫的**讀者**存取權。


## 關閉 OMS 工作區

當您關閉 OMS 工作區時，與您的工作區相關的所有資料，在關閉工作區之後的 30 天內，都會從 OMS 服務刪除。

如果您是管理員，而且有多位使用者與工作區關聯，則這些使用者與工作區之間的關聯將會中斷。如果使用者與其他工作區相關聯，則他們可以繼續搭配使用 OMS 與其他工作區。不過，如果使用者未與其他工作區相關聯，則他們必須建立新的工作區才能使用 OMS。

### 關閉 OMS 工作區

1. 在 OMS 之 [帳戶] 索引標籤的 [設定] 頁面上，按一下 [關閉工作區]。

2. 選取其中一個關閉工作區的原因，或者在文字方塊中輸入其他原因。

3. 按一下 [關閉工作區]。

## 後續步驟

- 請參閱[將 Windows 電腦連接到 Log Analytics](log-analytics-windows-agents.md)，來加入代理程式以及收集資料。
- [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)，以加入功能和收集資料。
- 如果您的組織使用 Proxy 伺服器或防火牆，請[在 Log Analytics 中設定 Proxy 和防火牆設定](log-analytics-proxy-firewall.md)，以讓代理程式可與 Log Analytics 服務通訊。

<!---HONumber=AcomDC_0504_2016-->