---
title: "使用 Azure 備份來備份和還原已加密的 VM"
description: "本文討論使用「Azure 磁碟加密」加密之 VM 的備份與還原經驗。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/18/2017
ms.author: markgal; jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 152285bee4631acca7610eb5bd9167ec37296a26
ms.openlocfilehash: d1ec8112b9fc347d9de5901c4b39a3291701da45


---
# <a name="backup-and-restore-encrypted-vms-using-azure-backup"></a>使用 Azure 備份來備份和還原已加密的 VM
本文討論使用 Azure 備份來備份和還原虛擬機器的步驟。 它也提供有關支援的案例、必要條件的詳細資料，以及的錯誤案例的疑難排解步驟。

## <a name="supported-scenarios"></a>支援的案例
> [!NOTE]
> 1. 只有 Resource Manager 部署的虛擬機器支援備份及還原加密的 VM。 傳統虛擬機器不提供支援。 <br>
> 2. 使用 Azure 磁碟加密的 Windows 和 Linux 虛擬機器均可提供支援，Azure 磁碟加密會運用業界標準的 Windows BitLocker 功能與 Linux 的 DM-Crypt 功能來提供磁碟加密。 <br>
> 3. 只有使用「BitLocker 加密金鑰」和「金鑰加密金鑰」加密的虛擬機器才提供支援。 只使用「BitLocker 加密金鑰」加密的虛擬機器不提供支援。 <br>
> 
> 

## <a name="pre-requisites"></a>必要條件
1. 虛擬機器已使用 [Azure 磁碟加密](../security/azure-security-disk-encryption.md)進行加密。 應使用「BitLocker 加密金鑰」和「金鑰加密金鑰」進行加密。
2. 已建立復原服務保存庫，並使用[準備環境以便備份](backup-azure-arm-vms-prepare.md)一文所述的步驟設定儲存體複寫。

## <a name="backup-encrypted-vm"></a>備份加密的 VM
使用下列步驟來設定備份目標、定義原則、設定項目和觸發備份。

### <a name="configure-backup"></a>設定備份
1. 如果您已開啟復原服務保存庫，請繼續下一個步驟。 如果您並未開啟復原服務保存庫，但位於 Azure 入口網站中，請在 [中樞] 功能表上按一下 [瀏覽] 。
   
   * 在資源清單中輸入 **復原服務**。
   * 當您開始輸入時，清單會根據您輸入的文字進行篩選。 當您看到 [復原服務保存庫] 時，請按一下它。
     
      ![建立復原服務保存庫的步驟 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>
     
     隨即會出現 [復原服務保存庫] 清單。 在 [復原服務保存庫] 清單中選取保存庫。
     
     選取的保存庫儀表板隨即開啟。
2. 從出現在保存庫下方的項目清單中，按一下 [備份] 以開啟 [備份] 刀鋒視窗。
   
      ![開啟 [備份] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup.png) 
3. 在 [備份] 刀鋒視窗中，按一下 [備份目標]  以開啟 [備份目標] 刀鋒視窗。
   
      ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-one.png) 
4. 在 [備份目標] 刀鋒視窗中，將 [工作負載的執行位置] 設定為 [Azure]，並將 [欲備份的項目] 設定為 [虛擬機器]，然後按一下 [確定]。
   
   [備份目標] 刀鋒視窗隨即關閉，然後開啟 [備份原則] 刀鋒視窗。
   
   ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-two.png) 
5. 在 [備份原則] 刀鋒視窗中選取您要套用至保存庫的備份原則，然後按一下 [確定] 。
   
      ![選取備份原則](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png) 
   
    預設原則的詳細資料便會列在詳細資料中。 如果您想要建立原則，請在下拉式功能表中選取 [建立新的]  。 一旦您按下 [確定] ，備份原則便會與保存庫建立關聯。
   
    接下來選擇要與保存庫建立關聯的 VM。
6. 選擇要與指定的原則建立關聯的已加密虛擬機器，然後按一下 [確定]。
   
      ![選取加密的 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. 此頁面會顯示與所選加密的 VM 相關聯的金鑰保存庫相關訊息。 備份服務需要金鑰保存庫中金鑰和密碼的唯讀存取權。 它會使用這些權限來備份金鑰和密碼，以及相關聯的 VM。 
   
      ![加密的 VM 訊息](./media/backup-azure-vms-encryption/encrypted-vm-message.png)
   
      現在您已定義保存庫的所有設定，接下來在 [備份] 刀鋒視窗中按一下頁面底部的 [啟用備份]。 [啟用備份] 可將原則部署到保存庫和 VM。
8. 下一個階段的準備作業是安裝 VM 代理程式，或確定 VM 代理程式已安裝。 若要執行相同的動作，請使用[準備環境以便備份](backup-azure-arm-vms-prepare.md)一文中所述的步驟。 

### <a name="triggering-backup-job"></a>觸發備份作業
使用[將 Azure VM 備份至復原服務保存庫](backup-azure-arm-vms.md)一文中所述的步驟來觸發備份作業。

## <a name="restore-encrypted-vm"></a>還原已加密的 VM
若要還原加密的 VM，請先使用[選擇 VM 還原組態](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的＜還原備份的磁碟＞一節中所述的步驟來還原磁碟。 之後，使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 中所述的 PowerShell 步驟，從還原的磁碟建立完整的 VM。

## <a name="troubleshooting-errors"></a>錯誤疑難排解
| 作業 | 錯誤詳細資料 | 解決方案 |
| --- | --- | --- |
| 備份 |驗證失敗，因為虛擬機器單獨以 BEK 進行加密。 只會對同時使用 BEK 和 KEK 加密的虛擬機器啟用備份。 |應該使用 BEK 和 KEK 來加密虛擬機器。 之後，應該啟用備份。 |
| 還原 |您無法還原這部已加密的 VM，因為與此 VM 相關聯的金鑰保存庫不存在。 |利用[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)，建立金鑰保存庫。 請參閱[使用 Azure 備份來還原金鑰保存庫金鑰和密碼](backup-azure-restore-key-secret.md)文章來還原金鑰和密碼 (如果不存在)。 |
| 還原 |您無法還原這部已加密的 VM，因為與此 VM 相關聯的金鑰和密碼不存在。 |請參閱[使用 Azure 備份來還原金鑰保存庫金鑰和密碼](backup-azure-restore-key-secret.md)文章來還原金鑰和密碼 (如果不存在)。 |
| 還原 |備份服務無權存取您訂用帳戶中的資源。 |如前所述，請先使用[選擇 VM 還原組態](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的＜還原備份的磁碟＞一節中所述的步驟來還原磁碟。 之後，使用 PowerShell 來[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。 




<!--HONumber=Jan17_HO3-->


