---
title: "Azure 備份︰從 Azure VM 備份復原檔案和資料夾 | Microsoft Docs"
description: "從 Azure 虛擬機器的復原點復原檔案"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "項目層級復原；從 Azure VM 備份檔案復原；從 Azure VM 中還原檔案"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>從 Azure 虛擬機器備份復原檔案 (預覽)

Azure 備份能夠讓您從 Azure VM 備份還原 [Azure VM 和磁碟](./backup-azure-arm-restore-vms.md)。 現在本文說明如何從 Azure VM 備份復原檔案和資料夾等項目。

> [!Note]
> 這項功能可使用 Resource Manager 模式供 Azure VM 部署使用，而且受保護復原服務保存庫。
> 目前，從 Windows Azure VM 備份的檔案復原為預覽狀態。 從 Linux Azure VM 的檔案復原即將推出。
目前不支援從加密 VM 備份的檔案復原。
>

## <a name="mount-the-volume-and-copy-files"></a>掛接磁碟區和複製檔案

1. 登入 [Azure 入口網站](http://portal.Azure.com)。 尋找相關的復原服務保存庫和必要的備份項目。

2. 在 [備份項目] 刀鋒視窗中，按一下 [檔案修復 (預覽)]

    ![開啟復原服務保存庫備份項目](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    [檔案復原] 刀鋒視窗隨即開啟。

    ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. 從 [選取復原點] 下拉式選單中，選取包含您所需檔案的復原點。 根據預設，已選取最近的復原點。

4. 按一下 [下載可執行檔] 下載您要用來從復原點複製檔案的軟體。

  可執行檔是在本機電腦和指定復原點之間建立連接的指令碼。

5. 在您要復原檔案的電腦上，執行可執行檔。 您必須以系統管理員認證來執行指令碼。 如果您在具有限制存取的電腦上執行指令碼，請確定可存取︰

    - go.microsoft.com
    - 用於 Azure VM 備份的 Azure 端點
    - 輸出連接埠 3260

    ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/executable-output.png)

    您可以在任何具有與用來產生復原點的電腦相同 (或相容) 作業系統的電腦上執行指令碼。 請參閱[相容作業系統資料表](backup-azure-restore-files-from-vm.md#compatible-os)以查看相容的作業系統。 如果受保護的 Azure 虛擬機器使用 Windows 儲存空間，則您無法在此虛擬機器上執行可執行的指令碼。 相反地，在使用 Windows 儲存空間的任何其他電腦上執行可執行的指令碼。 建議您在具有相容作業系統的電腦上執行可執行的指令碼。

    ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>相容的作業系統

下表顯示伺服器和電腦作業系統之間的相容性。 當復原檔案時，您無法還原不相容作業系統之間的檔案。

|伺服器作業系統 | 相容的用戶端作業系統  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>磁碟機代號指派

當您執行指令碼時，作業系統會掛接新磁碟區，並指派磁碟機代號。 您可以使用 Windows 檔案總管或檔案總管來瀏覽這些磁碟機。 指派給磁碟區的磁碟機代號可能與原始虛擬機器為不同的代號，不過，系統會保留磁碟區名稱。 例如，如果原始虛擬機器上的磁碟區是 “資料磁碟機 (E:\)”，則該磁碟區可以在本機電腦上附加做為 “資料磁碟機 ('任何可用的磁碟機代號':\)。 瀏覽指令碼輸出中所提及的所有磁碟區，直到找出您的檔案/資料夾。  


## <a name="closing-the-connection"></a>關閉連線

在識別檔案並將它們複製到本機儲存體位置之後，移除 (或卸載) 其他磁碟機。 若要卸載磁碟機，在 Azure 入口網站的 [檔案復原] 刀鋒視窗中，按一下 [取消掛接磁碟]。

![取消掛接磁碟](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

一旦卸載磁碟後，您會收到一則訊息，告知您已順利完成。 連線可能需要幾分鐘重新整理，以讓您可以移除磁碟。

## <a name="windows-storage-spaces"></a>Windows 儲存空間

Windows 儲存空間是 Windows 儲存體中的一種技術，可讓您虛擬化儲存體。 您可以利用 Windows 儲存空間將工業標準磁碟群組為儲存體集區，然後從這些儲存體集區中的可用空間建立虛擬磁碟，稱為儲存空間。

如果備份的 Azure VM 使用 Windows 儲存空間，則您無法在此虛擬機器上執行可執行的指令碼。 相反地，在使用 Windows 儲存空間的任何其他電腦上執行可執行的指令碼。 建議您在具有相容作業系統的電腦上執行可執行的指令碼。

## <a name="troubleshooting"></a>疑難排解

如果您從虛擬機器復原檔案時遇到問題，請檢查下表中的其他資訊。

| 錯誤訊息 / 案例 | 可能的原因 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Exe 輸出︰連線到目標的例外狀況 |指令碼無法存取復原點    | 檢查電腦是否符合上述的存取需求|  
|    Exe 輸出︰目標已透過 ISCSI 工作階段登入。 |    指令碼已在相同電腦上執行，並已附加磁碟機 |    復原點磁碟區已連接。 它們可能未使用原始 VM 的相同磁碟機代號裝載。 在檔案總管中的檔案瀏覽所有可用的磁碟區 |
| Exe 輸出︰這個指令碼無效，因為磁碟已透過入口網站/超過 12 小時限制卸載。請從入口網站下載新的指令碼。 |    已從入口網站卸載磁碟或超過 12 小時的限制 |    此特定 exe 目前無效，無法執行。 如果您想要存取該復原點時間的檔案，請瀏覽新 exe 的入口網站|
| 在執行 exe 的電腦上︰按一下 [卸載] 按鈕之後不會卸載新的磁碟區 |    電腦上的 ISCSI 啟動器沒有回應/重新整理其與目標之連接及維護快取 |    按下 [卸載] 按鈕後等候幾分鐘。 如果仍無法卸載新磁碟區，請瀏覽的所有磁碟區。 這會強制啟動器重新整理連接，且會卸載磁碟區，並出現磁碟無法使用的錯誤訊息|
| Exe 輸出︰指令碼成功執行，但指令碼輸出上不會顯示「附加的新磁碟區」 |    這是暫時性的錯誤    | 磁碟區可能已經附加。 開啟檔案總管以瀏覽。 如果您每次都使用同一部電腦執行指令碼，請考慮重新啟動電腦，清單應該會顯示在後續的 exe 執行。 |



<!--HONumber=Feb17_HO1-->


