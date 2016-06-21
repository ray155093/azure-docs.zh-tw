
<properties
	pageTitle="管理和監視 Resource Manager 部署的虛擬機器備份 | Microsoft Azure"
	description="了解如何管理和監視 Resource Manager 部署的虛擬機器備份"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# 管理和監視 Azure 虛擬機器備份

> [AZURE.SELECTOR]
- [管理 Azure VM 備份](backup-azure-manage-vms.md)
- [管理傳統 VM 備份](backup-azure-manage-vms-classic.md)

本文提供有關如何管理虛擬機器 (VM) 備份的指引，並說明入口網站儀表板中可用的備份資訊。本文中的指引適用於 VM 與復原服務保存庫搭配使用。本文並未涵蓋建立虛擬機器，也不說明如何保護虛擬機器。有關在 Azure 中以復原服務保存庫來保護 Azure Resource Manager 部署的 VM 的初步解說，請參閱[搶先目睹︰將 VM 備份至復原服務保存庫](backup-azure-vms-first-look-arm.md)。

## 存取保存庫與受保護的虛擬機器

在 Azure 入口網站中，復原服務保存庫儀表板可讓您存取保存庫的相關資訊，包括︰

- 最近的備份快照，也就是最新的還原點 <br>
- 備份原則 <br>
- 所有備份快照的大小總計 <br>
- 使用保存庫保護的虛擬機器數目 <br>

虛擬機器備份的許多管理工作都是從儀表板中開啟保存庫開始。不過，因為保存庫可以用來保護多個項目 (或多個虛擬機器)，若要檢視特定虛擬機器的詳細資料，您需要開啟保存庫項目儀表板。下列程序示範如何開啟保存庫儀表板，然後繼續開啟保存庫項目儀表板。兩個程序都有「提示」，指出如何使用「釘選到儀表板」命令，將保存庫和保存庫項目新增至 Azure 儀表板。「釘選到儀表板」是建立保存庫捷徑或項目捷徑的方法。您也可以從捷徑執行常用的命令。

>[AZURE.TIP] 如果有多個儀表板和刀鋒視窗開啟，您可以使用視窗底部的深藍色滑桿，在 Azure 儀表板中來回滑動檢視。

![使用滑桿以完整檢視](./media/backup-azure-manage-vms/bottom-slider.png)

### 在儀表板中開啟復原服務保存庫︰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 [中樞] 功能表上按一下 [瀏覽]，然後在資源清單中輸入**復原服務**。當您開始輸入時，清單將會根據您輸入的文字進行篩選。按一下 [復原服務保存庫]。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    隨即會顯示 [復原服務保存庫] 清單。

    ![復原服務保存庫清單](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] 如果您將保存庫釘選到 Azure 儀表板，則一開啟 Azure 入口網站就可立即存取該保存庫。若要將保存庫釘選到儀表板，請在保存庫清單中以滑鼠右鍵按一下保存庫，然後選取 [釘選到儀表板]。

3. 從保存庫清單中，選取保存庫以開啟其儀表板。選取保存庫時，保存庫儀表板和 [設定] 刀鋒視窗將會開啟。下圖中反白顯示 **Contoso-vault** 儀表板。

    ![開啟保存庫儀表板和設定刀鋒視窗](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### 開啟保存庫項目儀表板

上一個程序中，您開啟保存庫儀表板。若要開啟保存庫項目儀表板︰

1. 在保存庫儀表板的 [備份項目] 圖格上，按一下 [Azure 虛擬機器]。

    ![開啟備份項目備份圖格](./media/backup-azure-manage-vms/contoso-vault.png)

    [備份項目] 刀鋒視窗會列出每個項目的最後一個備份作業。在此範例中，有一個受此保存庫保護的虛擬機器：demovm markgal。

    ![備份項目圖格](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] 為了方便存取，您可以將保存庫項目釘選到 Azure 儀表板。若要釘選保存庫項目，請以滑鼠右鍵按一下項目，然後選取 [釘選到儀表板]。

2. 在 [備份項目] 刀鋒視窗中，按一下項目以開啟保存庫項目儀表板。

    ![備份項目圖格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    保存庫項目儀表板及其 [設定] 刀鋒視窗隨即開啟。

    ![搭配設定刀鋒視窗的備份項目儀表板](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    從保存庫項目儀表板，您可以完成許多重要的管理工作，例如︰

    - 變更原則或建立新的備份原則 <br>
	- 檢視還原點並查看其一致性狀態 <br>
	- 隨選備份虛擬機器 <br>
	- 停止保護虛擬機器 <br>
	- 繼續保護虛擬機器 <br>
	- 刪除備份資料 (或復原點) <br>
	- [還原備份 (或復原點)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

在以下的程序中，起始點是保存庫項目儀表板。

## 變更原則或建立新的備份原則

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)中，按一下 [所有設定] 以開啟 [設定] 刀鋒視窗。

    ![備份原則刀鋒視窗](./media/backup-azure-manage-vms/all-settings-button.png)

2. 在 [設定] 刀鋒視窗上，按一下 [備份原則] 以開啟該刀鋒視窗。

    刀鋒視窗上會顯示備份頻率和保留範圍詳細資料。

    ![備份原則刀鋒視窗](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. 從 [選擇備份原則] 功能表︰
    - 若要變更原則，請選取不同的原則，然後按一下 [儲存]。新原則時會立即套用至保存庫。<br>
    - 若要建立新原則，請選取 [建立新項目]。

    ![虛擬機器備份](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    如需建立備份原則的指示，請參閱[定義備份原則](backup-azure-manage-vms.md#defining-a-backup-policy)。


## 虛擬機器的隨選備份
設定保護後，您可以執行虛擬機器的隨選備份。如果虛擬機器的初始備份已暫止，則隨選備份會在復原服務保存庫中建立虛擬機器的完整複本。如果已完成初始備份，隨選備份只會將前一個備份快照的變更傳送到復原服務保存庫 (亦即一律是增量備份)。

>[AZURE.NOTE] 隨選備份的保留範圍是原則中為「每日」備份點所指定的保留值。如果未選取任何「每日」備份點，則會使用每週備份點。

若要觸發虛擬機器的隨選備份：

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [立即備份]。

    ![立即備份按鈕](./media/backup-azure-manage-vms/backup-now-button.png)

    入口網站會確認您想要啟動隨選備份作業。按一下 [是] 啟動備份作業。

    ![立即備份按鈕](./media/backup-azure-manage-vms/backup-now-check.png)

    備份作業會建立新的復原點。建立的復原點保留範圍，與在虛擬機器相關的原則中指定的保留範圍相同。若要追蹤作業的進度，請在保存庫儀表板中按一下 [備份作業] 圖格。


## 停止保護虛擬機器
如果您選擇停止保護虛擬機器，系統會詢問您是否要保留復原點。有兩種方式可停止保護虛擬機器︰停止所有未來備份作業並刪除所有復原點，或停止所有未來備份作業但保留復原點。在儲存體中保留復原點需要付出相關的成本。不過，保留復原點的好處是以後可以在需要時還原虛擬機器。如需這類虛擬機器的定價詳細資訊，請按一下[這裡](https://azure.microsoft.com/pricing/details/backup/)。如果您選擇刪除所有復原點，則無法還原虛擬機器。

若要停止保護虛擬機器：

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [停止備份]。

    ![停止備份按鈕](./media/backup-azure-manage-vms/stop-backup-button.png)

    [停止備份] 刀鋒視窗隨即開啟。

    ![停止備份刀鋒視窗](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. 在 [停止備份] 刀鋒視窗上，選擇要保留還是刪除備份資料。資訊方塊會針對您的選擇提供詳細資料。

    ![停止保護](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. 如果您選擇要保留備份資料，請跳至步驟 4。如果您選擇要刪除備份資料，請確認您想要停止備份作業，並刪除復原點 - 輸入項目的名稱。

    ![停止驗證](./media/backup-azure-manage-vms/item-verification-box.png)

    如果您不確定項目名稱，請以滑鼠暫留在驚嘆號來檢視名稱。項目的名稱也出現在刀鋒視窗頂端的 [停止備份] 下。

4. 選擇性地提供 [原因] 或 [註解]。

5. 若要停止目前項目的備份作業，請按一下 ![停止備份按鈕](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    通知訊息可讓您知道備份作業已停止。

    ![確認停止保護](./media/backup-azure-manage-vms/stop-message.png)


## 繼續保護虛擬機器
如果在停止保護虛擬機器時已選擇 [保留備份資料] 選項，則可以繼續保護。如果已選擇 [刪除備份資料] 選項，則無法繼續保護虛擬機器。

繼續保護虛擬機器

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [繼續備份]。

    ![繼續保護](./media/backup-azure-manage-vms/resume-backup-button.png)

    [備份原則] 刀鋒視窗隨即開啟。

    >[AZURE.NOTE] 重新保護虛擬機器時，您可以選擇與最初用於保護虛擬機器不同的原則。

2. 請依照[變更原則或建立新的備份原則](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy)中的步驟，指派虛擬機器的原則。

    備份原則套用至虛擬機器後，您會看到下列訊息。

    ![已成功保護 VM](./media/backup-azure-manage-vms/success-message.png)

## 刪除備份資料
您可以在**停止備份**作業期間，或備份停止之後的任何時間，刪除與虛擬機器相關聯的備份資料。允許停止虛擬機器的備份作業，並等待幾天或幾週再決定是否刪除復原點，如果這樣有好處，那就有可能如此。不同於還原復原點，當您刪除備份資料時，您無法選擇刪除特定的復原點。如果您選擇將它刪除，則會刪除與項目相關聯的所有復原點。

下列程序假設虛擬機器的備份作業已停止或停用。僅當備份作業停用後，保存庫項目儀表板中才有 [繼續備份] 和 [刪除備份] 選項可用。

![繼續和刪除按鈕](./media/backup-azure-manage-vms/resume-delete-buttons.png)

若要在「備份已停用」的情況下刪除虛擬機器上的備份資料：

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [刪除備份]。

    ![VM 類型](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    [刪除備份資料] 刀鋒視窗隨即開啟。

    ![VM 類型](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. 您必須輸入項目的名稱，以確認您想要刪除復原點。

    ![停止驗證](./media/backup-azure-manage-vms/item-verification-box.png)

    如果您不確定項目名稱，請以滑鼠暫留在驚嘆號來檢視名稱。項目的名稱也出現刀鋒視窗頂端的 [刪除備份資料] 下。

3. 選擇性地提供 [原因] 或 [註解]。

4. 若要刪除目前項目的備份資料，請按一下 ![停止備份按鈕](./media/backup-azure-manage-vms/delete-button.png)

    通知訊息可讓您知道備份資料已刪除。

## 稽核作業
您可以檢閱作業和事件記錄檔，查看已在復原服務保存庫上執行的管理作業。作業記錄檔會啟用備份作業的絕佳事後剖析和稽核支援。您可以使用稽核記錄檔功能來檢視訂用帳戶中所有作業的記錄。如需事件、作業和稽核記錄檔的詳細資訊，請參閱[檢視事件和稽核記錄檔](../azure-portal/insights-debugging-with-events.md)。使用 [稽核記錄檔] 設定，以檢視復原服務保存庫或保存庫項目特有的事件和作業記錄。

稽核記錄檔中會記錄下列作業：

- 註冊
- 取消註冊
- 設定保護
- 備份 (排程和隨選備份兩者)
- 還原
- 停止保護
- 刪除備份資料
- Add policy
- 刪除原則
- 更新原則
- 取消工作

若要檢視復原服務保存庫的事件記錄檔︰

1. 在[保存庫儀表板](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)中，瀏覽並按一下 [稽核記錄檔]，以開啟 [事件] 刀鋒視窗。

    ![稽核記錄檔](./media/backup-azure-manage-vms/audit-logs.png)

    開啟的 [事件] 刀鋒視窗會顯示針對目前保存庫而篩選的作業事件。此刀鋒視窗會顯示過去一週發生的嚴重、錯誤、警告和資訊事件清單。時間範圍是在 [篩選] 設定中設定的預設值。[事件] 刀鋒視窗也會顯示橫條圖來追蹤事件發生的時間。如果您不想看到橫條圖，請在 [事件] 功能表中按一下 [顯示圖表]，以關閉圖表。

    ![稽核記錄檔篩選器](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. 如需特定作業的其他資訊，請從作業清單中按一下該作業，以開啟其刀鋒視窗。此刀鋒視窗包含作業的詳細資訊，以及時間範圍內發生的事件清單。

    ![作業詳細資料](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. 若要檢視特定事件的詳細資訊，請從事件清單中按一下作業，以開啟其 [詳細資料] 刀鋒視窗。

    ![事件詳細資料](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    事件層級資訊隨著資訊越多而越詳細。此程序的其餘部分將說明如何編輯或變更可用的資訊。

4. 若要編輯可用的篩選器清單，請在 [事件] 功能表上按一下 [篩選]，以開啟該刀鋒視窗。

    ![開放篩選刀鋒視窗](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. 在 [篩選] 刀鋒視窗上，調整 [層級]、[時間範圍] 和 [呼叫者] 篩選器。其他篩選器已設定為提供復原服務保存庫的目前資訊，所以無法使用。

    ![稽核記錄檔查詢詳細資料](./media/backup-azure-manage-vms/filter-blade.png)

    您可以指定事件的**層級**︰嚴重、錯誤、警告或資訊。您可以選擇任何事件層級組合，但必須選取至少一個層級。開啟或關閉層級。[時間範圍] 篩選器可讓您指定時間長度來擷取事件。如果您使用自訂的時間範圍，您可以設定開始和結束時間。

6. 當您準備好使用篩選器來查詢作業記錄時，請按一下 [更新]。結果會顯示在 [事件] 刀鋒視窗中。

    ![作業詳細資料](./media/backup-azure-manage-vms/edited-list-of-events.png)


## 警示通知
您可以在入口網站取得作業的自訂警示通知。若要取得這些作業，請在作業記錄事件中定義以 PowerShell 為基礎的警示規則。使用 PowerShell 1.3.0 版或更新版本。

若要定義自訂通知以警示備份失敗，請使用如下的命令：

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**︰您可以從稽核記錄檔中取得這項資訊。作業記錄檔的 [資源] 資料行中提供 ResourceId。

**OperationName**：格式將會是 "Microsoft.RecoveryServices/recoveryServicesVault/<EventName>"，其中 EventName 為以下任一值：Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Status**：支援的值為 - Started、Succeeded 和 Failed。

**ResourceGroup**︰這是資源所屬的資源群組。您可以將 [資源群組] 資料行加入至產生的記錄檔。資源群組是其中一個可用的事件資訊類型。

**Name**：警示規則的名稱。

**CustomEmail**：指定您要將警示通知寄到的自訂電子郵件地址

**SendToServiceOwners**：此選項會將警示通知傳送給訂用帳戶的所有系統管理員和共同管理員。它可以用於 **New-AzureRmAlertRuleEmail** Cmdlet 中

### 警示的限制
以事件為基礎的警示受到下列限制：

1. 在復原服務保存庫中的所有虛擬機器上觸發警示。您無法針對復原服務保存庫中的一組特定虛擬機器自訂警示。
2. 這項功能處於預覽狀態。[深入了解](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. 警示會從 "alerts-noreply@mail.windowsazure.com" 寄出。目前您無法修改電子郵件寄件者。

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## 後續步驟

如需從復原點重新建立虛擬機器的詳細資訊，請參閱[還原 Azure VM](backup-azure-restore-vms.md)。如需保護虛擬機器的詳細資訊，請參閱[搶先目睹︰將 VM 備份至復原服務保存庫](backup-azure-vms-first-look-arm.md)。

<!---HONumber=AcomDC_0608_2016-->