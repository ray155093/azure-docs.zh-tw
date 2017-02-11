---
title: "Azure Government 備份 | Microsoft Docs"
description: "本文提供可在 Azure Government 中使用之 Azure 備份功能的概觀。"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


---
# <a name="azure-government-backup"></a>Azure Government 備份

本文提供 Azure 備份服務的概觀，並列出 Azure Government 中可用的備份功能。 Azure 備份是可用來將資料備份到 Microsoft Cloud 或加以保護的 Azure 架構服務。 保護 Azure 中的資料不僅表示將資料備份到雲端，亦是指還原雲端中的資料或還原到內部部署安裝。 Azure 備份提供下列主要優點︰

- 自動儲存體管理
- 無限制的調整
- 多個儲存體選項
- 無限制的資料傳輸
- 資料加密
- 應用程式一致備份
- 長期保留

如果您還不熟悉 Azure 備份，而想要概略了解可用的功能，請閱讀[何謂 Azure 備份](../backup/backup-introduction-to-azure-backup.md)一文。

> [!IMPORTANT]
> Azure Government 備份目前支援 Service Manager 部署 (也稱為傳統部署模型)。 Resource Manager 部署尚不受支援。 請參閱下列文章以了解 [Azure Resource Manager 和傳統部署模型之間的差異](../resource-manager-deployment-model.md)。

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Azure Government 備份中可用的 Azure 備份元件

您可以使用 Azure 備份來保護︰檔案、資料夾、磁碟區、虛擬機器、應用程式與工作負載。 根據您想要保護什麼資料以及該資料的所在位置，您會使用不同的 Azure 備份元件。 下列各節含有每個元件所適用之 Azure 備份公開文件中文章的連結。

每篇文章會說明如何在傳統版本的入口網站中使用 Azure 備份元件。

### <a name="windows-server-and-windows-computers"></a>Windows Server 和 Windows 電腦

- [備份 Windows Server 和 Windows 用戶端電腦](../backup/backup-configure-vault-classic.md)
- [還原 Windows Server 和 Windows 用戶端電腦](../backup/backup-azure-restore-windows-server.md)
- [管理 Windows Server 和 Windows 用戶端電腦備份](../backup/backup-azure-manage-windows-server.md)
- [使用 Powershell 備份 Windows Server](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>虛擬機器

- [準備您的虛擬機器環境](../backup/backup-azure-vms-prepare.md)
- [備份虛擬機器](../backup/backup-azure-vms-first-look.md)
- [還原虛擬機器](../backup/backup-azure-restore-vms.md)
- [管理虛擬機器](../backup/backup-azure-manage-vms-classic.md)
- [使用 PowerShell 備份虛擬機器](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager

- [備份 System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Azure 備份伺服器

- [Azure 備份伺服器](../backup/backup-azure-microsoft-azure-backup-classic.md)

Azure 備份伺服器是一個 Azure 備份元件，其功用類似 System Center Data Protection Manager (DPM)。 Azure 備份伺服器可從單一主控台保護 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 用戶端等項目到雲端的應用程式工作負載。

## <a name="next-steps"></a>後續步驟

如果您不確定要從哪裡著手，請從[使用傳統部署模型將 Windows Server 或用戶端備份至 Azure](../backup/backup-configure-vault-classic.md)一文開始。 此教學課程會引導您完成在 Windows Server 或電腦上設定備份專案的步驟。

如果您已經知道您可以使用 Azure 備份，但是想要知道其費用，請參閱[備份定價頁面](http://azure.microsoft.com/pricing/details/backup/)。 其內含常見問題清單，或許能提供有用的資訊。 也請注意，[區域] 下拉式功能表中有兩個 Azure Government 區域。



<!--HONumber=Nov16_HO3-->


