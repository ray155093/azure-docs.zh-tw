---
title: "Azure Government 備份 | Microsoft Docs"
description: "本文提供可在 Azure Government 中使用之 Azure 備份功能的概觀。"
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Azure Government 備份

本文提供 Azure 備份服務的概觀，並列出 Azure Government 中可用的備份功能。 Azure 備份是可用來將資料備份到 Microsoft Cloud 或加以保護的 Azure 架構服務。 保護 Azure 中的資料不僅意謂著將資料備份到雲端，也意謂著將資料還原到雲端，或還原到內部部署安裝。 Azure 備份提供下列主要優點︰

- 自動儲存體管理
- 無限制的調整
- 多個儲存體選項
- 無限制的資料傳輸
- 資料加密
- 應用程式一致備份
- 長期保留

如果您還不熟悉 Azure 備份，而想要概略了解可用的功能，請閱讀[何謂 Azure 備份](../backup/backup-introduction-to-azure-backup.md)一文。

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Azure Government 備份中可用的 Azure 備份元件

您可以使用 Azure 備份來保護︰檔案、資料夾、磁碟區、虛擬機器、應用程式與工作負載。 根據您想要保護什麼資料以及該資料的所在位置，您會使用不同的 Azure 備份元件。 下列各節含有每個元件所適用之 Azure 備份公開文件中文章的連結。 這些小節依傳統入口網站或 Azure 入口網站劃分。 如果您打算使用 Resource Manager 部署，請使用 Azure 入口網站版本。

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>在 Azure 入口網站中使用 Windows Server 和 Windows 電腦

- [備份 Windows Server 和 Windows 用戶端電腦](../backup/backup-configure-vault.md)
- [還原 Windows Server 和 Windows 用戶端電腦](../backup/backup-azure-restore-windows-server.md)
- [管理 Windows Server 和 Windows 用戶端電腦備份](../backup/backup-azure-manage-windows-server.md)
- [使用 PowerShell 備份 Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>在傳統入口網站中使用 Windows Server 和 Windows 電腦

- [備份 Windows Server 和 Windows 用戶端電腦](../backup/backup-configure-vault-classic.md)
- [還原 Windows Server 和 Windows 用戶端電腦](../backup/backup-azure-restore-windows-server-classic.md)
- [管理 Windows Server 和 Windows 用戶端電腦備份](../backup/backup-azure-manage-windows-server-classic.md)
- [使用 PowerShell 備份 Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>在 Azure 入口網站中使用虛擬機器

- [準備您的虛擬機器環境](../backup/backup-azure-arm-vms-prepare.md)
- [備份虛擬機器](../backup/backup-azure-vms-first-look-arm.md)
- [還原虛擬機器](../backup/backup-azure-arm-restore-vms.md)
- [管理虛擬機器](../backup/backup-azure-manage-vms.md)
- [使用 PowerShell 備份虛擬機器](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>在傳統入口網站中使用虛擬機器

- [準備您的虛擬機器環境](../backup/backup-azure-vms-prepare.md)
- [備份虛擬機器](../backup/backup-azure-vms-first-look.md)
- [還原虛擬機器](../backup/backup-azure-restore-vms.md)
- [管理虛擬機器](../backup/backup-azure-manage-vms-classic.md)
- [使用 PowerShell 備份虛擬機器](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>在 Azure 入口網站中使用 System Center Data Protection Manager

- [備份 System Center Data Protection Manager](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>在傳統入口網站中使用 System Center Data Protection Manager

- [備份 System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>在 Azure 入口網站中使用 Azure 備份伺服器

「Azure 備份伺服器」是一個「Azure 備份」元件，其功能類似 System Center Data Protection Manager (DPM)，唯一的差異在於「Azure 備份伺服器」無法將資料儲存到磁帶。 Azure 備份伺服器可從單一主控台保護 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 用戶端等項目到雲端的應用程式工作負載。 「Azure 備份伺服器」不需要 System Center 授權。

- [Azure 備份伺服器](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>在傳統入口網站中使用 Azure 備份伺服器

- [Azure 備份伺服器](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>後續步驟

如果您不確定要從哪裡著手，請從[使用傳統部署模型將 Windows Server 或用戶端備份至 Azure](../backup/backup-configure-vault-classic.md)一文開始。 此教學課程會引導您完成在 Windows Server 或電腦上設定備份專案的步驟。

如果您已經知道您可以使用 Azure 備份，但是想要知道其費用，請參閱[備份定價頁面](http://azure.microsoft.com/pricing/details/backup/)。 其內含常見問題清單，或許能提供有用的資訊。 也請注意，[區域] 下拉式功能表中有兩個 Azure Government 區域。



<!--HONumber=Jan17_HO1-->


