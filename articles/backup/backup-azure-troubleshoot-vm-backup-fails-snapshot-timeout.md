---
title: "Azure VM 備份失敗︰無法與 VM 代理程式通訊來取得快照集狀態 - 快照 VM 子工作已逾時 | Microsoft Docs"
description: "與無法與 VM 代理程式通訊來取得快照集狀態相關的 Azure VM 備份失敗的徵狀原因和解決方案。 快照集 VM 子工作已逾時錯誤"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM 備份失敗︰無法與 VM 代理程式通訊來取得快照集狀態 - 快照集 VM 子工作已逾時
## <a name="summary"></a>摘要
註冊及排程備份 Azure 備份的 Azure 虛擬機器 (VM) 之後，Azure 服務會藉由與 VM 中的備份擴充功能通訊來取得時間點快照集，在排定的時間起始備份作業。 有些情況可能會造成無法觸發快照集，因而導致備份失敗。 這篇文章提供關於 Azure VM 備份失敗中，與快照集逾時錯誤相關之問題的疑難排解步驟。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>徵狀
基礎結構即服務 (IaaS) 適用的 Microsoft Azure 備份 VM 失敗，並在 [Azure 入口網站](https://portal.azure.com/)中的作業錯誤詳細資料中傳回下列錯誤訊息：

**無法與 VM 代理程式通訊來取得快照集狀態 - 快照集 VM 子工作已逾時。**

## <a name="cause"></a>原因
此錯誤有四個常見的原因︰

* VM 沒有網際網路存取。
* VM 中安裝的 Microsoft Azure VM 代理程式已過時 (針對 Linux VM)。
* 備份擴充功能無法更新或載入。
* 無法擷取快照集狀態或無法取得快照集。

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>原因 1：VM 沒有網際網路存取
根據部署需求，VM 沒有網際網路存取，或是有既有限制，防止存取 Azure 基礎結構。

備份擴充功能需要連線到 Azure 公用 IP 位址，才能正確運作。 這是因為它會將命令傳送至 Azure 儲存體端點 (HTTP URL) 來管理 VM 的快照集。 如果擴充功能無法存取公用網際網路，則備份最終會失敗。

### <a name="solution"></a>方案
在此案例中，使用下列方法之一來解決問題︰

* 將 Azure 資料中心的 IP 範圍列入允許清單
* 建立 HTTP 流量的行經路徑

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>將 Azure 資料中心的 IP 範圍列入允許清單
1. 取得要列入允許清單的 [Azure 資料中心 IP 清單](https://www.microsoft.com/download/details.aspx?id=41653) 。
2. 使用 New-NetRoute Cmdlet 解除封鎖 IP。 在提升權限的 PowerShell 視窗中 (以系統管理員身分執行)，於 Azure VM 執行這個 Cmdlet。
3. 若您有允許存取 IP 的規則，將規則新增網路安全性群組 (NSG)。

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>建立 HTTP 流量的行經路徑
1. 如果已有既定的網路限制 (例如，NSG)，請部署 HTTP Proxy 伺服器來路由傳送流量。
2. 如果您有網路安全性群組 (NSG)，請新增規則來允許從 HTTP Proxy 存取網際網路。

了解如何 [設定 VM 備份的 HTTP Proxy](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)。

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2︰VM 中安裝的 Microsoft Azure VM 代理程式已過時 (針對 Linux VM)
### <a name="solution"></a>方案
針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響舊 VM 代理程式的問題所造成。 一般來說，針對此問題進行疑難排解的首要步驟如下所示︰

1. [安裝最新版的 Azure VM 代理程式](https://github.com/Azure/WALinuxAgent)。
2. 確定 Azure 代理程式在 VM 上執行。 若要這樣做，請執行下列命令：```ps -e```
   
    如果此程序未執行，請使用下列命令來重新啟動它。
   
    針對 Ubuntu：```service walinuxagent start```
   
    針對其他散發套件︰```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


