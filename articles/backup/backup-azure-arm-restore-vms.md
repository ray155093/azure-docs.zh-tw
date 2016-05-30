
<properties
	pageTitle="使用 Azure 入口網站從備份還原虛擬機器 | Microsoft Azure"
	description="使用 Azure 入口網站從復原點還原 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="還原備份；如何還原；復原點；"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="trinadhk; jimpark;"/>


# 使用 Azure 入口網站來還原虛擬機器

> [AZURE.SELECTOR]
- [在傳統入口網站中還原 VM](backup-azure-restore-vms.md)
- [在 Azure 入口網站中還原 VM](backup-azure-arm-restore-vms.md)


利用備份服務在定義的間隔取得您資料的快照，可以保護您的資料。這些快照稱為復原點，而且儲存在復原服務保存庫中。如果或需要修復或重新建立 VM，您可以從任何已儲存的復原點還原 VM。當您還原復原點時，您會使 VM 回到或恢復到取得復原點時的狀態。本文說明如何使用還原 VM。

> [AZURE.NOTE] Azure 有兩種用來建立和使用資源的部署模型：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。本文提供的資訊和程序可供還原使用 Resource Manager 模型部署的 VM。



## 還原復原點

1. 登入 [Azure 入口網站](http://ms.portal.azure.com/)。

2. 在 Azure 功能表上按一下 [瀏覽]，然後在服務清單中輸入**還原服務**。服務清單會調整成您輸入的內容。當您看到 [復原服務保存庫] 時，請選取它。

    ![開啟復原服務保存庫](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    便會顯示訂用帳戶中保存庫的清單。

    ![復原服務保存庫清單](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. 從這份清單，選取與您要還原的 VM 相關聯的保存庫。當您按一下保存庫時，其儀表板隨即開啟。

    ![復原服務保存庫清單](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. 您現在已在保存庫儀表板中。在 [備份項目] 圖格上，按一下 [Azure 虛擬機器] 以顯示與保存庫相關聯的 VM。

    ![保存庫儀表板](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    [備份項目] 刀鋒視窗會開啟並顯示 Azure 虛擬機器的清單。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. 從清單中選取 VM，以開啟儀表板。VM 儀表板會開啟至包含 [還原點] 圖格的 [監視] 區域。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. 在 VM 的儀表板功能表上，按一下 [還原]

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    [還原] 刀鋒視窗隨即開啟。

    ![還原刀鋒視窗](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. 在 [還原] 刀鋒視窗上，按一下 [還原點] 以開啟 [選取還原點] 刀鋒視窗。

    ![還原刀鋒視窗](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    根據預設，對話方塊會顯示過去 30 天內的所有還原點。使用刀鋒視窗頂端的 [篩選器]，可變更顯示之還原點的時間範圍。根據預設，系統會顯示所有一致性的還原點。修改 [所有還原點] 篩選器，可選取特定還原點的一致性。如需每種還原點類型的詳細資訊，請參閱[資料一致性](./backup-azure-vms-introduction.md#data-consistency)的說明。
    - [還原點一致性] 選擇以下清單︰
        - 當機時保持一致還原點，
        - 應用程式保持一致還原點，
        - 系統檔案保持一致還原點，
        - 所有還原點。  

8. 選擇還原點，然後按一下 [確定]。

    ![選擇還原點](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    [還原] 刀鋒視窗會顯示已設定還原點。

    ![已設定還原點](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. 在 [還原] 刀鋒視窗中，[還原組態] 會在設定還原點後自動開啟。

    ![已設定還原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## 選擇 VM 還原組態

既然您已選取還原點，請選擇還原 VM 的組態。設定還原 VM 的選項包括使用︰Azure 入口網站或 PowerShell。

> [AZURE.NOTE] 入口網站提供還原 VM 專用的「快速建立」選項。如果您想要自訂即將還原之 VM 的 VM 組態，請使用 PowerShell 來還原備份的磁碟，再將它們附加至所選擇的 VM 組態。請參閱[使用特殊網路組態還原 VM](#restoring-vms-with-special-network-configurations)。

1. 如果您尚未到達該位置，請移至 [還原] 刀鋒視窗。確定已選取 [還原點]，然後按一下 [還原組態] 以開啟 [復原組態] 刀鋒視窗。

    ![已設定復原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. 在 [還原組態] 刀鋒視窗上，輸入或選取下列各欄位的值︰
    - **虛擬機器名稱** - 提供 VM 的名稱。對資源群組 (適用於 ARM VM) 或雲端服務 (適用於傳統 VM) 來說，此名稱必須是唯一的。不支援當虛擬機器存在於訂用帳戶中時取代虛擬機器。
    - **資源群組** - 使用現有資源群組，或建立新的群組。如果您要還原傳統 VM，請使用此欄位來指定新雲端服務的名稱。建立新的資源群組/雲端服務時，此名稱必須是全域唯一的名稱。一般來說，雲端服務名稱會與公眾對應 URL 相關聯 - 例如：[cloudservice].cloudapp.net。如果您嘗試使用已經使用的雲端資源群組/雲端服務名稱，Azure 會指派給資源群組/雲端服務與 VM 相同的名稱。Azure 會顯示未與任何同質群組相關聯的資源群組/雲端服務和 VM。如需詳細資訊，請參閱[如何從同質群組移轉至區域虛擬網路 (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。
    - **虛擬網路** -建立 VM 時，請選取虛擬網路 (VNET)。此欄位提供與訂用帳戶相關聯的所有 VNET。顯示的 VM 資源群組會以括號括住。 
    - **子網路** - 如果 VNET 有子網路，預設會選取第一個子網路。如果有其他子網路，請選取所需的子網路。
    - **儲存體帳戶** - 開啟與復原服務保存庫位於相同位置的儲存體帳戶清單。在選擇儲存體帳戶時，您必須選擇與復原服務保存庫共用相同位置的帳戶。不支援區域備援的儲存體帳戶。如果沒有與復原服務保存庫相同位置的儲存體帳戶，您必須在開始還原作業之前，建立一個儲存體帳戶。儲存體帳戶的複寫類型會在括號中註明。 
    
    > [AZURE.NOTE] 您必須在還原 ARM 架構的 VM 時選取 VNET。對傳統 VM 而言，VNET 是選擇性的。

3. 在 [還原組態] 刀鋒視窗上，按一下 [確定] 完成還原組態。

4. 在 [還原] 刀鋒視窗上，按一下 [還原] 以觸發還原作業。

    ![已完成復原設定](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## 追蹤還原作業

一旦觸發還原作業，備份服務會建立一個作業以便追蹤還原作業。備份服務也會建立，並會在入口網站的 [通知] 區域暫時顯示通知。如果看不到通知，您隨時可以按一下 [通知] 圖示來檢視您的通知。

![已觸發還原](./media/backup-azure-arm-restore-vms/restore-notification.png)

若要檢視正在處理的作業，或檢視完成的作業，請開啟 [備份作業] 清單。

1. 在 Azure 功能表上按一下 [瀏覽]，然後在服務清單中輸入**復原服務**。服務清單會調整成您輸入的內容。當您看到 [復原服務保存庫] 時，請選取它。

    ![開啟復原服務保存庫](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    便會顯示訂用帳戶中保存庫的清單。

    ![復原服務保存庫清單](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. 從這份清單，選取與您還原的 VM 相關聯的保存庫。當您按一下保存庫時，其儀表板隨即開啟。

3. 在保存庫儀表板中的 [備份作業] 圖格上，按一下 [Azure 虛擬機器] 以顯示與保存庫相關聯的作業。

    ![保存庫儀表板](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    [備份作業] 刀鋒視窗會開啟並顯示作業的清單。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)

## 還原具有特殊網路組態的 VM
可以備份和還原具有下列特殊網路組態的 VM。不過，進行還原程序時，這些組態需要一些特殊的考量。

- 負載平衡器底下的 VM (內部與外部)
- 具有多個保留的 IP 的 VM
- 具有多個 NIC 的 VM

>[AZURE.IMPORTANT] 建立 VM 的特殊網路組態時，您必須使用 PowerShell 從還原的磁碟建立 VM。

為了在還原至磁碟後，完全重新建立虛擬機器，請依照以下步驟執行：

1. 使用 [PowerShell](backup-azure-vms-automation.md/#restore-an-azure-vm) 從復原服務保存庫還原磁碟

2. 使用 PowerShell Cmdlet 建立負載平衡器/多個 NIC/多個保留的 IP 所需的 VM 組態，並使用該組態建立具備想要之組態的 VM。
	- 使用[內部負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在雲端服務中建立 VM
	- 建立 VM 來連線至[網際網路對向負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)
	- 建立具有[多個 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/) 的 VM
	- 建立具有[多個保留的 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/) 的 VM

## 後續步驟
您現在可以還原您的 VM，請參閱疑難排解文章中有關 VM 常見錯誤的資訊。此外，請參閱有關您的 VM 管理工作的文章。

- [針對錯誤進行疑難排解](backup-azure-vms-troubleshoot.md#restore)
- [管理虛擬機器](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0518_2016-->