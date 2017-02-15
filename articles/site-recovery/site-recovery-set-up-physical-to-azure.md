---
title: "設定來源環境 (實體伺服器至 Azure) | Microsoft Docs"
description: "這篇文章說明如何設定內部部署環境，開始將執行 Windows 或 Linux 的實體伺服器複寫至 Azure。"
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
ms.openlocfilehash: 9416ec83089a6892099634b0572b2c794dc74dba

---

# <a name="set-up-the-source-environment-physical-server-to-azure"></a>設定來源環境 (實體伺服器至 Azure)
> [!div class="op_single_selector"]
> * [VMware 虛擬機器](./site-recovery-set-up-vmware-to-azure.md)
> * [實體伺服器](./site-recovery-set-up-physical-to-azure.md)

這篇文章說明如何設定內部部署環境，開始將執行 Windows 或 Linux 的實體伺服器複寫至 Azure。

## <a name="prerequisites"></a>必要條件

本文假設您已經有
1. 復原服務保存庫 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")。
3. 可供安裝設定伺服器的實體電腦。

### <a name="configuration-server-minimum-requirements"></a>設定伺服器的最低需求
下表列出設定伺服器的硬體、軟體和網路的最低需求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-server-requirements.md)]

> [!NOTE]
> 設定伺服器不支援 HTTPS 型 Proxy 伺服器。

## <a name="choose-your-protection-goals"></a>選擇您的保護目標

1. 在 Azure 入口網站中，瀏覽至 [復原服務] 保存庫刀鋒視窗，然後選取您的保存庫。
2. 在保存庫的 [資源] 功能表中，按一下 [開始使用] > [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。

    ![選擇目標](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. 在 [保護目標] 中選取 [至 Azure]，然後選取 [未虛擬化/其他]。 然後按一下 [確定] 。

    ![選擇目標](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>設定來源環境

1. 在 [準備來源] 中，如果您沒有組態伺服器，請按一下 [+組態伺服器] 來新增一部伺服器。

  ![設定來源](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. 在 [加入伺服器] 刀鋒視窗中，檢查 [組態伺服器] 是否出現在 [伺服器類型] 中。
4. 下載 Site Recovery 統一安裝的安裝檔案。
5. 下載保存庫註冊金鑰。 執行整合安裝時，您需要有註冊金鑰。 該金鑰產生後**五**天內有效。

    ![設定來源](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. 在作為設定伺服器的電腦上，請執行 **Azure Site Recovery 整合安裝**，以安裝設定伺服器、處理伺服器和主要目標伺服器。

#### <a name="running-the-azure-site-recovery-unified-setup"></a>執行 Azure Site Recovery 整合安裝

> [!TIP]
> 如果您電腦系統時鐘的時間比當地時間快或慢五分鐘以上，設定伺服器註冊會失敗。  開始安裝之前，請先將系統時鐘與[時間伺服器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 您可以透過命令列安裝設定伺服器。 如需詳細資訊，請參閱[使用命令列工具安裝設定伺服器](http://aka.ms/installconfigsrv)。


## <a name="common-issues"></a>常見問題

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>後續步驟
下一個步驟是在 Azure 中[設定目標環境](./site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment)。



<!--HONumber=Jan17_HO2-->


