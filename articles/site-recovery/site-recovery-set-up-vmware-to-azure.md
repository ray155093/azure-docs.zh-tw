---
title: "設定來源環境 (從 VMware 到 Azure) | Microsoft Docs"
description: "本文說明如何設定您的內部部署環境，以開始將 VMware 虛擬機器複寫到 Azure。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 2256ba395e8d5f7a1ce7d4d78168a9cc51d4f074

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>設定來源環境 (從 VMware 到 Azure)
> [!div class="op_single_selector"]
> * [VMware 虛擬機器](./site-recovery-set-up-vmware-to-azure.md)
> * [實體伺服器](./site-recovery-set-up-physical-to-azure.md)

本文說明如何設定您的內部部署環境，以開始將在 VMware 上執行的虛擬機器複寫到 Azure。

## <a name="prerequisites"></a>必要條件

本文假設您已經建立
1. 復原服務保存庫 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")。
2. 您 VMware vCenter 中可用來進行[自動探索](./site-recovery-vmware-to-azure.md#vmware-account-permissions)的專用帳戶。
3. 可供安裝「組態伺服器」的虛擬機器。

### <a name="configuration-server-minimum-requirements"></a>組態伺服器的最低需求
「組態伺服器」軟體應該部署在具有「高可用性」的 VMware 虛擬機器上。 下表列出組態伺服器的最低硬體、軟體及網路需求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-server-requirements.md)]

> [!NOTE]
> 「組態伺服器」不支援 HTTPS 型 Proxy 伺服器。

## <a name="choose-your-protection-goals"></a>選擇您的保護目標

1. 在 Azure 入口網站中，瀏覽至 [復原服務] 保存庫刀鋒視窗，然後選取您的保存庫。
2. 在保存庫的 [資源] 功能表中，按一下 [開始使用] > [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。

    ![選擇目標](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. 在 [保護目標] 中選取 [至 Azure]，然後選取 [是，使用 VMware vSphere Hyperviso]。 然後按一下 [確定] 。

    ![選擇目標](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>設定來源環境
設定來源環境涉及兩項主要活動

1. 安裝組態伺服器並向 Site Recovery 服務註冊。
2. 將 Azure Site Recovery 連接到內部部署 VMware vCenter 或 vSphere EXSi 主機以探索內部部署虛擬機器。

### <a name="step-1-install--register-a-configuration-server"></a>步驟 1：安裝及註冊組態伺服器

1. 按一下 [步驟 1：準備基礎結構]  >  [來源]。 在 [準備來源] 中，如果您沒有組態伺服器，請按一下 [+組態伺服器] 來新增一部伺服器。

    ![設定來源](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. 在 [加入伺服器] 刀鋒視窗中，檢查 [組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 執行「整合安裝」時，您需要該註冊金鑰。 該金鑰產生後**五**天內有效。

    ![設定來源](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. 在作為組態伺服器的電腦上，執行「Azure Site Recovery 整合安裝」，以安裝組態伺服器、處理伺服器和主要目標伺服器。

#### <a name="running-the-azure-site-recovery-unified-setup"></a>執行 Azure Site Recovery 整合安裝

> [!TIP]
> 如果您電腦「系統時鐘」的時間比當地時間快或慢五分鐘以上，「組態伺服器」註冊將會失敗。 開始安裝之前，請先將「系統時鐘」與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您可以透過命令列安裝「組態伺服器」。 如需詳細資訊，請參閱[使用命令列工具安裝組態伺服器](http://aka.ms/installconfigsrv)。

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>新增用於自動探索的 VMware 帳戶

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>步驟 2：新增 vCenter
若要允許 Azure Site Recovery 探索在您內部部署環境中執行的虛擬機器，您必須將 VMware vCenter Server 或 vSphere ESXi 主機與 Site Recovery 連接。

請按一下 [+vCenter] 按鈕來開始連接 VMware vCenter Server 或 VMware vSphere ESXi 主機。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>常見問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>後續步驟
下一個步驟是在 Azure 中[設定目標環境](./site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment)。



<!--HONumber=Jan17_HO2-->


