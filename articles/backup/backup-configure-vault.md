<properties
	pageTitle="將 Windows Server 或用戶端備份至 Azure | Microsoft Azure"
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
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# 將 Windows Server 或用戶端備份至 Azure
本文涵蓋您要準備環境並將 Windows Server (或用戶端) 備份至 Azure 所需依循的程序。它還涵蓋部署備份解決方案的考量。如果您想要第一次嘗試 Azure 備份，這篇文章可快速引導您完成該程序。

![[建立保存庫]](./media/backup-configure-vault/initial-backup-process.png)

## 開始之前
若要將伺服器或用戶端備份至 Azure，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/free/)。

## 步驟 1：建立備份保存庫
若要從伺服器或用戶端備份檔案與資料夾，您必須在要儲存這些資料的地理區域中建立備份保存庫。

### 若要建立備份保存庫

1. 登入 [Azure 入口網站](https://manage.windowsazure.com/)。

2. 按一下 [新增] > [資料服務] > [復原服務] > [備份保存庫]，然後選擇 [快速建立]。

3. 針對 [名稱] 參數，輸入易記的備份保存庫名稱。輸入包含 2 到 50 個字元的名稱。該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。每個訂用帳戶的名稱皆需為唯一名稱。

4. 針對 [**區域**] 參數，選取備份保存庫的地區區域。此選項會決定您的備份資料要傳送到哪個地理區域。透過選擇靠近您位置的地理區域，可以在備份至 Azure 時減少網路延遲。

5. 按一下 [建立保存庫]。

    ![建立備份保存庫](./media/backup-configure-vault/demo-vault-name.png)

    要等備份保存庫建立好，可能需要一些時間。若要檢查狀態，可以監控位於入口網站底部的通知。

    建立備份保存庫之後，您會看到一則訊息表示已成功建立保存庫。它也會在 [復原服務] 資源清單中顯示為 [使用中]。

    ![正在建立保存庫狀態](./media/backup-configure-vault/recovery-services-select-vault.png)

4. 依照此處說明的步驟選取儲存體備援選項。

    >[AZURE.IMPORTANT] 識別儲存體備援選項的最佳時機，在於保存庫建立之後，以及任何電腦註冊至保存庫之前。在項目已註冊至保存庫之後，儲存體備援選項就會鎖定且無法修改。

    如果您使用 Azure 作為主要的備份儲存體端點 (例如，您正從 Windows Server 備份至 Azure)，請考慮挑選 (預設值) [異地備援儲存體](../storage/storage-redundancy.md#geo-redundant-storage)選項。

    如果您使用 Azure 做為第三備份儲存體端點 (例如，您使用 System Center Data Protection Manager 在內部部署環境中儲存本機備份複本，並使用 Azure 來滿足長期保留需求)，則應該考慮選擇[本地備援儲存體](../storage/storage-redundancy.md#locally-redundant-storage)。這將減少在 Azure 中儲存資料的成本，同時針對您可能會接受第三個複本的資料提供較低層級的持久性。

    **選取儲存體備援選項：**

    a.按一下您剛才建立的保存庫。

    b.在 [快速啟動] 頁面上，選取 [設定]。

    ![設定保存庫狀態](./media/backup-configure-vault/configure-vault.png)

    c.選擇適當的儲存體備援選項。

    如果您選取 [本地備援]，您必須按一下 [儲存] (因為 [異地備援] 是預設選項)。

    d.在左側瀏覽窗格中，按一下 [復原服務] 來回到復原服務的資源清單。

## 步驟 2：下載保存庫認證檔
內部部署機器必須先驗證備份保存庫，才能將資料備份至 Azure。驗證是透過*保存庫認證*來達成。保存庫認證檔會透過安全通道，從 Azure 入口網站下載。憑證私密金鑰不會保存在入口網站或服務中。

深入了解如何[使用保存庫認證來驗證備份服務](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file)。

### 將保存庫認證檔下載至本機電腦

1. 在左側導覽窗格中，按一下 [復原服務]，然後選取您所建立的備份保存庫。

    ![IR 已完成](./media/backup-configure-vault/rs-left-nav.png)

2.  在 [快速入門] 頁面上，按一下 [**下載保存庫認證**]。

    入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。保存庫認證檔僅在註冊工作流程期間使用，並於 48 小時後過期。

    保存庫認證檔可以從入口網站下載。

3. 按一下 [儲存] 將保存庫認證檔下載至本機帳戶的 [下載] 資料夾。您也可以從 [儲存] 功能表選取 [另存新檔]，以指定保存庫認證檔的儲存位置。

    >[AZURE.NOTE] 請確保保存庫認證檔儲存在可從您的電腦存取的位置。如果是儲存在檔案共用或伺服器訊息區塊中，請確認您擁有存取權限。

## 步驟 3：下載、安裝和註冊備份代理程式
在您建立備份保存庫並下載保存庫認證檔之後，必須在您每一部 Windows 電腦上安裝代理程式。

### 下載、安裝和註冊代理程式

1. 按一下 [復原服務]，然後選取您要向伺服器註冊的備份保存庫。

2. 在 [快速啟動] 頁面上，按一下 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端的代理程式] 代理程式。然後按一下 [儲存]。

    ![儲存代理程式](./media/backup-configure-vault/agent.png)

3. 在下載 MARSagentinstaller.exe 檔案之後，按一下 [執行] \(或從儲存的位置按兩下 **MARSAgentInstaller.exe**)。

4. 選擇代理程式所需的安裝資料夾和快取資料夾，然後按一下 [下一步]。您指定的快取位置必須至少包含等於備份資料大小 5% 的可用空間。

5. 您可以繼續透過預設 Proxy 設定連線網際網路。若您使用 Proxy 伺服器連線網際網路，請在 [Proxy 組態] 頁面上選取 [使用自訂 Proxy 設定] 核取方塊，然後輸入 Proxy 伺服器詳細資料。若您使用已驗證的 Proxy，請輸入使用者名稱和密碼詳細資料，然後按一下 [下一步]。

7. 按一下 [安裝] 開始代理程式安裝。備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。

8. 安裝代理程式之後，按一下 [繼續註冊] 以繼續工作流程。

9. 在 [保存庫識別] 頁面中，瀏覽並選取先前下載的保存庫認證檔。

    從入口網站下載保存庫認證檔之後，其有效期只有 48 小時。如果您在此頁面上遇到錯誤 (例如「提供的保存庫認證檔已過期」)，請登入入口網站並再次下載保存庫認證檔。

    請確定保存庫認證檔位於可透過設定應用程式存取的位置。如果您遇到存取權相關的錯誤，請將保存庫認證檔複製到同一部電腦中的暫存位置並重試作業。

    如果您遇到保存庫認證錯誤 (例如「提供的保存庫認證無效」)，表示檔案已經損毀或沒有和復原服務關聯的最新認證。從入口網站下載新的保存庫認證檔後重試作業。若使用者連續快速地按下 [下載保存庫認證] 選項數次，也可能會發生此錯誤。在此情況下，只有最後一個保存庫認證檔有效。

9. 在 [加密設定] 頁面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。請記得將複雜密碼存放在安全的位置。

10. 按一下 [完成]。[註冊伺服器精靈] 會向備份註冊伺服器。


    > [AZURE.WARNING] 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。加密複雜密碼為您所擁有，Microsoft 並無法看到您所使用的複雜密碼。請將檔案儲存在安全的位置，因為在復原作業期間將會需要該檔案。

11. 設定加密金鑰之後，請讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已選取狀態，然後按一下 [關閉]。

## 步驟 4︰完成初始備份

初始備份包括兩項重要工作：

- 建立備份排程
- 第一次備份檔案和資料夾

在備份原則完成初始備份之後，它會建立在您需要復原資料時可使用的備份點。備份原則會依據您定義的排程執行此作業。

### 排程備份

1. 開啟 Microsoft Azure 備份代理程式。(如果您在關閉註冊伺服器精靈時，讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已選取狀態，備份代理程式將會自動開啟。) 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。

    ![啟動 Azure 備份代理程式](./media/backup-configure-vault/snap-in-search.png)

2. 在備份代理程式中，按一下 [排程備份]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-backup-close.png)

3. 在排程備份精靈的 [開始使用] 頁面上，按一下 [下一步]。

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

**啟用網路節流**

1. 在備份代理程式中，按一下 [變更屬性]。

    ![變更屬性](./media/backup-configure-vault/change-properties.png)

2. 在 [節流] 索引標籤上，選取 [啟用備份操作的網際網路頻寬使用節流設定] 核取方塊。

    ![網路節流](./media/backup-configure-vault/throttling-dialog.png)

3. 在您啟用節流之後，請指定允許的頻寬進行 [工作時間] 和 [非工作時間] 期間的備份資料傳輸。

    頻寬值從每秒 512 KB (KBps) 開始，並可高達每秒 1023 MB (MBps)。您也可以指定 [工作時間] 的開始和完成時間，以及一週中有哪幾天視為工作天。指定之工作時間以外的時間則視為非工作時間。

4. 按一下 [確定]。

### 立即備份

1. 在備份代理程式中按一下 [立即備份]，以透過網路完成初始植入。

    ![Windows Server 立即備份](./media/backup-configure-vault/backup-now.png)

2. 在 [確認] 頁面上，檢閱立即備份精靈將用於備份電腦的設定。然後按一下 [備份]。

3. 按一下 [關閉] 即可關閉精靈。如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。

完成初始備份之後，備份主控台中會顯示 [作業已完成] 狀態。

![IR 已完成](./media/backup-configure-vault/ircomplete.png)

## 後續步驟
- 註冊[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

如需備份 VM 或其他工作負載的詳細資訊，請參閱︰

- [備份 IaaS VM](backup-azure-vms-prepare.md)
- [使用 Microsoft Azure 備份伺服器備份工作負載](backup-azure-microsoft-azure-backup.md)
- [使用 DPM 將工作負載備份到 Azure](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0406_2016-->