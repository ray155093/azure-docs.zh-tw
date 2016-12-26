---
title: "從備份保存庫的另一部 Azure 備份伺服器復原資料 | Microsoft Docs"
description: "從登錄至 Azure 備份保存庫的任何 Azure 備份伺服器，復原該保存庫中保護的資料。"
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: giridham;jimpark;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: b5b18d063a5926ad4acb7d0aa3935978d0fedb8c
ms.openlocfilehash: 35f3689760adbf2a26b6c2d396fb4a24f36a5806


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>從備份保存庫的另一部 Azure 備份伺服器復原資料
您現在可以從登錄至 Azure 備份保存庫的任何 Azure 備份伺服器，復原該保存庫中保護的資料。 執行此工作的流程已完全整合至 ABS 管理主控台，且與其他復原工作流程類似。

> [!NOTE] 
> 這篇文章和下列步驟也適用於 [System Center Data Protection Manager UR7] (https://support.microsoft.com/en-us/kb/3065246) 與[最新版的 Azure Backup Agent](http://aka.ms/azurebackup_agent)。
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>從另一部 Azure 備份伺服器復原資料
若要從另一部 Azure 備份伺服器復原資料：

1. 在 ABS 管理主控台的 [復原] 索引標籤中，按一下 [新增外部 DPM] (在畫面左上方)。   
    ![新增外部 DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. 從與要復原資料的 **Azure 備份伺服器**關聯的保存庫下載新的**保存庫認證**、從已向備份保存庫登錄的 Azure 備份伺服器清單中選擇 Azure 備份伺服器，並提供與要復原資料的伺服器關聯的**加密複雜密碼**。
   
    ![外部 DPM 認證](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)
   
   > [!NOTE]
   > 只有與相同登錄保存庫相關聯的 Azure 備份伺服器，才可以復原彼此的資料。
   > 
   > 
   
    順利新增外部 Azure 備份伺服器之後，您就可以從 [復原] 索引標籤瀏覽外部伺服器和本機 Azure 備份伺服器的資料。
3. 瀏覽由外部 Azure 備份伺服器保護的實際執行伺服器可用清單，並選取適當的資料來源。
   
    ![瀏覽外部 DPM 伺服器](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. 從 [復原點] 下拉式清單中選取「月份和年份」，選取代表復原點何時建立的必要 [復原日期]，並選取 [復原時間]。
   
    檔案和資料夾清單會出現在底部窗格中，方便您瀏覽並復原到任何位置。
   
    ![外部 DPM 伺服器復原點](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 在適當的項目上按一下滑鼠右鍵，然後按一下 [復原] 。
   
    ![外部 DPM 復原](./media/backup-azure-alternate-dpm-server/recover.png)
6. 檢閱「復原選取項目」 。 請確認要復原之備份複本的資料和時間，以及建立備份複本的來源。 如果選取項目不正確，請按一下 [取消]  ，往回瀏覽至 [復原] 索引標籤，以選取適當的復原點。 如果選取項目正確，請按 [下一步] 。
   
    ![外部 DPM 復原摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. 選取 [復原到替代位置] 。  到正確的復原位置。
   
    ![外部 DPM 復原替代位置](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. 選擇與 [建立複本]、[略過] 或 [覆寫] 相關的選項。
   
   *  會在名稱有衝突時，建立一份檔案的複本。
   *  會在名稱有衝突時，略過復原檔案。
   *  會在名稱有衝突時，覆寫指定位置中的現有複製。
     
     請選擇適當的選項來「還原安全性」 。 您可以在建立復原點時，針對將資料復原至其中的目的地電腦，套用其安全性設定，或是套用適用於產品的安全性設定。
     
     確認是否要在復原成功完成後傳送「通知」  。
     
     ![外部 DPM 復原通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. [摘要]  畫面會列出到目前為止已選擇的選項。 按一下 [復原] 之後，資料就會復原到適當的內部部署位置。
   
    ![外部 DPM 復原選項摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)
   
   > [!NOTE]
   > 您可以在 Azure 備份伺服器的 [監視] 索引標籤中監視復原作業。
   > 
   > 
   
    ![監視復原](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. 您可以在 DPM 伺服器的 [復原] 索引標籤上，按一下 [清除外部 DPM]，以移除外部 DPM 伺服器的檢視。
    
    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>疑難排解錯誤訊息
| 否。 | 錯誤訊息 | 疑難排解步驟 |
|:---:|:--- |:--- |
| 1. |保存庫認證所指定的保存庫中未登錄此伺服器。 |**原因：**如果選取的保存庫認證檔案不屬於與所嘗試復原之 Azure 備份伺服器關聯的備份保存庫，就會出現此錯誤。 <br> **解決方法：**從已登錄 Azure 備份伺服器的備份保存庫下載保存庫認證檔。 |
| 2. |可復原資料無法使用，或選取的伺服器不是 DPM 伺服器。 |**原因：**沒有任何其他 Azure 備份伺服器已向備份保存庫登錄，或伺服器尚未上傳中繼資料，或選取的伺服器不是 Azure 備份伺服器 (也稱為 Windows Server 或 Windows 用戶端)。 <br> **解決方法：**如果有其他已向備份保存庫登錄的 Azure 備份伺服器，請確定已安裝最新的 Azure Backup Agent。 <br>如果有其他 Azure 備份伺服器已向備份保存庫登錄，請在安裝後等候一天，再開始復原程序。 夜間作業會針對所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。 |
| 3. |此保存庫未登錄其他 DPM 伺服器。 |**原因︰**沒有任何其他 Azure 備份伺服器已向嘗試復原的保存庫登錄。<br>**解決方法：**如果有其他已向備份保存庫登錄的 Azure 備份伺服器，請確定已安裝最新的 Azure Backup Agent。<br>如果有其他 Azure 備份伺服器已向備份保存庫登錄，請在安裝後等候一天，再開始復原程序。 夜間作業會針對所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。 |
| 4. |提供的加密複雜密碼與下列伺服器關聯的複雜密碼不相符： **<server name>** |**原因：**在加密來自要復原之 Azure 備份伺服器的資料過程中使用的加密複雜密碼，與所提供的加密複雜密碼不符。 代理程式無法解密資料。 因此復原失敗。<br>**解決方法：**請提供與要復原資料的 Azure 備份伺服器關聯且完全相同的加密複雜密碼。 |

## <a name="frequently-asked-questions"></a>常見問題集：
1. **為什麼不能在安裝 UR7 和最新的 Azure Backup Agent 之後，從另一部 DPM 伺服器新增外部 DPM 伺服器？(適用於您使用 SC DPM 2012 R2 的情況)**
   
    答) 針對資料來源已在雲端受保護 (藉由使用比「更新彙總套件 7」舊的更新彙總套件進行保護) 的現有 DPM 伺服器，您必須在安裝 UR7 和最新的 Azure Backup Agent 之後至少等候一天，再開始「新增外部 DPM 伺服器」 。 這需要將 DPM 保護群組的中繼資料上傳至 Azure。 這項作業會在第一次夜間工作時進行。
2. **Azure Backup Agent 所需的最低版本為何？**
   
    答) 啟用這項功能的 Azure Backup Agent 最低版本為 2.0.8719.0。  您可以瀏覽至 [控制台] **>** [所有控制台項目] **>** [程式和功能] **>** [Microsoft Azure 復原服務代理程式]，以確認 Azure Backup Agent 版本。 如果版本低於 2.0.8719.0，請下載 [最新的 Azure Backup Agent](https://go.microsoft.com/fwLink/?LinkID=288905) 並進行安裝。
   
    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>後續步驟：
•    [Azure 備份常見問題集](backup-azure-backup-faq.md)




<!--HONumber=Nov16_HO5-->


