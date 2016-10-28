<properties
	pageTitle="使用 Site Recovery 將 Windows 虛擬機器從 Amazon Web Services 移轉至 Azure | Microsoft Azure"
	description="本文說明如何使用 Azure Site Recovery 將 Amazon Web Services (AWA) 中執行的 Windows 虛擬機器移轉至 Azure。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/22/2016"
	ms.author="raynew"/>

#  使用 Azure Site Recovery 將 Amazon Web Services (AWS) 中的 Windows 虛擬機器移轉至 Azure

## Overview

歡迎使用 Azure Site Recovery。使用本文來使用 Site Recovery 將執行 AWS 的 Windows 執行個體移轉到 Azure。開始之前，請注意：

- Azure 建立和處理資源的部署模型有二種：Azure Resource Manager 和傳統。Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。無論您是在 Resource Manager 還是在傳統中設定 Site Recovery，基本的移轉步驟都是相同的。不過本文中的 UI 指示和螢幕擷取畫面都是和 Azure 入口網站相關。
- **目前您只能從 AWS 移轉到 Azure。您可以從 AWS 容錯移轉至 Azure，但是無法將它們容錯移轉回來。將不會有任何持續性的複寫。**
- 本文中的移轉指示是以將實體機器複寫到 Azure 的指示為基礎。它包含了[將 VMware VM 或實體伺服器複寫至 Azure](site-recovery-vmware-to-azure.md) 中步驟的連結，其內容描述如何在 Azure 入口網站中複寫實體伺服器。
- 如果您是在傳統入口網站中設定 Site Recovery，請遵循[這篇文章](site-recovery-vmware-to-azure-classic.md)中的詳細指示。您不應該再使用此[舊版文件](site-recovery-vmware-to-azure-classic-legacy.md)中的指示。

請在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題


## 必要條件

以下是您針對此部署所需要的項目

- **組態伺服器**︰執行 Windows Server 2012 R2 並做為組態伺服器的內部部署 VM。您也可在此 VM 上安裝其他 Site Recovery 元件 (包括處理序伺服器和主要目標伺服器)。請參閱[案例架構](site-recovery-vmware-to-azure.md#scenario-architecture)和[組態伺服器必要條件](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)以深入了解。
- **EC2 VM 執行個體**：執行您要移轉 Windows 的執行個體。

## 部署步驟

本節描述新 Azure 入口網站中的部署步驟。如果您需要傳統入口網站中的 Site Recovery 部署步驟，請參閱[這篇文章](site-recovery-vmware-to-azure-classic.md)。

1. [建立保存庫](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [部署組態伺服器](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 部署組態伺服器之後，請驗證該伺服器否能夠與您要移轉的 VM 通訊。
4. [設定複寫設定](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。建立複寫原則並將它指派到組態伺服器。
5. [安裝行動服務](site-recovery-vmware-to-azure.md#step-6-replication-application)。所有要保護的 VM 都需要安裝行動服務。這項服務會將資料傳送至處理序伺服器。行動服務可以手動方式安裝，或在為 VM 啟用保護時由處理序伺服器自動推入安裝。您要移轉的 EC2 執行個體上的防火牆規則應該設定為允許推入安裝這項服務。EC2 執行個體的安全性群組應該具有下列規則：

	![防火牆規則](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [啟用複寫](site-recovery-vmware-to-azure.md#enable-replication)。針對您想要移轉的 VM 啟用複寫。您可以使用可從 EC2 主控台取得的私人 IP 位址探索 EC2 執行個體。
7. [執行非計劃性容錯移轉](site-recovery-failover.md#run-an-unplanned-failover)。初始複寫完成之後，您可以為每部 VM 執行從 AWS 到 Azure 的非規劃性容錯移轉。(選擇性) 您可以建立復原計劃並執行未規劃的容錯移轉，將多部虛擬機器從 AWS 移轉至 Azure。[深入了解](site-recovery-create-recovery-plans.md)復原計劃。

## 後續步驟

在[什麼是 Azure Site Recovery？](site-recovery-overview.md)中深入了解其他複寫案例

<!---HONumber=AcomDC_0824_2016-->