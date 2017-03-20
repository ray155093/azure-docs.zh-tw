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
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 7e82ac74a8aef4e3cc8aff4dea3c572dcb9d9c40
ms.lasthandoff: 03/03/2017

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>安裝行動服務 (VMWare/實體至 Azure)
行動服務需要部署在您想要複寫至 Azure 的每部電腦上 (VMware VM 或實體伺服器)。 它會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。 您可以使用下列方法，將行動服務部署至需要保護的伺服器


1. [使用 System Center Configuration Manager 之類的軟體部署工具安裝行動服務](site-recovery-install-mobility-service-using-sccm.md)
2. [使用 Azure 自動化和期望狀態設定 (DSC) 安裝行動服務](site-recovery-automate-mobility-service-install.md)
3. [使用圖形化使用者介面 (GUI) 手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [使用命令列手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [從 Azure Site Recovery 使用推入安裝來安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)


>[!IMPORTANT]
> 從版本 9.7.0.0 開始，在 Windows 虛擬機器上，行動服務安裝程式也會安裝最新可用的 [Azure VM 代理程式](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent)。 這可確保電腦在容錯移轉至 Azure 時符合這項先決條件，以使用任何 VM 擴充功能。

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
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4、6.5、6.6、6.7、6.8 (僅限 64 位元) </br> CentOS 6.4、6.5、6.6、6.7。 6.8 (僅限&64; 位元) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元)|


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


> [!NOTE]
安裝行動服務後，您可以使用 Azure 入口網站中的 [+複寫] 按鈕，開始對這些 VM 啟用保護。

## <a name="uninstall-mobility-service-on-windows-servers"></a>將 Windows Server 上的行動服務解除安裝
您可以透過兩種方式將 Windows Server 上的「行動服務」解除安裝

### <a name="uninstall-using-graphical-user-interface"></a>使用圖形化使用者介面來解除安裝
1. 開啟 [控制台] > [程式集]
2. 選取 [Microsoft Azure Site Recovery Mobility Service/主要目標伺服器]，然後按一下 [解除安裝]。

### <a name="uninstall-using-command-line"></a>使用命令列來解除安裝
1. 開啟系統管理命令提示字元
2. 執行下列命令以將「行動服務」解除安裝。

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-linux-computers"></a>將 Linux 電腦上的行動服務解除安裝
1. 在您的 Linux 伺服器上以 **ROOT** 身分登入
2. 在「終端機」中，瀏覽至 /user/local/ASR
3. 執行下列命令以將「行動服務」解除安裝

```
uninstall.sh -Y
```

