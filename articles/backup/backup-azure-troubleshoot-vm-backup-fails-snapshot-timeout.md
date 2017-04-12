---
title: "對 Azure 備份失敗進行疑難排解︰快照 VM 子工作逾時 | Microsoft Docs"
description: "與錯誤相關的 Azure 備份失敗的徵狀、原因及解決方案：無法與 VM 代理程式通訊以取得快照狀態 - 快照 VM 子工作逾時"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli;markgal;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7924d8aade1ea582faa0f319f8c1d16d5461fbc
ms.lasthandoff: 04/03/2017

---

# <a name="troubleshoot-azure-backup-failure-snapshot-vm-sub-task-timed-out"></a>對 Azure 備份失敗進行疑難排解︰快照 VM 子工作逾時
## <a name="summary"></a>摘要
在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 四種狀況的任一種都可能會阻止觸發快照，接著導致備份失敗。 本文提供疑難排解步驟，協助您解決與快照逾時錯誤相關的備份失敗。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>徵狀
適用於基礎結構即服務 (IaaS) VM 的 Azure 備份失敗，會在 [Azure 入口網站](https://portal.azure.com/)的作業錯誤詳細資料中傳回下列錯誤訊息：「無法與 VM 代理程式通訊以取得快照狀態 - 快照 VM 子工作逾時」。

## <a name="cause-1-the-vm-has-no-internet-access"></a>原因 1：VM 沒有網際網路存取權
根據部署需求，VM 沒有網際網路存取權，或是有既有限制，防止存取 Azure 基礎結構。

備份擴充功能需要連線到 Azure 公用 IP 位址，才能正確運作。 擴充功能會將命令傳送至 Azure 儲存體端點 (HTTP URL) 來管理 VM 的快照。 如果擴充功能無法存取公用網際網路，備份最終會失敗。

### <a name="solution"></a>方案
若要解決此問題，請嘗試下列其中一個方法。
#### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>允許存取 Azure 資料中心的 IP 範圍

1. 取得允許存取的 [Azure 資料中心 IP 清單](https://www.microsoft.com/download/details.aspx?id=41653)。
2. 在提升權限的 PowerShell 視窗中，於 Azure VM 中執行 **New-NetRoute** Cmdlet 來解除封鎖 IP。 以系統管理員身分執行 Cmdlet。
3. 若要允許存取 IP，可將規則新增到網路安全性群組 (如果您有一個)。

#### <a name="create-a-path-for-http-traffic-to-flow"></a>建立 HTTP 流量的行經路徑

1. 如果您已有網路限制 (例如，網路安全性群組)，請部署 HTTP Proxy 伺服器來路由傳送流量。
2. 若要允許從 HTTP Proxy 存取網際網路，可將規則新增到網路安全性群組 (如果您有一個)。

若要了解如何設定 VM 備份的 HTTP Proxy，請參閱[準備環境以備份 Azure 虛擬機器](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

## <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2︰VM 中安裝的代理程式已過時 (針對 Linux VM)

### <a name="solution"></a>方案
針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md)的指示。

 >[!NOTE]
 >我們強烈建議您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理程式程式碼，並加以更新。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

2. 執行下列命令，以確定 Azure 代理程式正在 VM 上執行：`ps -e`

 如果此程序並未執行，請使用下列命令來重新啟動它：

 * 針對 Ubuntu：`service walinuxagent start`
 * 針對其他散發套件︰`service waagent start`

3. [設定自動重新啟動代理程式](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 執行新的測試備份。 如果失敗持續發生，請收集下列來自客戶 VM 的記錄：

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

如果我們需要 waagent 的詳細資訊記錄，請遵循下列步驟：

1. 在 /etc/waagent.conf 檔案中，找出下一行︰**Enable verbose logging (y|n)**
2. 將 **Logs.Verbose** 值從 *n* 變更為 *y*。
3. 儲存變更，然後遵循本節前面的步驟，重新啟動 waagent。

## <a name="cause-3-the-backup-extension-fails-to-update-or-load"></a>原因 3︰備份擴充功能無法更新或載入
如果無法載入擴充功能，備份就會因為無法取得快照而失敗。

### <a name="solution"></a>方案

針對 Windows 客體作業系統︰

確認 iaasvmprovider 服務已啟用，而且啟動類型為「自動」。 如果服務不是使用此方式所設定，請啟用該服務以判斷下一次備份是否成功。

針對 Linux 客體︰

VMSnapshot for Linux (備份所使用的擴充功能) 的最新版本是 1.0.91.0。

如果還是無法更新或載入備份擴充功能，您可以透過解除安裝擴充功能來強制重新載入 VMSnapshot 擴充功能。 下一次的備份嘗試將會重新載入擴充功能。

若要解除安裝擴充功能，請執行下列動作：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。
2. 找出具有備份問題的 VM。
3. 按一下 [設定] 。
4. 按一下 [擴充功能]。
5. 按一下 [Vmsnapshot 擴充功能]。
6. 按一下 [解除安裝]。  

此程序會導致在下一次備份期間重新安裝擴充功能。

## <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>原因 4︰無法擷取快照狀態或無法取得快照
VM 備份仰賴發給底層儲存體帳戶的快照命令。 備份可能會失敗，因為它無權存取儲存體帳戶，或是因為快照工作延遲執行。

### <a name="solution"></a>方案
下列狀況可能導致快照集工作失敗：

| 原因 | 方案 |
| --- | --- |
| VM 已設定 SQL Server 備份。 | 根據預設，VM 備份會在 Windows VM 上執行 VSS 完整備份。 在執行以 SQL Server 為基礎的伺服器並設定了 SQL Server 備份的 VM 上，可能會發生快照延遲執行。<br><br>如果您因為快照問題而遇到備份失敗，請設定下列登錄機碼：<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| 因為 RDP 中的 VM 關機，而導致報告的 VM 狀態不正確。 | 如果您關閉遠端桌面通訊協定 (RDP) 中的 VM，請檢查入口網站，以判斷 VM 狀態是否正確。 如果不正確，可使用 VM 儀表板上的 [關閉] 選項來關閉入口網站中的 VM。 |
| 相同的雲端服務中的許多 VM 都設定為同時備份。 | 最佳做法是向外分配相同雲端服務中 VM 的備份排程。 |
| VM 正在以高 CPU 或記憶體使用量執行。 | 如果 VM 正在以高 CPU 使用量 (超過 90%) 或高記憶體使用量執行，即會將快照工作排入佇列並延遲，而其最終會逾時。 在此情況下，請嘗試隨選備份。 |
| VM 無法從 DHCP 取得主機/網狀架構位址。 | 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。  如果 VM 無法從 DHCP 回應 245 取得主機/網狀架構位址，則無法下載或執行任何擴充功能。 如果您需要靜態私人 IP 位址，您應該透過平台來進行設定。 VM 內的 DHCP 選項應保持啟用。 如需詳細資訊，請參閱[設定靜態內部私人 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。 |

