---
title: "準備要上傳至 Azure 的 Windows VHD | Microsoft Docs"
description: "如何在上傳至 Azure 之前準備 Windows VHD 或 VHDX"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: glimoli;genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 4d8972e5a18cbe471ec4c5baa53992cc23fad129
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017


---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>準備 Windows VHD 或 VHDX 以上傳至 Azure
若要從內部部署將 Windows VM 上傳至 Azure，您必須準備虛擬硬碟 (VHD 或 VHDX)。 Azure 只支援採用 VHD 檔案格式且具有固定大小磁碟的第 1 代虛擬機器。 允許的 VHD 大小上限為 1023 GB。 您可以將第 1 代虛擬機器從 VHDX 轉換為 VHD 檔案格式，並從動態擴充轉換為固定大小的磁碟。 但您無法變更虛擬機器的世代。 如需詳細資訊，請參閱[應該在 Hyper-V 中建立第 1 代還是第 2 代的虛擬機器？](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>將虛擬磁碟轉換成 VHD 及固定大小的磁碟 
如果您需要將虛擬磁碟轉換為 Azure 所需的格式，請使用本節中的其中一種方法。 在執行虛擬磁碟轉換程序之前備份 VM ，並確定 Windows VHD 在本機伺服器上正常運作。 先解決 VM 本身的任何錯誤，然後嘗試轉換或上傳至 Azure。

在轉換磁碟之後，建立會使用轉換磁碟的 VM。 啟動並登入 VM 以完成準備上傳 VM。

### <a name="convert-disk-using-hyper-v-manager"></a>使用 HYPER-V 管理員轉換磁碟
1. 開啟 Hyper-V 管理員，然後在左側選取您的本機電腦。 在上方功能表中，按一下 [動作] >  [編輯磁碟]。
2. 在 [尋找虛擬硬碟]  畫面上，瀏覽並選取您的虛擬磁碟。
3. 在 [選擇動作] 畫面上，選取 [轉換] 和 [下一步]。
4. 如果您需要從 VHDX 進行轉換，選取 [VHD]，然後按 [下一步]
5. 如果您需要從動態擴充磁碟進行轉換，選取 [固定大小]，然後按 [下一步]
6. 瀏覽並選取 [儲存新的 VHD 檔案路徑]。
7. 按一下 [完成]  以關閉。

### <a name="convert-disk-using-powershell"></a>使用 PowerShell 轉換磁碟
您可以在 Windows PowerShell 中使用 [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet 來轉換虛擬磁碟。 當您啟動 PowerShell 時，選取 [以系統管理員身分執行]。 下列範例會示範如何從 VHDX 轉換至 VHD，以及從動態擴充磁碟轉換至固定大小的磁碟︰

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
將 -Path 的值取代為您想要轉換之虛擬硬碟的路徑，以及 -DestinationPath 的值取代為新路徑和轉換磁碟的名稱。

### <a name="convert-from-vmware-vmdk-disk-format"></a>從 VMware VMDK 磁碟格式進行轉換
如果您的 Windows VM 映像是 [VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)，使用 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) 將它轉換為 VHD。 如需詳細資訊，請參閱部落格：[How to Convert a VMware VMDK to Hyper-V VHD (如何將 VMWare VMDK 轉換為 Hyper-V VHD)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>設定適用於 Azure 的 Windows 設定

在您要上傳至 Azure 的虛擬機器上，在準備要上傳的虛擬機器上，使用 [系統管理權限](https://technet.microsoft.com/library/cc947813.aspx)從命令提示字元執行下列所有命令。


1. 在路由表上移除任何靜態持續路由：
   
   * 若要檢視路由表，請從命令提示字元視窗執行 `route print`。
   * 檢查 [持續路由]  區段。 如果有持續的路由，請使用 [route delete](https://technet.microsoft.com/library/cc739598.apx) 加以移除。
2. 移除 WinHTTP Proxy：
   
    ```CMD
    netsh winhttp reset proxy
    ```
3. 將磁碟 SAN 原則設為 [Onlineall](https://technet.microsoft.com/library/gg252636.aspx)。 
   
    ```CMD
    diskpart 
    san policy=onlineall
    exit
    ```
    

4. 設定適用於 Windows 的國際標準時間 (UTC)，並將 Windows 時間 (w32time) 服務的啟動類型設為 [自動]：
   
    ```CMD
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    ```
    ```CMD
    sc config w32time start= auto
    ```

## <a name="set-services-startup-to-windows-default-values"></a>將服務啟動設定為 Windows 的預設值
確定以下的每個 Windows 服務都已設為 **Windows 預設值**。 若要重設啟動設定，請執行下列命令：
   
```CMD
sc config bfe start= auto
   
sc config dcomlaunch start= auto
   
sc config dhcp start= auto
   
sc config dnscache start= auto
   
sc config IKEEXT start= auto
   
sc config iphlpsvc start= auto
   
sc config PolicyAgent start= demand
   
sc config LSM start= auto
   
sc config netlogon start= demand
   
sc config netman start= demand
   
sc config NcaSvc start= demand
   
sc config netprofm start= demand
   
sc config NlaSvc start= auto
   
sc config nsi start= auto
   
sc config RpcSs start= auto
   
sc config RpcEptMapper start= auto
   
sc config termService start= demand
   
sc config MpsSvc start= auto
   
sc config WinHttpAutoProxySvc start= demand
   
sc config LanmanWorkstation start= auto
   
sc config RemoteRegistry start= auto
```

## <a name="update-remote-desktop-registry-settings"></a>更新遠端桌面登錄設定
1. 如果有任何自我簽署憑證繫結至遠端桌面通訊協定 (RDP) 接聽程式，請移除它們：
   
    ```CMD
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    如需如何針對 RDP 接聽程式設定憑證的詳細資訊，請參閱 [Listener Certificate Configurations in Windows Server (Windows Server 中的接聽程式憑證組態) ](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)
2. 針對 RDP 服務設定 [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) 值：
   
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
    ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. 設定 RDP 服務的驗證模式：
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   ```
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. 將下列子機碼加入至登錄以啟用 RDP 服務：
   
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>設定 Windows 防火牆規則
1. 在 PowerShell 中執行下列命令以允許 WinRM 透過三種防火牆設定檔 (網域、私人和公用)，並啟用 PowerShell 遠端服務：
   
   ```powershell
   Enable-PSRemoting -force
   ```
2. 在命令提示字元視窗中執行下列命令，以確定下列客體作業系統的防火牆規則已就緒：
   
   * 輸入
   
   ```CMD
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * 輸入和輸出
   
   ```CMD
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * 輸出
   
   ```CMD
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>確認 VM 處於狀況良好、安全且可使用 RDP 存取 
1. 在命令提示字元視窗中，執行 `winmgmt /verifyrepository` 以確認 Windows Management Instrumentation (WMI) 存放庫是一致的。 如果存放庫損毀，請參閱部落格文章 [WMI︰資料庫損毀，還是沒有損毀？](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)
2. 設定開機組態資料 (BCD) 設定︰
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. 移除任何額外的傳輸驅動程式介面篩選，例如分析 TCP 封包的軟體。
4. 若要確定磁碟狀況良好且一致，請在命令提示字元視窗中執行 `CHKDSK /f` 命令。 輸入"Y" 以排程檢查並重新啟動 VM。
5. 將與實體元件或任何其他虛擬化技術相關的所有其他協力廠商軟體和驅動程式解除安裝。
6. 確定協力廠商應用程式並未使用連接埠 3389。 在 Azure 中，此連接埠是由 RDP 服務所使用。 您可以在命令提示字元視窗中執行 `netstat -anob`，以查看應用程式所使用的連接埠。
7. 如果您想要上傳的 Windows VHD 是一個網域控制站，請遵循 [這些額外步驟](https://support.microsoft.com/kb/2904015) 來準備磁碟。
8. 將 VM 重新開機，以確保 Windows 仍然狀況良好，可以使用 RDP 連線來達成。
9. 重設目前的本機系統管理員密碼，並確定您可以使用此帳戶，透過 RDP 連線登入 Windows。 此存取權限會受到「允許透過遠端桌面服務登入」群組原則物件所控制。 您可以在 [電腦設定]\[Windows 設定]\[安全性設定]\[本機原則]\[使用者權限指派] 之下的本機群組原則編輯器檢視此物件。

## <a name="install-windows-updates"></a>安裝 Windows 更新
安裝 Windows 的最新更新。 如果這不可行，請確定已安裝下列更新：
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VM 無法從網路中斷情況復原，並發生資料損毀問題
   * [KB3115224](https://support.microsoft.com/kb/3115224) 為在 Windows Server 2012 R2 或 Windows Server 2012 主機上執行的 VM 提供的可靠性改進
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031：Microsoft Windows 的安全性更新，可解決提高權限問題：2016 年 3 月 8 日
   * [KB3063075](https://support.microsoft.com/kb/3063075) 當您在 Microsoft Azure 中執行 Windows Server 2012 R2 虛擬機器時，會記錄許多識別碼 129 的事件
   * [KB3114025](https://support.microsoft.com/kb/3114025) 當您從 Windows 8.1 或 Server 2012 R2 存取 Azure 檔案儲存體時效能變慢
   * [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix 可針對 Windows 中的 Azure 服務，提高每個處理序在 RIO 緩衝區中的 64K 限制
   * [KB3004545](https://support.microsoft.com/kb/3004545) 您無法在 Windows 中透過 VPN 連線，存取裝載於 Azure 裝載服務上的虛擬機器
   * [KB3082343](https://support.microsoft.com/kb/3082343) 當 Azure 網站間 VPN 通道使用 Windows Server 2012 R2 RRAS 時，遺失了跨單位 VPN 連線
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048：CSRSS 安全性更新的描述︰2016 年 4 月 12 日
   * [KB2904100](https://support.microsoft.com/kb/2904100) 在 Windows 中，系統會在磁碟 I/O 期間凍結
     
## 執行 Sysprep  <a id="step23"></a>    
如果您想要建立部署至多部 VM 的映像，您需要先執行 Sysprep 來將該映像一般化，再將 VHD 上傳至 Azure。 您不需要執行 Sysprep 即可使用特定的 VHD。 

Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

1. 登入 Windows 虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。
3. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化] 核取方塊。
4. 在 [關機選項] 中選取 [關機]。
5. 按一下 [確定] 。
   
    ![啟動 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 完成時，會關閉虛擬機器。 不要重新啟動 VM。




## <a name="complete-recommended-configurations"></a>完成建議的設定
下列設定不會影響 VHD 上傳。 不過，我們強烈建議您設定它們。

* 安裝 [Azure 虛擬機器代理程式](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 安裝代理程式之後，您就能啟用 VM 擴充功能。 VM 擴充功能實作了您想要與 VM 搭配使用的大部分重要功能，例如重設密碼、設定 RDP，以及許多其他功能。
* 傾印記錄檔能幫助您進行 Windows 損毀問題的疑難排解。 啟用傾印記錄檔收集：
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* 在 Azure 中建立 VM 之後，在磁碟機 D 上設定系統定義的大小分頁檔：
  
    ```CMD
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>後續步驟
* [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](upload-generalized-managed.md)


