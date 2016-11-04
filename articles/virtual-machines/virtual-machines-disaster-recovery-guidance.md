---
title: 發生影響 Azure 虛擬機器的 Azure 服務中斷事件時該怎麼辦 | Microsoft Docs
description: 了解發生影響 Azure 虛擬機器的 Azure 服務中斷事件時該怎麼辦。
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: timlt
editor: ''

ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick

---
# 發生影響 Azure 虛擬機器的 Azure 服務中斷事件時該怎麼辦
Microsoft 的同仁一向努力確保提供您需要的服務。有時候因為不可抗力之影響，造成服務意外中斷。

Microsoft 為其服務提供服務等級協定 (SLA)，作為執行時間和連接承諾。個別的 Azure 服務 SLA 位於 [Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

Azure 已經有許多支援高可用性應用程式的內建平台功能。如需這些服務的詳細資訊，請參閱 [Azure 應用程式的災害復原與高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

本文內含當整個地區因重大天災或大規模服務中斷而發生中斷的真實災害復原案例。這些都是極其罕見的情況，但您還是必須對整個區域發生中斷的可能性有所準備。如果整個區域的服務中斷，會暫時無法使用本機的備援資料複本。如果已啟用異地複寫，會在不同的區域儲存額外三份 Azure 儲存體 Blob 和資料表。如果發生全面性區域中斷或嚴重損壞，且主要區域無法復原時，Azure 會重新對應異地複寫區域的所有 DNS 項目。

> [!NOTE]
> 請注意，您完全無法控制這個程序，而且它只有在全區域服務中斷時才會發生。因此，您也必須依賴其他的應用程式特定備份策略，以達到最高層級的可用性。如需詳細資訊，請參閱[災害復原的資料策略](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery)一節。
> 
> 

為協助您處理這些罕見事件，我們提供以下 Azure 虛擬機器指引，以因應 Azure 虛擬機器應用程式部署所在的整個區域發生服務中斷的情況。

## 選項 1︰等待復原
在此情況下，您不需要採取任何動作。但您要知道，我們正在努力還原服務的可用性。您可以在 [Azure 服務健康狀態儀表板](https://azure.microsoft.com/status/)上看見目前的服務狀態。

> [!NOTE]
> 如果發生中斷前尚未設定 Azure Site Recovery、虛擬機器備份、讀取權限異地備援儲存體或異地備援儲存體，這便是您的最佳選項。如果儲存 VM 虛擬硬碟 (VHD) 的儲存體帳戶已設定異地備援儲存體或讀取權限異地備援儲存體，您可以指望復原基本映像 VHD，並嘗試用它佈建新的 VM。因為除非使用 Azure VM 備份或 Azure Site Recovery，否則不保證能夠同步處理資料，所以這不是慣用的選項。因此，不保證此選項可以運作。
> 
> 

想要立即存取虛擬機器的客戶有下列兩個選項可用。

> [!NOTE]
> 請注意，下列兩個選項都可能會遺失部分資料。
> 
> 

## 選項 2︰從備份還原 VM
您可以為已設定 VM 備份的客戶，從備份和復原點來還原 VM。

若要從 Azure 備份還原新的 VM，請參閱[還原 Azure 中的虛擬機器](../backup/backup-azure-restore-vms.md)。

請參閱[在 Azure 中規劃 VM 備份基礎結構](../backup/backup-azure-vms-introduction.md)，協助規劃您的 Azure 虛擬機器備份基礎結構。

## 選項 3︰使用 Azure Site Recovery 起始容錯移轉
如已設定 Azure Site Recovery 和受影響的 Azure 虛擬機器一起運作，您可以用它們的複本來還原 VM。這些複本可以位在 Azure 或內部部署上。如此，您就可以用其現有的複本來建立新的 VM。若要使用 Azure Site Recovery 複本還原 VM，請參閱[使用 Azure Site Recovery 在 Azure 區域之間移轉 Azure IaaS 虛擬機器](../site-recovery/site-recovery-migrate-azure-to-azure.md)。

> [!NOTE]
> 雖然 Azure 虛擬機器的作業系統和資料磁碟會複寫至次要 VHD，但如果它們位在異地備援儲存體或讀取權限異地備援儲存體帳戶，則會各自複寫每個 VHD。這個層級的複寫並不保證各個複寫的 VHD 一致。如果使用這些資料磁碟的應用程式和/或資料庫彼此有相依性，不保證所有的 VHD 都能複寫成一個快照。也不保證異地備援儲存體或讀取權限異地備援儲存體的 VHD 複本能讓應用程式一致的快照啟動 VM。
> 
> 

## 後續步驟
若要深入了解如何實作災害復原和高可用性策略，請參閱 [Azure 應用程式的災害復原和高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)。

若要開發雲端平台功能的詳細技術知識，請參閱 [Azure 復原技術指導](../resiliency/resiliency-technical-guidance.md)。

若要了解如何備份 VM，請參閱[備份 Azure 虛擬機器](../backup/backup-azure-vms.md)。

了解如何使用 Azure Site Recovery 來協調和自動保護在 VMWare 和 Hyper-V VM 上執行的實體 (和虛擬) Windows 和 Linux 機器，請參閱 [Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)。

如果指示不清楚，或如果您希望 Microsoft 代您執行作業，請連絡[客戶支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

<!---HONumber=AcomDC_0824_2016-->