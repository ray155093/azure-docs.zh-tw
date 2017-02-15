---
title: "安裝行動服務 (VMware/實體至 Azure) | Microsoft Docs"
description: "本文說明如何在內部部署電腦上安裝行動服務代理程式，以開始保護這些電腦。"
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
ms.date: 12/9/2016
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 09a84e5afa4bd462207179c7505bc80400a5a3a5

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>安裝行動服務 (VMWare/實體至 Azure)
行動服務需要部署在您想要複寫至 Azure 的每部電腦上 (VMware VM 或實體伺服器)。 它會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。  您可以使用下列方法，將行動服務部署至需要保護的伺服器
1. [使用 System Center Configuration Manager 之類的軟體部署工具安裝行動服務](site-recovery-install-mobility-service-using-sccm.md)
2. [使用 Azure 自動化和期望狀態設定 (DSC) 安裝行動服務](site-recovery-automate-mobility-service-install.md)
3. [使用圖形化使用者介面 (GUI) 手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [使用命令列手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [從 Azure Site Recovery 使用推入安裝來安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)

## <a name="prerequisites"></a>必要條件
在伺服器上手動開始安裝行動服務之前，請執行這些必要條件。
1. 登入設定伺服器，以系統管理權限開啟命令提示字元。
2. 將目錄切換至 bin 資料夾，並建立複雜密碼檔案

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. 因為我們在行動服務安裝期間需要使用此檔案，請將它儲存在安全的位置。
4. 在此目錄下，可以找到適用於所有受支援作業系統的行動服務安裝程式     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>行動服務安裝程式與作業系統之間的對應

| 安裝程式檔案的範本名稱| 作業系統 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 位元) SP1</br> Windows Server 2012 (64 位元) </br> Windows Server 2012 R2 (64 位元) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4、6.5、6.6 (僅限 64 位元) </br> CentOS 6.4、6.5、6.6 (僅限 64 位元) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元)|
|Microsoft-ASR_UA\*Ubuntu-14.04-64\*release.tar.gz | Ubuntu 14.04 (僅限 64 位元)|

## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>使用圖形化使用者介面手動安裝行動服務

>[!NOTE]
> 只有 Microsoft Windows 作業系統才支援透過圖形化使用者介面來安裝。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>使用命令列手動安裝行動服務
### <a name="command-line-based-install-on-windows-computers"></a>在 Windows 電腦上透過命令列安裝
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>在 Linux 電腦上透過命令列安裝
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>從 Azure Site Recovery 使用推入安裝來安裝行動服務
為了能夠使用 Azure Site Recovery 執行行動服務的推入安裝，您需要確定所有目標電腦上符合下列必要條件。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>後續步驟
安裝行動服務後，您可以使用 Azure 入口網站中的 [+複寫] 按鈕，開始對這些 VM 啟用保護。



<!--HONumber=Jan17_HO2-->


