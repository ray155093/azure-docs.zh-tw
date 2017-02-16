---
title: "將 Azure 中的資料還原至 Windows Server 或 Windows 電腦 | Microsoft Docs"
description: "了解如何將儲存於 Azure 中的資料還原至 Windows Server 或 Windows 電腦。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: bb07ff13e3e4023e334c706d5f9bd95196ee6051
ms.openlocfilehash: 47c5789fc3379b702d8f6ceafd630bece669b11e


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>使用 Resource Manager 部署模型將檔案還原到 Windows Server 或 Windows 用戶端電腦
> [!div class="op_single_selector"]
> * [Azure 入口網站](backup-azure-restore-windows-server.md)
> * [傳統入口網站](backup-azure-restore-windows-server-classic.md)
>
>

本文涵蓋執行兩種類型還原作業所需的步驟：

* 將資料還原到進行備份的相同電腦。
* 將資料還原到任何其他電腦。

這兩種情況之下，都會從 Azure 復原服務保存庫擷取資料。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-restore-data-to-the-same-machine"></a>使用「立即還原」將資料還原至同一台電腦
如果您不小心刪除檔案，而您想要將它還原到相同的電腦 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟 **Microsoft Azure 備份** 嵌入式管理單元。 如果您不知道快照安裝的位置，請在電腦或伺服器上搜尋 **Microsoft Azure 備份**。

    傳統型應用程式應該會出現在搜尋結果中。

2. 按一下 [復原資料] 以啟動精靈。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

3. 在 [開始使用] 窗格中，若要將資料還原至同一台伺服器或電腦，請選取 [這台伺服器 (`<server name>`)] 並按一下 [下一步]。

    ![選擇 [這台伺服器] 選項以將資料還原到同一台電腦](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. 在 [選取復原模式] 窗格中，選擇要還原：
    - **個別檔案和資料夾**：如果您要還原特定檔案和資料夾，請使用此選項。
    - **磁碟區**：使用此選項還原選取磁碟區內的所有檔案和資料夾。

    然後按一下 [下一步]。

    ![瀏覽檔案](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. 在 [選取磁碟區和日期] 窗格上，選取包含您要還原之檔案和/或資料夾的磁碟區。

    在日曆上，選取復原點。 您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 一旦您選取一個日期，如果有多個復原點可用，您可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![磁碟區和日期](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 選擇要還原的復原點之後，按一下 [掛接]。

    Azure 備份會掛接本機復原點，並且使用它做為復原磁碟區。

7. 在 [瀏覽及復原檔案] 窗格上，按一下 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![修復選項](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

   * 您可以選擇還原至原始位置 (其中的檔案/資料夾可能會遭到覆寫) 或相同電腦中的其他位置。
   * 如果目標位置有您想要還原的檔案/資料夾存在，您可以建立複本 (相同檔案的兩個版本)、覆寫目標位置中的檔案，或略過修復目標位置已存在的檔案。
   * 強烈建議您針對正在復原檔案上的 ACL 保留預設還原選項。
8. 在 [Windows 檔案總管] 中，複製要還原的檔案和/或資料夾，並將它們貼上至伺服器或電腦的任意本機位置。 您可以直接從復原磁碟區開啟或串流檔案，並確認是否復原正確的版本。

    ![複製掛接磁碟區的檔案和資料夾，並貼上至本機位置](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. 當您完成還原檔案和/或資料夾後，請在 [瀏覽及復原檔案] 窗格上，按一下 [卸載]。 按一下 [是] 以確認要卸載磁碟區。

    ![卸載磁碟區並確認](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接六個小時 (從掛接後開始計算)。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>使用「立即還原」將資料還原至其他電腦
若您遺失整個伺服器，您仍然可從 Azure 備份將資料還原到其他電腦。 下列步驟說明工作流程。

> [!NOTE]
> 如果您要使用「立即還原」將資料還原到其他電腦，必須要有 [2017 年 1 月 Azure 備份更新](https://support.microsoft.com/en-us/help/3216528?preview)。 另外，您也必須在支援文章列出之地區設定的保存庫中，保護備份資料。 「立即還原」目前無法在所有地區設定中使用。
>

這些步驟中所使用的術語包含：

*  – 用來進行備份且目前無法使用的的原始電腦。
*  – 復原資料時的目標電腦。
* 「範例保存庫」–「來源電腦」和「目標電腦」註冊的復原服務保存庫。 <br/>

> [!NOTE]
> 備份無法還原到執行舊版作業系統的目標電腦。 例如，從 Windows 7 電腦建立的備份可以還原至 Windows 8 或更新版本的電腦。 從 Windows 8 電腦建立的備份無法還原至 Windows 7 電腦。
>
>

1. 在「目標電腦」上開啟 [Microsoft Azure 備份] 嵌入式管理單元。

2. 確定 [目標電腦] 和 [來源電腦] 已註冊到同一個復原服務保存庫。

3. 按一下 [復原資料] 以開啟 [復原資料精靈]。

    ![復原資料](./media/backup-azure-restore-windows-server/recover.png)

4. 在 [開始使用] 窗格上，選取 [其他伺服器]

    ![其他伺服器](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. 提供與「範例保存庫」相對應的保存庫認證檔，然後按一下 [下一步]。

    如果保存庫認證檔無效 (或已過期)，請從 Azure 入口網站中的「範例保存庫」下載新的保存庫認證檔。 一旦您提供有效的保存庫認證檔之後，就會顯示對應的備份保存庫名稱。

6. 在 [選取備份伺服器] 窗格上，從顯示的電腦清單選取 [來源電腦]，並提供複雜密碼。 然後按 [下一步] 。

    ![電腦清單](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. 在 [選取復原模式] 窗格上，選取 [個別檔案和資料夾] 並按一下 [下一步]。

    ![搜尋](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. 在 [選取磁碟區和日期] 窗格上，選取包含您要還原之檔案和/或資料夾的磁碟區。

    在日曆上，選取復原點。 您可以從任何時間的復原點還原。 **粗體**的日期表示至少有一個復原點可用。 一旦您選取一個日期，如果有多個復原點可用，您可以從 [時間] 下拉式功能表選擇特定的復原點。

    ![搜尋項目](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. 按一下 [掛接] 以掛接本機的復原點，做為 [目標電腦] 的復原磁碟區。

10. 在 [瀏覽及復原檔案] 窗格上，按一下 [瀏覽] 以開啟 [Windows 檔案總管]，然後尋找所需的檔案和資料夾。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. 在 [Windows 檔案總管] 中，從復原磁碟區複製檔案和/或資料夾，然後貼上至 [目標電腦] 位置。 您可以直接從復原磁碟區開啟或串流檔案，並確認是否復原正確的版本。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. 當您完成還原檔案和/或資料夾後，請在 [瀏覽及復原檔案] 窗格上，按一下 [卸載]。 按一下 [是] 以確認要卸載磁碟區。

    ![加密](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > 如果您並未按一下 [卸載]，復原磁碟區會保持掛接六個小時 (從掛接後開始計算)。 當磁碟區處於掛接狀態時，不會執行任何備份作業。 當掛接磁碟區時，任何排定要執行的備份作業會在復原磁碟區卸載之後才執行。
    >

## <a name="next-steps"></a>後續步驟
* 現在您已復原檔案和資料夾，接下來您可以 [管理您的備份](backup-azure-manage-windows-server.md)。



<!--HONumber=Jan17_HO5-->


