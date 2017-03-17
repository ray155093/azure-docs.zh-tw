---
title: "使用軟體部署工具來自動化適用於 Azure Site Recovery 的行動服務安裝 | Microsoft Docs。"
description: "本文協助您使用 System Center Configuration Manager 之類的軟體部署工具，以自動化行動服務安裝"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 310f2a2fe793601d22952bf516a812bf4867bbec
ms.lasthandoff: 03/03/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>使用軟體部署工具來自動化行動服務安裝

本文提供範例說明如何使用 System Center Configuration Manager (SCCM)，將 Azure Site Recovery 行動服務部署在資料中心。 使用 SCCM 之類的軟體部署工具有下列優點
* 排程部署 - 在您規劃的軟體更新維護期間，全新安裝和升級。
* 大規模同時部署至數百個伺服器


> [!NOTE]
> 本文使用 System Center Configuration Manager 2012 R2 示範部署活動。 您也可以使用 [Azure 自動化和期望的狀態設定](site-recovery-automate-mobility-service-install.md)，以自動化行動服務安裝。

## <a name="prerequisites"></a>必要條件
1. 環境中已部署軟體部署工具，例如 System Center Configuration Manager(SCCM)。
  * 建立兩個[裝置集合](https://technet.microsoft.com/library/gg682169.aspx)，一個用於所有 **Windows 伺服器**，另一個用於所有 **Linux 伺服器** (都是您想要使用 Azure Site Recovery 保護的伺服器)。
3. 已向 Azure Site Recovery 註冊的設定伺服器。
4. SCCM 伺服器可存取的安全網路檔案共用 (SMB 共用)。

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>在執行 Microsoft Windows 作業系統的電腦上部署行動服務
> [!NOTE]
> 本文假設下列項目
> 1. 設定伺服器的 IP 位址是 192.168.3.121
> 2. 安全網路檔案共用是 \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>步驟 1︰準備部署
1. 在網路共用上建立資料夾，並命名為 **MobSvcWindows**
2. 登入設定伺服器，開啟系統管理命令提示字元
3. 執行下列命令來產生複雜密碼檔案。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 將 MobSvc.passphrase 檔案複製到網路共用上的 MobSvcWindows 資料夾。
5. 接下來，執行命令以瀏覽至設定伺服器上的安裝程式存放庫。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 將 **Microsoft ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** 複製到**網路共用上的 MobSvcWindows ** 資料夾。
7. 複製下列程式碼，儲存為 **install.bat** 放在 **MobSvcWindows** 資料夾中
> [!NOTE]
> 請記得將下列指令碼中的 [CSIP] 預留位置，換成設定伺服器 IP 位址的實際值。

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>步驟 2：建立套件

1. 登入 System Center Configuration Manager 主控台
2. 瀏覽至 [軟體程式庫] > [應用程式管理] > [套件]
3. 以滑鼠右鍵按一下 [套件]，然後選取 [建立套件]
4. 提供 [名稱]、[描述]、[製造商]、[語言]、[版本] 的值。
5. 勾選 [此套件包含來源檔案] 核取方塊。
6. 按一下 [瀏覽] 按鈕，選取儲存安裝程式的網路共用 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. 在 [選擇您要建立的程式類型] 頁面上，選取 [標準程式]，然後按 [下一步]

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. 在 [指定此標準程式的相關資訊] 頁面上，提供下列輸入，然後按 [下一步]。 (其他輸入可以保留預設值)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **參數名稱** | **值** |
|--|--|
| 名稱 | 安裝 Microsoft Azure 行動服務 (Windows) |
| 命令列 | install.bat |
| 程式可以執行 | 使用者是否登入 |
9. 在下一頁，選取目標作業系統。 行動服務可以安裝在 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2。

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. 按兩次 [下一步] 以完成精靈。

> [!NOTE]
> 指令碼支援全新安裝行動服務代理程式和升級/更新已安裝的代理程式。

### <a name="step-3-deploy-the-package"></a>步驟 3︰部署套件
1. 在 SCCM 主控台，以滑鼠右鍵按一下套件，然後選取 [發佈內容]****
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. 選取套件應該複製到的 **[發佈點](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成精靈後，套件會開始複寫至指定的發佈點
4. 完成套件發佈後，以滑鼠右鍵按一下套件，然後選取 [部署]****
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 選取您在必要條件一節所建立的 Widows Server 裝置集合，作為部署的目標集合。

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. 在 **指定內容目的地** 頁面上，選取您的 **發佈點**
7. 在 [指定控制此軟體部署方式的設定] 頁面上，確定已依需要選取用途。

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. 在 [指定此部署的排程] 中指定排程。 深入了解[排程套件](https://technet.microsoft.com/library/gg682178.aspx)
9. 根據資料中心的需求，在 **發佈點** 頁面上設定屬性，並完成精靈。

> [!TIP]
> 為了避免不必要的重新開機，請排定在每月維護期間或軟體更新期間安裝套件。

您可以使用 SCCM 主控台並移至 [監視]**** > [部署]**** > *[your package name]*，以監視部署進度
  ![](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>在執行 Linux 作業系統的電腦上部署行動服務
> [!NOTE]
> 本文假設下列項目
> 1. 設定伺服器的 IP 位址是 192.168.3.121
> 2. 安全網路檔案共用是 \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>步驟 1︰準備部署
1. 在網路共用上建立資料夾，並命名為 **MobSvcLinux**
2. 登入設定伺服器，開啟系統管理命令提示字元
3. 執行下列命令來產生複雜密碼檔案。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 將 MobSvc.passphrase 檔案複製到網路共用上的 MobSvcWindows 資料夾。
5. 接下來，執行命令以瀏覽至設定伺服器上的安裝程式存放庫。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 將下列檔案複製到網路共用上的 **MobSvcLinux**資料夾
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. 複製下列程式碼，儲存為 **install_linux.sh** 放在 **MobSvcLinux** 資料夾中
> [!NOTE]
> 請記得將下列指令碼中的 [CSIP] 預留位置，換成設定伺服器 IP 位址的實際值。

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>步驟 2：建立套件

1. 登入 System Center Configuration Manager 主控台
2. 瀏覽至 [軟體程式庫] > [應用程式管理] > [套件]
3. 以滑鼠右鍵按一下 [套件]，然後選取 [建立套件]
4. 提供 [名稱]、[描述]、[製造商]、[語言]、[版本] 的值。
5. 勾選 [此套件包含來源檔案] 核取方塊。
6. 按一下 [瀏覽] 按鈕，選取儲存安裝程式的網路共用 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. 在 [選擇您要建立的程式類型] 頁面上，選取 [標準程式]，然後按 [下一步]

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. 在 [指定此標準程式的相關資訊] 頁面上，提供下列輸入，然後按 [下一步]。 (其他輸入可以保留預設值)

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **參數名稱** | **值** |
|--|--|
| 名稱 | 安裝 Microsoft Azure 行動服務 (Linux) |
| 命令列 | ./install_linux.sh |
| 程式可以執行 | 使用者是否登入 |

9. 在下一頁，選取 [此程式可以在任何平台上執行]****
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. 按兩次 [下一步] 以完成精靈。
> [!NOTE]
> 指令碼支援全新安裝行動服務代理程式和升級/更新已安裝的代理程式。

### <a name="step-3-deploy-the-package"></a>步驟 3︰部署套件
1. 在 SCCM 主控台，以滑鼠右鍵按一下套件，然後選取 [發佈內容]****
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. 選取套件應該複製到的 **[發佈點](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成精靈後，套件會開始複寫至指定的發佈點。
4. 完成套件發佈後，以滑鼠右鍵按一下套件，然後選取 [部署]****
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 選取您在必要條件一節所建立的 Linux 伺服器裝置集合，作為部署的目標集合。

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. 在 **指定內容目的地** 頁面上，選取您的 **發佈點**
7. 在 [指定控制此軟體部署方式的設定] 頁面上，確定已依需要選取用途。

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. 在 [指定此部署的排程] 中指定排程。 深入了解[排程套件](https://technet.microsoft.com/library/gg682178.aspx)
9. 根據資料中心的需求，在 **發佈點** 頁面上設定屬性，並完成精靈。

行動服務會根據您設定的排程，安裝在 Linux 伺服器裝置集合上。

## <a name="other-methods-to-install-mobility-services"></a>安裝行動服務的其他方法
深入了解安裝行動服務的其他方法。
* [使用 GUI 手動安裝](http://aka.ms/mobsvcmanualinstall)
* [使用命令列手動安裝](http://aka.ms/mobsvcmanualinstallcli)
* [使用設定伺服器推送安裝](http://aka.ms/pushinstall)
* [使用 Azure 自動化和期望的狀態設定來自動化安裝](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>將行動服務解除安裝
就像安裝一樣，您可以建立 SCCM 套件以將行動服務解除安裝。 使用以下指令碼以將行動服務解除安裝。

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>後續步驟
您現在可以對虛擬機器[啟用保護](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications)。

