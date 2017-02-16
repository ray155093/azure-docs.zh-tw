---
title: "將 Azure VM 備份到復原服務保存庫 | Microsoft Docs"
description: "探索、註冊及備份 Azure 虛擬機器到復原服務保存庫。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "虛擬機器備份; 備份虛擬機器; 備份和災害復原; arm vm 備份"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 39147f2db1e660a21d6ed622206787ea0c569056
ms.openlocfilehash: 28a5014f7ee73b30f879d249811e7fc303b13ac6


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>將 Azure VM 備份到復原服務保存庫
> [!div class="op_single_selector"]
> * [將 VM 備份到復原服務保存庫](backup-azure-arm-vms.md)
> * [將 VM 備份到備份保存庫](backup-azure-vms.md)
>
>

本文將詳細說明如何將 Azure VM (包括以 Resource Manager 部署和傳統部署的 VM) 備份至復原服務保存庫。 備份 VM 的工作多數是準備。 在備份或保護 VM 之前，您必須完成 [必要條件](backup-azure-arm-vms-prepare.md) 來備妥 VM 的保護環境。 完成必要條件後，您就能初始備份作業來製作 VM 的快照集。


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

如需詳細資訊，請參閱[在 Azure 中規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)和 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)的文章。

## <a name="triggering-the-backup-job"></a>觸發備份作業
與復原服務保存庫相關聯的備份原則會定義備份作業的執行頻率和時間。 根據預設，第一個排定的備份是初始備份。 在執行初始備份之前，[備份作業] 刀鋒視窗上的 [上次備份狀態] 會顯示為 [警告 (待執行初始備份)]。

![待備份](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

除非您的初始備份預計會馬上開始，否則建議您執行 [立即備份] 。 以下程序從保存庫儀表板開始。 當您完成所有必要條件後，此程序可用來執行初始備份作業。 如果您已執行過初始備份作業，此程序便不適用。 相關聯的備份原則決定後續的備份作業。  

若要執行初始備份作業：

1. 在保存庫儀表板的 [備份] 圖格上，按一下 [Azure 虛擬機器]。 <br/>
    ![設定圖示](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    [備份項目]  刀鋒視窗隨即開啟。
2. 在 [備份項目] 刀鋒視窗中，以滑鼠右鍵按一下您要備份的保存庫，然後按一下 [立即備份]。

    ![設定圖示](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    備份作業便會觸發。 <br/>

    ![備份作業已觸發](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. 若要檢視初始備份是否已完成，請在保存庫儀表板的 [備份作業] 圖格上按一下 [Azure 虛擬機器]。

    ![備份工作圖格](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    [備份工作] 刀鋒視窗隨即開啟。
4. 在 [備份作業]  刀鋒視窗中，您可以看到所有作業的狀態。

    ![備份工作圖格](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > 備份作業期間，每部虛擬機器中的備份擴充功能會排清所有寫入並採用一致的快照集。
   >
   >

    當備份作業完成時，狀態會是「完成」 。

## <a name="troubleshooting-errors"></a>錯誤疑難排解
如果您在備份虛擬機器時遇到問題，請參閱 [VM 疑難排解文章](backup-azure-vms-troubleshoot.md)以取得說明。

## <a name="next-steps"></a>後續步驟
現在您已能保護您的 VM，請參閱下列文章，以了解 VM 管理工作及如何還原 VM。

* [管理和監視虛擬機器](backup-azure-manage-vms.md)
* [還原虛擬機器](backup-azure-arm-restore-vms.md)



<!--HONumber=Jan17_HO5-->


