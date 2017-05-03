---
title: "Azure 備份：Linux VM 的應用程式一致備份 | Microsoft Docs"
description: "使用指令碼針對 Linux 虛擬機器保證應用程式會一致地備份到 Azure。 指令碼僅適用於資源管理員部署中的 Linux VM，不適用於 Windows VM 或服務管理員部署。 這篇文章會帶領您完成設定指令碼的步驟，包括疑難排解。"
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "應用程式一致備份; 應用程式一致 Azure VM 備份; Linux VM 備份; Azure 備份"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 4529037cb610e31028a35cf4643a2a99e90b2b8f
ms.lasthandoff: 04/21/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM 的應用程式一致備份 (預覽)

本文討論 Linux 前置和後置指令碼架構，以及如何使用它來取得 Azure Linux VM 的應用程式一致備份。

> [!Note]
> 僅支援資源管理員所部署 Linux 虛擬機器的前置和後置指令碼架構。 不支援服務管理員所部署虛擬機器或 Windows 虛擬機器的應用程式一致指令碼。
>

## <a name="how-the-framework-works"></a>架構的運作方式

架構提供一個選項可在取得 VM 快照集時執行自訂前置和後置指令碼。 前置指令碼就在取得 VM 快照集之前執行，而後置指令碼則是在 VM 快照集完成後立即執行。 這可在取得 VM 備份時提供使用者控制其應用程式和環境的彈性。

此架構的重要案例是確保應用程式一致的 VM 備份。 前置指令碼可以叫用應用程式原生 API 以停止 IO，並排清記憶體中的內容傳送到磁碟，如此可確保快照集是應用程式一致，也就是應用程式會在 VM 還原後開機時安裝。 後置指令碼可以使用應用程式原生 API 來解除凍結 IO，讓應用程式可在 VM 快照集後繼續正常作業。

## <a name="steps-to-configure-pre-script-and-post-script"></a>設定前指令碼和後置指令碼的步驟

1. 以根使用者身分登入要備份的 Linux VM。

2. 從 [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載 VMSnapshotScriptPluginConfig.json，並將它複製到所有要備份 VM 上的 /etc/azure 資料夾。 如果它尚未存在，請建立 /etc/azure 目錄。

3. 在所有要進行備份之 VM 上複製應用程式的前置指令碼和後置指令碼。 您可以將指令碼複製到 VM 內的任何位置，您必須更新 VMSnapshotScriptPluginConfig.json 檔案中指令碼檔案的完整路徑

4. 請確定下列檔案的權限︰

   - VMSnapshotScriptPluginConfig.json- 權限 “600” 也就是只有「根」使用者可擁有此檔案的「讀取」與「寫入」權限，使用者不應該有「執行」權限。
   - 前置指令碼檔案- 權限 “700” 也就是只有「根」使用者可擁有此檔案的「讀取」、「寫入」與「執行」權限。
   - 後置指令碼- 權限 “700” 也就是只有「根」使用者可擁有此檔案的「讀取」、「寫入」與「執行」權限。

   > [!Note]
   > 此架構會提供許多權限給使用者，所以完全安全非常重要，且應只有「根」使用者有權存取重要的 JSON 和指令碼檔案。
   > 如果萬一不符合上述需求，指令碼將不會執行，導致檔案系統/損毀一致備份。
   >

5. 根據下列詳細資料設定 VMSnapshotScriptPluginConfig.json
    - **pluginName**- 將此欄位保留原狀，無論您的指令碼是否可能無法如預期般運作。
    - **preScriptLocation**- 在要備份的 VM 上提供前置指令碼的完整路徑。
    - **postScriptLocation**- 在要備份的 VM 上提供後置指令碼的完整路徑。
    - **preScriptParams**- 需要傳遞至前置指令碼的任何選擇性參數，若有多個參數則所有的參數應該是以引號括住並以逗號分隔。
    - **postScriptParams**- 需要傳遞至後置指令碼的任何選擇性參數，若有多個參數則所有的參數應該是以引號括住並以逗號分隔。
    - **preScriptNoOfRetries**- 生任何錯誤時前置指令碼在終止之前應該重試的次數。 0 表示只嘗試一次，且系統故障時不能重試。
    - **postScriptNoOfRetries**- 生任何錯誤時後置指令碼在終止之前應該重試的次數。 0 表示只嘗試一次，且系統故障時不能重試。
    - **timeoutInSeconds**- 前置指令碼和後置指令碼的個別逾時。
    - **continueBackupOnFailure**- 如果您在前置或後置指令碼失敗時想要 Azure 備份改為使用檔案系統一致/損毀一致備份，請將這個值設定為 true。 指令碼失敗時，將此設定為 false 會使備份失敗 (除非在不論此設定皆會回到損毀一致備份的單一磁碟 VM)。
    - **fsFreezeEnabled**- 這會指定是否應該呼叫 Linux fsfreeze，同時取得 VM 快照集以確保檔案系統一致性。 我們建議您將這個保持為 true，除非您的應用程式在已停用的 fsfreeze 上具有相依性。

6. 現在已設定指令碼架構，如果已設定 VM 備份，下一次備份會叫用指令碼並觸發應用程式一致備份。 如果未設定 VM 備份，請使用[將 Azure 虛擬機器備份到復原服務保存庫](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)來設定。

## <a name="troubleshooting"></a>疑難排解

請確定您在寫入前置指令碼和後置指令碼時新增適當的記錄，並檢閱您的指令碼記錄以修正任何指令碼的問題。 如果仍有執行指令碼的問題，請參閱下列表格以取得詳細資訊。

| 錯誤 | 錯誤訊息 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |前置指令碼會傳回錯誤，所以備份可能無法應用程式一致。    | 請查看您指令碼的失敗記錄來修正此問題。|  
|    Post-ScriptExecutionFailed |    後置指令碼傳回的錯誤可能會影響應用程式狀態。 |    請查看您指令碼的失敗記錄並檢查應用程式狀態來修正此問題。 |
| Pre-ScriptNotFound |    VMSnapshotScriptPluginConfig.json 組態檔中指定的位置找不到前置指令碼。 |    請確定前置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| Post-ScriptNotFound |    VMSnapshotScriptPluginConfig.json 組態檔中指定的位置找不到後置指令碼 |    請確定後置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| IncorrectPluginhostFile |    VmSnapshotLinux 延伸模組隨附的 Pluginhost 檔案已損毀，因此無法執行前置指令碼和後置指令碼，且備份為應用程式不一致。    | 請解除安裝 VmSnapshotLinux 延伸模組，它會自動與下一次備份重新安裝以解決問題。 |
| IncorrectJSONConfigFile | VMSnapshotScriptPluginConfig.json 檔不正確，所以無法執行前置指令碼和後置指令碼，且備份為應用程式不一致 | 請從 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載副本並再設定一次 |
| InsufficientPermissionforPre-Script | 針對執行指令碼，根使用者應該是檔案擁有者，且檔案應具有 “700” 權限，也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| InsufficientPermissionforPost-Script | 針對執行指令碼，根使用者應該是檔案擁有者，且檔案應具有 “700” 權限，也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| Pre-ScriptTimeout | 執行應用程式一致備份前置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時。 |
| Post-ScriptTimeout | 執行應用程式一致備份後置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時。 |

## <a name="next-steps"></a>後續步驟
[設定 VM 備份以復原服務保存庫](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

