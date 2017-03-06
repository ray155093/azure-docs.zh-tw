---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>安裝行動服務
啟用虛擬機器和實體伺服器保護的第一個步驟是安裝行動服務。 您可以使用下列幾種方式來執行這個動作：

* **處理序伺服器推送**︰當您在機器上啟用複寫時，請從處理序伺服器推送並安裝行動服務元件。 
請注意，如果機器已經執行最新版本的元件，便不會進行推送安裝。
* **企業推送**：使用您的企業推送處理程序 (例如 WSUS、System Center Configuration Manager 或 [Azure 自動化和預期狀態設定](site-recovery-automate-mobility-service-install.md)) 來自動安裝元件。 請先設定組態伺服器，再執行這項操作。
* **手動安裝**︰在您想要複寫的每一部機器上手動安裝元件。 請先設定組態伺服器，再執行這項操作。

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>準備在 Windows 機器上自動推入
以下是如何準備 Windows 機器，讓處理序伺服器可以自動安裝行動服務。

1. 建立可以由處理序伺服器用來存取機器的帳戶。 帳戶應該具備系統管理員權限 (本機或網域)，並且僅用於推送安裝。

   > [!NOTE]
   > 如果您未使用網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下加入 DWORD 項目 LocalAccountTokenFilterPolicy，其值為 1。 若要從 CLI 新增登錄項目，請輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。
   >
   >
2. 在您想要保護之機器的 Windows 防火牆上，選取 [允許應用程式或功能通過防火牆] 。 啟用 [檔案及印表機共用] 和 [Windows Management Instrumentation]。 對於隸屬於網域的機器，您可以使用 GPO 設定防火牆設定。

   ![防火牆設定](./media/site-recovery-vmware-to-azure/mobility1.png)
3. 加入您所建立的帳戶：

   * 開啟 **cspsconfigtool**。 它會在桌面上的捷徑，位於 [安裝位置]\home\svsystems\bin 資料夾。
   * 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
   * 加入您所建立的帳戶。 新增帳戶之後，當您啟用機器的複寫時，必須提供認證。

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>準備在 Linux 伺服器上自動推入
1. 確定您要保護的 Linux 機器受到支援，如 [受保護的機器必要條件](#protected-machine-prerequisites)中所述。 確定 Linux 機器和處理序伺服器之間有網路連線。
2. 建立可以由處理序伺服器用來存取機器的帳戶。 帳戶應該是來源 Linux 伺服器上的根使用者，並且僅用於推送安裝。

   * 開啟 **cspsconfigtool**。 它會在桌面上的捷徑，位於 [安裝位置]\home\svsystems\bin 資料夾。
   * 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
   * 加入您所建立的帳戶。 新增帳戶之後，當您啟用機器的複寫時，必須提供認證。
3. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4. 在您想要複寫的機器上安裝最新的 openssh、openssh 伺服器、openssl 封裝。
5. 請確定 SSH 已啟用且正在連接埠 22 上執行。
6. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示：

   * 以 root 的身分登入。
   * 在 /etc/ssh/sshd_config 檔案中，尋找以 **PasswordAuthentication** 開頭的行。
   * 取消該行的註解並將值從 **no** 變更為 **yes**。
   * 尋找以 **Subsystem** 為開頭的行並取消其註解。

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>手動安裝行動服務
安裝程式位於「設定伺服器」上的 **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository** 中。

| 來源作業系統 | 行動服務安裝檔案 |
| --- | --- |
| Windows Server (僅限&64; 位元) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4、6.5、6.6 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>在 Windows 伺服器上安裝行動服務
1. 下載並執行相關安裝程式。
2. 在 [開始之前] 中，選取 [行動服務]。

    ![行動服務](./media/site-recovery-vmware-to-azure/mobility3.png)
3. 在 [組態伺服器詳細資料] 中，指定組態伺服器的 IP 位址，以及您執行「統一安裝」時所產生的複雜密碼。 您可以在組態伺服器上執行下列命令來擷取複雜密碼：**<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v**。

    ![行動服務](./media/site-recovery-vmware-to-azure/mobility6.png)
4. 在 [安裝位置] 中，保留預設設定，然後按 [下一步] 來開始安裝。
5. 在 [安裝進度] 中，監視安裝的進度，並在系統提示時重新啟動機器。 安裝服務之後可能需要大約 15 分鐘，狀態才會更新在入口網站中。

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>使用命令提示字元在 Windows 伺服器上安裝行動服務
1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 C:\Temp)。 安裝程式位於「組態伺服器」上的 **[安裝位置]\home\svsystems\pushinstallsvc\repository** 中。 Windows 作業系統的套件名稱類似於 Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. 請將此檔案重新命名為 MobilitySvcInstaller.exe
3. 執行下列命令來擷取 MSI 安裝程式：

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>完整的命令列語法
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**參數**

* **/Role：**必要。 指定是否應該要安裝行動服務。 輸入值 Agent|MasterTarget
* **/InstallLocation：**必要。 指定安裝服務的位置。
* **/PassphraseFilePath：**必要。 組態伺服器複雜密碼。
* **/LogFilePath：**必要。 應該用來建立安裝記錄檔的位置。

#### <a name="uninstall-the-mobility-service-manually"></a>手動解除安裝行動服務
您可以使用 [控制台] 中的 [新增或移除程式] 或使用下列命令列指令，來解除安裝行動服務︰MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} 

#### <a name="install-the-mobility-service-on-a-linux-server"></a>在 Linux 伺服器上安裝行動服務
1. 根據上表，將適當的 tar 封存檔複製到您要複寫的 Linux 機器。
2. 執行下列命令來開啟殼層程式，並將壓縮的 tar 封存檔解壓縮到本機路徑：`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. 在解壓縮 tar 封存檔內容的本機目錄中建立 passphrase.txt 檔案。 若要執行這項操作，請在組態伺服器上從 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 複製複雜密碼，然後在殼層中執行 *`echo <passphrase> >passphrase.txt`* 以將它儲存在 passphrase.txt 中。
4. 執行 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*來安裝行動服務。
5. 指定組態伺服器的內部 IP 位址，並確定已選取連接埠 443。 安裝服務之後可能需要大約 15 分鐘，狀態才會更新在入口網站中。

**您也可以從命令列安裝**：

在組態伺服器上從 C:\Program Files (x86)\InMage Systems\private\connectio 複製複雜密碼，並且在組態伺服器上將它儲存為 "passphrase.txt"。 然後執行以下命令。 在我們的範例中，組態伺服器的 IP 位址是 104.40.75.37 且 HTTPS 連接埠應該是 443：


若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

若要在主要目標伺服器上安裝：

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




