<properties
	pageTitle="使用 Resource Manager 部署模型將 Windows Server 或用戶端備份到 Azure | Microsoft Azure"
	description="藉由建立備份保存庫、下載認證、安裝備份代理程式，並完成檔案和資料夾的初始備份，來將 Windows Server 或用戶端備份到 Azure。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="備份保存庫；備份 Windows Server；備份Windows；"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="jimpark; trinadhk; markgal"/>

# 使用 Resource Manager 部署模型將 Windows Server 或用戶端備份到 Azure

> [AZURE.SELECTOR]
- [Azure 入口網站](backup-configure-vault.md)
- [傳統入口網站](backup-configure-vault-classic.md)

本文說明如何 Resource Manager 部署模型將 Windows Server (或 Windows 用戶端) 檔案和資料夾備份至 Azure。

![備份程序步驟](./media/backup-configure-vault/initial-backup-process.png)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

## 開始之前
若要將伺服器或用戶端備份至 Azure，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/free/)。

## 步驟 1：建立復原服務保存庫

復原服務保存庫是一個實體，會儲存歷來建立的所有備份和復原點。復原服務保存庫也包含套用至受保護檔案和資料夾的備份原則。當您建立復原服務保存庫時，也應該選取適當的儲存體備援選項。

### 建立復原服務保存庫

1. 如果您尚未這麼做，請使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 [中樞] 功能表上按一下 [瀏覽]，然後在資源清單中輸入**復原服務**。當您開始輸入時，清單將會根據您輸入的文字進行篩選。按一下 [復原服務保存庫]。

    ![建立復原服務保存庫的步驟 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    隨即會顯示 [復原服務保存庫] 清單。

3. 在 [復原服務保存庫] 功能表上，按一下 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-configure-vault/rs-vault-menu.png)

    [復原服務保存庫] 刀鋒視窗隨即開啟，並提示您提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置]。

    ![建立復原服務保存庫的步驟 5](./media/backup-configure-vault/rs-vault-attributes.png)

4. 在 [名稱] 中，輸入易記名稱來識別保存庫。必須是 Azure 訂用帳戶中唯一的名稱。輸入包含 2 到 50 個字元的名稱。該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

5. 按一下 [訂用帳戶] 以查看可用的訂用帳戶清單。如果您不確定要使用哪個訂用帳戶，請使用預設 (或建議) 的訂用帳戶。只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。

6. 按一下 [資源群組] 以查看可用的資源群組清單，或按一下 [新增] 以建立新的資源群組。如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)

7. 按一下 [位置] 以選取保存庫的地理區域。此選項會決定您的備份資料要傳送到哪個地理區域。透過選擇靠近您位置的地理區域，可以在備份至 Azure 時減少網路延遲。

8. 按一下 [建立]。要等復原服務保存庫建立好，可能需要一些時間。請監視入口網站右上方區域中的狀態通知。保存庫一旦建立好，應該就會在入口網站中開啟。如果保存庫在完成之後並沒有在清單中列出，請按一下 [重新整理]。清單重新整理之後，按一下保存庫的名稱。

### 決定儲存體備援
首次建立復原服務保存庫時會決定儲存體的複寫方式。

1. 在保存庫儀表板會自動開啟的 [設定] 刀鋒視窗中，按一下 [備份基礎結構]。

2. 在 [備份基礎結構] 刀鋒視窗中，按一下 [備份組態] 以檢視 [儲存體複寫類型]。

    ![建立復原服務保存庫的步驟 5](./media/backup-configure-vault/backup-infrastructure.png)

3. 選擇保存庫的儲存體複寫選項。

    ![復原服務保存庫清單](./media/backup-configure-vault/choose-storage-configuration.png)

    根據預設，保存庫具有異地備援儲存體。如果您使用 Azure 做為主要的備份儲存體端點，請繼續使用異地備援儲存體。如果您使用 Azure 做為非主要的備份儲存體端點，則請選擇本地備援儲存體，以減少在 Azure 中儲存資料的成本。在此[概觀](../storage/storage-redundancy.md)中，深入了解[異地備援](../storage/storage-redundancy.md#geo-redundant-storage)和[本地備援](../storage/storage-redundancy.md#locally-redundant-storage)儲存體選項。

    選擇好保存庫的儲存體選項後，就可以開始建立檔案和資料夾與保存庫的關聯。

現在您已建立保存庫，接下來您要下載及安裝 Microsoft Azure 復原服務代理程式、下載保存庫認證，以及使用這些認證向保存庫註冊代理程式，讓基礎結構做好備份檔案和資料夾的準備。

## 步驟 2 - 下載檔案

>[AZURE.NOTE] 透過 Azure 入口網站進行備份的功能很快就會推出。目前，您可以在內部部署使用 Microsoft Azure 復原服務代理程式來備份檔案和資料夾。

1. 按一下復原服務保存庫儀表板上的 [設定]。

    ![開啟備份目標刀鋒視窗](./media/backup-configure-vault/settings-button.png)

2. 按一下 [設定] 刀鋒視窗上的 [開始使用] > [備份]。

    ![開啟備份目標刀鋒視窗](./media/backup-configure-vault/getting-started-backup.png)

3. 按一下 [備份] 刀鋒視窗上的 [備份目標]。

    ![開啟備份目標刀鋒視窗](./media/backup-configure-vault/backup-goal.png)

4. 在 [工作負載的執行位置?] 功能表中選取 [內部部署]。

5. 在 [欲備份的項目?] 功能表中選取 [檔案和資料夾]，然後按一下 [確定]。

#### 下載復原服務代理程式

1. 按一下 [準備基礎結構] 刀鋒視窗中的 [下載適用於 Windows Server 或 Windows 用戶端的代理程式]。

    ![準備基礎結構](./media/backup-configure-vault/prepare-infrastructure-short.png)

2. 按一下下載快顯視窗中的 [儲存]。根據預設，**MARSagentinstaller.exe** 檔案會儲存至 [下載] 資料夾。

#### 下載保存庫認證

1. 按一下 [準備基礎結構] 刀鋒視窗上的 [下載] > [儲存]。

    ![準備基礎結構](./media/backup-configure-vault/prepare-infrastructure-download.png)

## 步驟 3 - 安裝並註冊代理程式

1. 在 [下載] 資料夾 (或其他儲存位置) 找到 **MARSagentinstaller.exe** 並對其按兩下。

2. 完成 Microsoft Azure 復原服務代理程式安裝精靈。若要完成精靈，您需要︰

    - 選擇安裝和快取資料夾的位置。
    - 如果您使用 Proxy 伺服器來連線到網際網路，請提供您的 Proxy 伺服器資訊。
    - 如果您使用已驗證的 Proxy，請提供您的使用者名稱和密碼詳細資料。
    - 提供下載的保存庫認證
    - 將加密複雜密碼存放在安全的位置。

    >[AZURE.NOTE] 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。請將檔案存放在安全的位置。必須有此檔案才能還原備份。

現已安裝代理程式，且已向保存庫註冊您的電腦。您已準備好可以設定及排程備份。

## 步驟 4︰完成初始備份

初始備份包括兩項重要工作：

- 排程備份
- 第一次備份檔案和資料夾

若要完成初始備份，您可以使用 Microsoft Azure 備份代理程式。

### 排程備份

1. 開啟 Microsoft Azure 備份代理程式。您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。

    ![啟動 Azure 備份代理程式](./media/backup-configure-vault/snap-in-search.png)

2. 在備份代理程式中，按一下 [排程備份]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

3. 在排程備份精靈的 [開始使用] 頁面上，按 [下一步]。

4. 在 [選取要備份的項目] 頁面上，按一下 [新增項目]。

5. 選取您要備份的檔案和資料夾，然後按一下 [確定]。

6. 按 [下一步]。

7. 在 [指定備份排程] 頁面上，指定[備份排程]，然後按一下 [下一步]。

    您可以排程每日 (一天最多三次) 或每週備份。

    ![Windows Server 備份項目](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] 如需如何指定備份排程的相關詳細資訊，請參閱[使用 Azure 備份來取代您的磁帶基礎結構](backup-azure-backup-cloud-as-tape.md)一文。

8. 在 [選取保留原則] 頁面上，選取 [保留原則] 做為備份複本。

    保留原則會指定備份將儲存的期間。除了僅針對所有備份點指定「一般原則」之外，您可以指定在進行備份時根據不同的保留原則。您可以修改每日、每週、每月和每年保留原則，以符合您的需求。

9. 在 [選擇初始備份類型] 頁面上，選擇初始備份類型。讓 [自動透過網路] 選項保持已選取狀態，然後按一下 [下一步]。

    您可以透過網路自動備份，也可以離線備份。這篇文章的其餘部分說明自動備份的程序。如果您想要執行離線備份，請檢閱[在 Azure Backup 中離線備份工作流程](backup-azure-backup-import-export.md)一文以了解其他資訊。

10. 在 [確認] 頁面上檢閱資訊，然後按一下 [完成]。

11. 當精靈建立好備份排程時，請按一下 [關閉]。

### 啟用網路節流 (選擇性)

備份代理程式提供網路節流。節流會控制資料傳輸期間的網路頻寬使用方式。如果您需要在上班時間內備份資料，但不希望備份程序干擾其他網際網路流量，這樣的控制會很有幫助。節流適用於備份和還原活動。

>[AZURE.NOTE] 網路節流不適用於 Windows Server 2008 R2 SP1、Windows Server 2008 SP2 或 Windows 7 (含 service pack)。Azure 備份網路節流功能可保證本機作業系統上的服務品質 (QoS)。雖然 Azure 備份可保護這些作業系統，但這些平台上可用的 QoS 版本無法與 Azure 備份網路節流搭配使用。網路節流可使用於所有其他[支援的作業系統](backup-azure-backup-faq.md#installation-amp-configuration)。

**啟用網路節流**

1. 在備份代理程式中，按一下 [變更屬性]。

    ![變更屬性](./media/backup-configure-vault/change-properties.png)

2. 在 [節流] 索引標籤上，選取 [啟用備份操作的網際網路頻寬使用節流設定] 核取方塊。

    ![網路節流](./media/backup-configure-vault/throttling-dialog.png)

3. 在您啟用節流之後，請指定允許的頻寬進行 [工作時間] 和 [非工作時間] 期間的備份資料傳輸。

    頻寬值從每秒 512 KB (Kbps) 開始，並可高達每秒 1023 MB (Mbps)。您也可以指定 [工作時間] 的開始和完成時間，以及一週中有哪幾天視為工作天。指定之工作時間以外的時間則視為非工作時間。

4. 按一下 [確定]。

### 第一次備份檔案和資料夾

1. 在備份代理程式中，按一下 [立即備份]，以透過網路完成初始植入。

    ![Windows Server 立即備份](./media/backup-configure-vault/backup-now.png)

2. 在 [確認] 頁面上，檢閱立即備份精靈將用於備份電腦的設定。然後按一下 [備份]。

3. 按一下 [關閉] 即可關閉精靈。如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。

完成初始備份之後，備份主控台中會顯示 [作業已完成] 狀態。

![IR 已完成](./media/backup-configure-vault/ircomplete.png)

## 有疑問嗎？
如果您有問題，或希望我們加入任何功能，請[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## 後續步驟
如需備份 VM 或其他工作負載的詳細資訊，請參閱︰

- 現在您已備份好檔案和資料夾，接下來您可以[管理您的保存庫和伺服器](backup-azure-manage-windows-server.md)。
- 如果您需要還原備份，請使用本文來[還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。

<!---HONumber=AcomDC_0720_2016-->