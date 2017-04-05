---
title: "針對 Azure 備份伺服器進行疑難排解 | Microsoft Docs"
description: "針對安裝、註冊 Azure 備份伺服器以及備份和還原應用程式工作負載進行疑難排解"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/24/2017
ms.author: pullabhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a42488d618c58b36fa8105c1b22fd32ca615d1b1
ms.lasthandoff: 03/27/2017


---

# <a name="troubleshoot-azure-backup-server"></a>針對 Azure 備份伺服器進行疑難排解

您可以針對使用 Azure 備份伺服器搭配下表列出的資訊時所發生的錯誤進行疑難排解。

>
>

## <a name="registration-and-agent-related-issues"></a>註冊與代理程式相關問題
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 註冊至保存庫 | 提供的保存庫認證無效。 檔案可能損毀或沒有復原服務所關聯的最新認證 | 若要修正此錯誤： <ol><li> 從保存庫下載最新的認證檔案，然後再試一次 <br>(或)</li> <li> 如果上述動作沒有用，請嘗試將認證下載至不同的本機目錄，或建立新的保存庫 <br>(或)</li> <li> 如[這個部落格](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)所述嘗試更新日期和時間設定 <br>(或)</li> <li> 檢查 c:\windows\temp 是否有超過 65000 個檔案。 將過時檔案移至另一個位置，或刪除暫存資料夾中的項目 <br>(或)</li> <li> 檢查憑證的狀態 <br> a. 開啟 [管理電腦憑證] (在控制台中) <br> b. 展開 [個人] 節點和它的子節點 [憑證] <br> c.  移除 [Windows Azure 工具] 憑證 <br> d. 在 Azure 備份用戶端中重試註冊 <br> (或) </li> <li> 檢查是否已備有任何群組原則 </li></ol> |
| 將代理程式推送至受保護的伺服器 | 代理程式作業因為 \<ServerName> 上的 DPM 代理程式協調員服務發生通訊錯誤而失敗 | **如果產品所示的建議動作沒有用**， <ol><li> 如果您要連結來自不受信任網域的電腦，請遵循[這些](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)步驟 <br> (或) </li><li> 如果您要連結來自受信任網域的電腦，請使用[這個部落格](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)中概述的步驟進行疑難排解 <br>(或)</li><li> 嘗試停用防毒來作為疑難排解步驟。 如果這能解決問題，請修改 [這篇文章] (https://technet.microsoft.com/library/hh757911.aspx) 中建議的防毒設定</li></ol> |
| 將代理程式推送至受保護的伺服器 | 為伺服器指定的認證無效 | **如果產品所示的建議動作沒有用**， <br> 嘗試手動將保護代理程式安裝在[這篇文章](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)所指定的生產伺服器上|


## <a name="configuring-protection-group"></a>設定保護群組
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 設定保護群組 | DPM 無法列舉受保護電腦 (受保護的電腦名稱) 上的應用程式元件 | 在相關的資料來源/元件層級按一下設定保護群組 UI 畫面上的 [重新整理] |
| 設定保護群組 | 無法設定保護 | 如果受保護的伺服器是 SQL Server，請檢查是否以將 sysadmin 角色權限提供給[這篇文章](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)中所述之受保護電腦上的系統帳戶 (NTAuthority\System)
| 設定保護群組 | 此保護群組在儲存體集區中沒有足夠的可用空間 | 新增至儲存體集區的磁碟[不應包含磁碟分割](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx)。 刪除磁碟上的任何現有磁碟區，然後將它新增到儲存體集區|

## <a name="backup"></a>備份
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 備份 | 複本不一致 | 您可以在[這裡](https://technet.microsoft.com/library/cc161593.aspx)找到更多關於複本不一致原因和相關建議的詳細資料 <br> <ol><li> 如果是系統狀態/BMR 備份，請檢查受保護伺服器上是否已安裝 Windows Server Backup </li><li> 檢查 DPM/MABS 伺服器上 DPM 儲存體集區的空間相關問題，並視需要配置儲存體 </li><li> 檢查受保護伺服器上的磁碟區陰影複製服務狀態。 如果它是停用狀態，請將它設定為手動啟動，並在伺服器上啟動服務。 然後返回 DPM/MABS 主控台，並開始一致性檢查作業的同步處理。</li></ol>|
| 備份 | 執行作業時發生非預期的錯誤，裝置未就緒 | **如果產品所示的建議動作沒有用**， <br> <ol><li>針對保護群組中的項目，將陰影複製儲存空間設定為無限，並執行一致性檢查 <br></li> (或) <li>嘗試刪除現有保護群組，並建立多個新的保護群組，每個保護群組中各有一個個別項目</li></ol> |
| 備份 | 如果您只要備份系統狀態，請確認受保護的電腦上是否有足夠的可用空間可儲存系統狀態備份 | <ol><li>確認受保護的電腦上已安裝 WSB</li><li>確認受保護的電腦上有足夠空間可存放系統狀態︰最簡單的執行方法是移到受保護的電腦上、開啟 WSB、逐一點選選取項目，然後選取 BMR。 然後，UI 會告訴您這需要多少空間。 開啟 WSB -> 本機備份 -> 備份排程 -> 選取備份設定 -> 完整伺服器 (大小會顯示出來)。 使用此大小進行驗證。</li></ol>
| 備份 | 線上復原點建立失敗 | 如果錯誤訊息指出「Windows Azure Backup Agent 無法建立所選磁碟區的快照集」，請嘗試增加複本和復原點磁碟區中的空間。
| 備份 | 線上復原點建立失敗 | 如果錯誤訊息指出「未設定此伺服器的加密複雜密碼。 請設定加密複雜密碼」，請嘗試設定加密複雜密碼。 如果作業失敗， <br> <ol><li>請檢查暫存位置是否存在。 名稱為 “ScratchLocation” 之登錄 HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config 中所提到的位置應該要存在。</li><li> 如果暫存位置存在，請嘗試使用舊的複雜密碼重新註冊。 **每當您設定加密複雜密碼時，請將它儲存在安全的位置**</li><ol>
| 備份 | BMR 的備份失敗 | 如果 BMR 大小很大，請將一些應用程式檔案移到作業系統磁碟機後再重試 |
| 備份 | 存取檔案/共用資料夾時發生錯誤 | 請嘗試依[這裡](https://technet.microsoft.com/library/hh757911.aspx)的建議修改防毒設定|

