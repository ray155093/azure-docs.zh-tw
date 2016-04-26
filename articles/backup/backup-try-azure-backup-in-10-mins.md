<properties
   pageTitle="了解如何將檔案和資料夾從 Windows 備份到 Azure | Microsoft Azure"
   description="了解如何透過建立保存庫、安裝備份代理程式，以及將檔案和資料夾備份到 Azure，以備份 Windows Server 資料。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="如何備份; 如何備份"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="04/14/2016"
   ms.author="jimpark;"/>

# 先睹為快︰將檔案和資料夾從 Windows Server 或用戶端備份到 Azure

本文說明如何使用 Azure 備份將 Windows Server (或 Windows 用戶端) 檔案和資料夾備份至 Azure。本文中的教學課程是要讓您逐步了解基本概念。如果您想要開始使用 Azure 備份，請閱讀本文的內容。

如果您想要深入了解 Azure 備份，請閱讀此[概觀](backup-introduction-to-azure-backup.md)。

將檔案和資料夾備份至 Azure 需要進行下列活動︰

![步驟 1](./media/backup-try-azure-backup-in-10-mins/step-1.png)取得 Azure 訂用帳戶 (如果您還沒有的話)。<br> ![步驟 2](./media/backup-try-azure-backup-in-10-mins/step-2.png)建立備份保存庫並下載必要項目。<br> ![步驟 3](./media/backup-try-azure-backup-in-10-mins/step-3.png)安裝和註冊備份代理程式。<br> ![步驟 4](./media/backup-try-azure-backup-in-10-mins/step-4.png)備份檔案和資料夾。


![如何使用 Azure 備份來備份 Windows 電腦](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## 步驟 1：建立 Azure 訂用帳戶

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)，以便存取任何 Azure 服務。

## 步驟 2︰建立備份保存庫並下載必要項目

若要備份檔案與資料夾，您必須在要儲存這些資料的區域中建立備份保存庫。您也要決定儲存體的複寫方式，並下載認證和備份代理程式。

### 若要建立備份保存庫

1. 如果您尚未這麼做，請使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [新增] > [混合式整合] > [備份]。

    ![開始準備備份檔案和資料夾](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. 在 [名稱] 中，輸入易記名稱來識別備份保存庫。

4. 在 [區域] 中，選取與您最接近的區域以加快檔案傳輸速度。

5. 按一下 [建立保存庫]。

    ![建立保存庫](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    備份保存庫就緒之後，就會在 [復原服務] 的資源中列為 [使用中]。

    ![保存庫狀態為使用中](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

在建立保存庫後，您會選取儲存體的複寫方式。

>[AZURE.NOTE] 您必須選擇在保存庫建立好之後、有任何電腦向其註冊之前，儲存體的複寫方式。一旦有項目向保存庫註冊，儲存體複寫就會遭到鎖定且無法修改。

### 選取儲存體的複寫方式

1. 按一下您建立的保存庫。
2. 在 [快速啟動] 頁面上，選取 [設定]。

    ![設定保存庫](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. 選擇適當的儲存體選項。

    如果您使用 Azure 做為您的主要備份，請選擇[異地備援儲存體](../storage/storage-redundancy.md#geo-redundant-storage)。如果您使用 Azure 做為您的第三個備份，請選擇[本地備援儲存體](../storage/storage-redundancy.md#locally-redundant-storage)。

    ![選擇儲存體複寫選項](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. 如果您選取 [本地備援]，請按一下 [儲存]，因為 [異地備援] 是預設值。

您需要使用保存庫認證以向備份保存庫驗證您的電腦。以下是這些認證的下載方法。

### 下載保存庫認證
保存庫認證檔僅在註冊程序期間使用，並於 48 小時後過期。

1. 為了返回保存庫的 [快速啟動] 頁面，請按一下 ![選取新的保存庫](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. 按一下 [下載保存庫認證] > [儲存]。

接著，您必須下載備份代理程式。

### 下載備份代理程式

按一下 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端的代理程式] > [儲存]。

![儲存備份代理程式](./media/backup-try-azure-backup-in-10-mins/agent.png)

現在您已建立保存庫並下載好所有項目，接著您必須安裝和註冊備份代理程式。

## 步驟 3︰安裝和註冊備份代理程式

1. 從儲存的位置按兩下 **MARSagentinstaller.exe**。
2. 完成 Microsoft Azure 復原服務代理程式安裝精靈。若要完成精靈，您需要︰
    - 選擇安裝和快取資料夾的位置。
    - 如果您使用 Proxy 伺服器來連線到網際網路，請提供您的 Proxy 伺服器資訊。
    - 如果您使用已驗證的 Proxy，請提供您的使用者名稱和密碼詳細資料。
    - 將加密複雜密碼存放在安全的位置。

    >[AZURE.NOTE] 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。請將檔案存放在安全的位置。必須有此檔案才能還原備份。

現已安裝代理程式，且已向保存庫註冊您的電腦。您已準備好可以設定及排程備份。

## 步驟 4︰備份檔案和資料夾
如果備份代理程式尚未開啟，您可以透過在電腦中搜尋「Microsoft Azure 備份」來找出備份。

1. 在 [備份代理程式] 中，按一下 [排程備份]。

    ![Windows Server 備份排程](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. 完成「排程備份精靈」。在完成精靈時，您將會︰

    - 選取要備份的檔案和資料夾。
    - 指定備份排程 (每日或每週)。
    - 決定您的保留原則。
    - 選擇您想要如何完成初始備份 (透過網路或離線進行)。

    深入了解[離線完成初始備份](backup-azure-backup-import-export.md)。<br><br>

3. 當精靈完成時返回 [備份代理程式]，然後按一下 [立即備份] 以透過網路完成初始備份。

    ![Windows Server 立即備份](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. 在 [確認] 畫面上按一下 [備份]。如果您在備份程序完成之前關閉精靈，精靈會繼續在背景中執行。

    初始備份完成時，主控台中的 [作業] 檢視就會顯示工作已完成。

    ![初始備份完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

恭喜，您已將檔案和資料夾成功備份到 Azure 備份。

## 後續步驟
- 詳細了解如何[備份 Windows 電腦](backup-configure-vault.md)。
- 現在您已備份好檔案和資料夾，接下來您可以[管理您的保存庫和伺服器](backup-azure-manage-windows-server.md)。
- 如果您需要還原備份，請使用本文來[還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。

<!---HONumber=AcomDC_0420_2016-->