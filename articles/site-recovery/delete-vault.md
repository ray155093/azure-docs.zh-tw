---
title: "刪除復原服務保存庫"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>刪除復原服務保存庫
相依性可防止您刪除復原服務保存庫，而且您需要執行的動作會依據下列 Azure Site Recovery 案例類型而改變：VMWare 到 Azure、Hyper-V (具備或不具備 VMM) 到 Azure，及 Azure 備份。 若要刪除 Azure 備份中使用的保存庫，請查看[此連結](../backup/backup-azure-delete-vault.md)。

>[!Important]
>如果您正在測試產品，並想要快速刪除保存庫且不需要考慮資料遺失問題，可使用強制刪除方法來移除保存庫和其所有相依性。

> 請注意，PowerShell 命令將會刪除保存庫的所有內容，而且此步驟無法復原

## <a name="force-delete-vault-using-powershell"></a>使用 Powershell 強制刪除保存庫

依照下列步驟刪除 Site Recovery 保存庫，即使有受保護的項目也會一併刪除

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



依照針對您的案例所建議的步驟 (以指定順序) 刪除保存庫

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>刪除 Site Recovery 中用來保護 VMWare VM 到 Azure 的保存庫：
1. 請參閱[作法](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)，確定刪除所有受保護的 VM。
2.  請參閱[作法](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)，確定刪除所有複寫原則。
3.  請參閱[作法](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery)，確定刪除對 vCenter 的參考。
4. 請參閱[作法](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)，以確定刪除設定伺服器。
5. 現在請嘗試刪除保存庫。


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>刪除 Site Recovery 中用來保護 Hyper-V VM (具備 VMM) 到 Azure 的保存庫：
1.  請參閱[作法](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)，確定刪除所有受保護的 VM。
- 請參閱[作法](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)，確定刪除所有複寫原則。
-   請參閱[作法](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)，刪除對 VMM 伺服器的參考
-   現在請嘗試刪除保存庫。

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>刪除 Site Recovery 中用來保護 Hyper-V VM (不具備 VMM) 到 Azure 的保存庫：
1. 請參閱[作法](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)，確定刪除所有受保護的 VM。
- 請參閱[作法](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)，確定刪除所有複寫原則。
-   請參閱[作法](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)，刪除對 Hyper-V 伺服器的參考。
-   刪除 Hyper-V 站台。
-   現在請嘗試刪除保存庫。

