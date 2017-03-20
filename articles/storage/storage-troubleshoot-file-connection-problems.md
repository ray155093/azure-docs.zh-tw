---
title: "針對 Azure 檔案儲存體的問題進行疑難排解 | Microsoft Docs"
description: "針對 Azure 檔案儲存體的問題進行疑難排解"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 0479db07710d7ff6037dc692e5387a314bed32ca
ms.lasthandoff: 03/08/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>針對 Azure 檔案儲存體的問題進行疑難排解
本文列出當您從 Windows 和 Linux 用戶端連接到 Microsoft Azure 檔案儲存體時相關的常見問題。 文中也會提供這些問題的可能原因和解決辦法。

**一般問題 (發生在 Windows 和 Linux 用戶端)**

* [嘗試開啟檔案時，發生配額錯誤](#quotaerror)
* [當您從 Windows 或 Linux 存取 Azure 檔案儲存體時效能緩慢](#slowboth)
* [如何追蹤 Azure 檔案儲存體中的讀取和寫入作業](#traceop)

**Windows 用戶端的問題**

* [當您從 Windows 8.1 或 Windows Server 2012 R2 存取 Azure 檔案儲存體時效能緩慢](#windowsslow)
* [錯誤 53 嘗試掛接 Azure 檔案共用](#error53)
* [錯誤 87 嘗試掛接為 Azure 檔案共用時，參數不正確](#error87)
* [net use 成功，但在 Windows 檔案總管 UI 中看不到掛接的 Azure 檔案共用或磁碟機代碼](#netuse)
* [我的儲存體帳戶包含 "/"，net use 命令失敗](#slashfails)
* [我的應用程式/服務無法存取掛接的 Azure 檔案磁碟機](#accessfiledrive)
* [效能最佳化的其他建議](#additional)
* [將檔案上傳/複製至 Azure 檔案時，發生「您正將檔案複製到不支援加密的目的地」錯誤](#encryption)

**Linux 用戶端的問題**

* [間歇性 IO 錯誤 - 在現有檔案共用上發生「主機當機 (錯誤 112)」，或在掛接點上執行 list 命令時殼層停止回應](#errorhold)
* [嘗試在 Linux VM 上掛接 Azure 檔案時，發生掛接錯誤 115](#error15)
* [掛接在 Linux VM 上的 Azure 檔案共用發生效能變慢的問題](#delayproblem)


**從其他應用程式存取**

* [我可以透過 Web 工作參考我的應用程式的 Azure 檔案共用嗎？](#webjobs)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>嘗試開啟檔案時，發生配額錯誤
在 Windows 中，您會收到類似以下的錯誤訊息︰

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

在 Linux 上，您會收到類似以下的錯誤訊息︰

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>原因
因為您已達到檔案所允許的同時開啟控點上限，就會發生此問題。

### <a name="solution"></a>方案
關閉一些控點以減少同時開啟的控點數，並再試一次。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)。

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>從 Windows 或 Linux 存取 Azure 檔案儲存體時效能緩慢
* 如果您沒有特定的 I/O 大小需求下限，建議您使用 1 MB 的 I/O 大小以獲得最佳效能。
* 如果您知道擴充寫入檔案的最終大小，而且當檔案上尚未寫入的結尾中有零時您的軟體沒有相容性問題，則請將事先設定檔案大小，而不是在每次寫入是擴充寫入時設定。
* 使用正確的複製方法：
      * 針對任何在兩個檔案共用間的傳輸使用 AZCopy。 如需詳細資料，請參閱[使用 AzCopy 命令列公用程式傳輸資料](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy)。
      * 在檔案共用和內部部署電腦之間使用 Robocopy。 如需詳細資料，請參閱[使用多執行緒的 Robocopy 以加快複製 (英文)](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)。
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>從 Windows 8.1 或 Windows Server 2012 R2 存取檔案儲存體時效能緩慢
若用戶端執行 Windows 8.1 或 Windows Server 2012 R2，請確定已安裝 Hotfix [KB3114025](https://support.microsoft.com/kb/3114025)。 此 hotfix 可改善建立和關閉控點的效能。

您可以執行下列指令碼來檢查是否已安裝此 hotfix：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果已安裝 hotfix，則會顯示下列輸出︰

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> 自 2015 年 12 月起，Azure Marketplace 中的 Windows Server 2012 R2 映像預設已安裝 hotfix KB3114025。
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>如何追蹤 Azure 檔案儲存體中的讀取和寫入作業

[Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226) 能夠以純文字顯示用戶端的要求，而連線要求與交易之間有相當良好的關係 (假設此處為 SMB，不是 REST)。  缺點是您必須在每個用戶端上執行此工具，如果您有許多 IaaS VM 工作人員，這就會相當費時。

如果您使用 Message Analyzer 搭配 ProcMon，便可清楚了解哪個應用程式程式碼負責那些交易。

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>效能最佳化的其他建議
絕對不要建立或開啟檔案進行要求寫入存取但不要求讀取存取的快取 I/O。 也就是說，當您呼叫 **Createfile()**，絕對不要只指定 **GENERIC_WRITE**，一定要指定 **GENERIC_READ |GENERIC_WRITE**。 唯寫的控點無法在本機快取少量寫入，即使它是該檔案的唯一開啟控點。 這會在少量寫入時帶來嚴重的效能損失。 請注意，"a" 模式 CRT **fopen()** 會開啟唯寫的控點。

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>當您嘗試掛接或取消掛接 Azure 檔案共用時發生「錯誤 53」或「錯誤 67」
這個問題可能是因下列情況而起︰

### <a name="cause-1"></a>原因 1
「發生系統錯誤 53。 存取遭到拒絕。」 基於安全性理由，如果通訊通道沒有加密，Azure 檔案共用的連線會遭到封鎖，故不會從 Azure 檔案共用所在的相同資料中心進行連線嘗試。 如果使用者的用戶端作業系統不支援 SMB 加密，則不會提供通訊通道加密。 指出這個狀況的就是「發生系統錯誤 53。 存取遭到拒絕。」錯誤訊息，在使用者嘗試從內部部署或不同的資料中心掛接檔案共用時出現。 Windows 8、Windows Server 2012 和更新版本的每個交涉皆會要求包含支援加密的 SMB 3.0。

### <a name="solution-for-cause-1"></a>原因 1 的解決辦法
從符合 Windows 8、Windows Server 2012 和更新版本需求的用戶端進行連線，或是從 Azure 檔案共用所使用的 Azure 儲存體帳戶相同的資料中心上的虛擬機器進行連線。

### <a name="cause-2"></a>原因 2
當您掛接 Azure 檔案共用時，如果連接埠 445 至 Azure 檔案資料中心的輸出通訊遭到封鎖，可能會發生「系統錯誤 53」或「系統錯誤 67」。 按一下[這裡](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)查看 ISP 是否允許從連接埠 445 進行存取的摘要。

Comcast 和某些 IT 組織會封鎖此連接埠。 若要了解這是否是「系統錯誤 53」訊息的原因，您可以使用 Portqry 查詢 TCP:445 端點。 如果篩選顯示 TCP:445 端點，則 TCP 連接埠會被封鎖。 查詢範例如下：

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

如果 TCP 445 是被網路路徑規則封鎖，您會看到下列輸出︰

**TCP port 445 (microsoft-ds service): FILTERED**

如需有關使用 Portqry 的詳細資訊，請參閱 [Portqry.exe 命令列公用程式的說明](https://support.microsoft.com/kb/310099)。

### <a name="solution-for-cause-2"></a>原因 2 的解決辦法
聯絡您的 IT 組織，要求開啟連接埠 445 輸出到 [Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

<a id="error87"></a>
### <a name="cause-3"></a>原因 3
如果用戶端上已啟用 NTLMv1 通訊，則也會收到「系統錯誤 53 或系統錯誤 87」。 啟用 NTLMv1 會使用戶端變得較不安全。 因此，Azure 檔案會封鎖通訊。 若要確認這是否為錯誤的原因，請確認下列登錄子機碼是設為 3︰

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel。

如需詳細資訊，請參閱 TechNet 上的 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 主題。

### <a name="solution-for-cause-3"></a>原因 3 的解決辦法
若要解決此問題，將 HKLM\SYSTEM\CurrentControlSet\Control\Lsa 登錄機碼中 LmCompatibilityLevel 的值還原為預設值 3。

Azure 檔案僅支援 NTLMv2 驗證。 請確定群組原則有套用到用戶端。 這會防止發生此錯誤。 這也是公認最符合安全性的最佳作法。 如需詳細資訊，請參閱[如何利用群組原則將用戶端設定為使用 NTLMv2](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx)。

建議的原則設定是**只傳送 NTLMv2 回應**。 這會對應到登錄值 3。 用戶端只會使用 NTLMv2 驗證，而且如果伺服器支援的話，用戶端會使用 NTLMv2 工作階段安全性。 網域控制站接受 LM、NTLM 和 NTLMv2 驗證。

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>net use 成功，但在 Windows 檔案總管中看不到掛接的 Azure 檔案共用
### <a name="cause"></a>原因
根據預設，Windows 檔案總管不會以系統管理員身分執行。 如果您從系統管理員命令提示字元執行 **net use**，會將網路磁碟機對應至「系統管理員身分」。 因為對應的磁碟機是以使用者為中心，如果磁碟機掛接在不同的使用者帳戶下，登入的使用者帳戶不會顯示此磁碟機。

### <a name="solution"></a>方案
從非系統管理員命令掛接共用。 或者，您可以依照[此 TechNet 主題](https://technet.microsoft.com/library/ee844140.aspx)設定 **EnableLinkedConnections** 登錄值。

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>我的儲存體帳戶包含 "/"，net use 命令失敗
### <a name="cause"></a>原因
在命令提示字元 (cmd.exe) 中執行 **net use** 命令時，會被剖析為加上 "/" 作為命令列選項。 這會導致磁碟機對應失敗。

### <a name="solution"></a>方案
您可以使用下列其中一種方式來解決這個問題：

•    使用下列 PowerShell 命令：

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

從批次檔可以這麼做

`Echo new-smbMapping ... | powershell -command –`

•    除非 "/" 是第一個字元，可以用雙引號括住索引鍵來解決此問題。 如果是，可使用互動模式分開輸入您的密碼，或者，重新產生索引鍵，以取得不是以正斜線 (/) 字元開頭的索引鍵。

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>我的應用程式/服務無法存取掛接的 Azure 檔案磁碟機
### <a name="cause"></a>原因
磁碟機是按每個使用者掛接。 如果您的應用程式或服務是在不同的使用者帳戶下執行，使用者就不會看到磁碟機。

### <a name="solution"></a>方案
從應用程所屬的相同使用者帳戶掛接磁碟機。 這可以使用 psexec 之類的工具。

使用 **net use** 的另一個做法，是在 **net use** 命令的使用者名稱和密碼參數中傳遞儲存體帳戶名稱和金鑰。

遵循這些指示操作之後，您可能會收到下列錯誤訊息：「發生系統錯誤 1312。 指定的登入工作階段不存在。 可能已被終止。」，在您以系統/網路服務帳戶執行 **net use ** 時。 如果發生這種情況，請確定傳遞至 **net use** 的使用者名稱包含網域資訊 (例如，"[storage account name].file.core.windows.net")。

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>「您正將檔案複製到不支援加密的目的地」錯誤
### <a name="cause"></a>原因
Bitlocker 加密的檔案可以複製到 Azure 檔案。 不過，檔案儲存體不支援 NTFS EFS。 因此，在此情況下您可能會使用 EFS。 如果您透過 EFS 加密檔案，複製到檔案儲存體的複製作業可能會失敗，除非複製命令會解密複製的檔案。

### <a name="workaround"></a>因應措施
若要將檔案複製到檔案儲存體，您必須先將它解密。 您可以使用下列方法之一：

•    使用 **copy /d**。

•    設定下列登錄機碼︰

* Path=HKLM\Software\Policies\Microsoft\Windows\System
* Value type=DWORD
* Name = CopyFileAllowDecryptedRemoteDestination
* Value = 1

不過請注意，設定登錄機碼會影響所有複製到網路共用的複製作業。

<a id="errorhold"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>在現有檔案共用上發生「主機當機 (錯誤 112)」錯誤，或在掛接點上執行 list 命令時殼層停止回應
### <a name="cause"></a>原因
當用戶端已經閒置一段時間，Linux 用戶端上會發生此錯誤。 用戶端長時間閒置時，用戶端會中斷連線，而連線會逾時。 

連線可能會因各種原因而閒置。 有一個原因是使用「軟」掛接選項時 (這是預設值)，造成無法重新建立 TCP 連線以連線到伺服器的網路通訊失敗。

另一個原因可能是還有一些未存在於較舊核心的重新連線修正程式。

### <a name="solution"></a>方案

指定硬掛接將會強制用戶端一直等候直到連線建立或明確中斷，而且它可用來防止因為網路逾時而發生錯誤。 不過，使用者應該注意這可能會導致無限期等候，而且應該視需要處理暫停連線的情況。

此 Linux 核心中的重新連線問題已隨下列變更集修正

* [修正重新連線在通訊端重新連線許久之後不會延遲 SMB3 工作階段重新連線 (英文)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)

* [在通訊端重新連線之後立即呼叫 Echo 服務 (英文)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)

* [CIFS：修正重新連線期間可能發生的記憶體損毀 (英文)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)

* [CIFS：修正重新連線期間可能發生的 Mutex 雙重鎖定 (針對核心&4;.9 版與更新版本) (英文)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) 

但是此變更可能尚未移植到所有 Linux 發行版本。 這是已知的常用 Linux 核心清單，其具有這個和其他重新連線修正程式︰4.4.40+ 4.8.16+ 4.9.1+。
您可以移至上述建議的核心版本，以便挑選最新的修正程式。

### <a name="workaround"></a>因應措施
如果無法移至最新的核心版本，您可以使用下列因應措施解決此問題：在 Azure 檔案共用中保留一個每 30 秒 (或更短時間) 就會寫入的檔案。 這必須是寫入作業，例如重寫檔案的建立/修改日期。 否則，您可能會取得快取的結果，而您的作業可能不會觸發重新連線。 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>嘗試在 Linux VM 上掛接 Azure 檔案時發生「掛接錯誤 115」
### <a name="cause"></a>原因
Linux 散發套件尚未支援 SMB 3.0 中的加密功能。 在某些散發套件中，如果使用者因為功能遺失而嘗試使用 SMB 3.0 來掛接 Azure 檔案，可能會收到「115」錯誤訊息。

### <a name="solution"></a>方案
如果使用的 Linux SMB 用戶端不支援加密，在與檔案儲存體帳戶相同的資料中心上，從 Linux VM 使用 SMB 2.1 掛接 Azure 檔案。

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>掛接在 Linux VM 上的 Azure 檔案共用發生效能變慢的問題

停用快取可能是效能變慢的原因。 查看 "cache=" 來檢查是否已啟用快取。  *cache=none* 表示已停用快取。 請使用預設的 mount 命令重新掛接共用，或明確地為 mount 命令加上 **cache=strict** 選項，以確保啟用預設快取或 "strict" 快取模式。

在某些情況下，serverino 掛接選項可能會造成 ls 命令對所有目錄項目執行 stat，這會在列出大型目錄時導致效能變慢。 您可以檢查 "/etc/fstab" 項目中的掛接選項：

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

您也可以執行 **sudo mount | grep cifs** 命令並查看其輸出，來檢查是否使用正確的選項：

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

如果沒有 cache=strict 或 serverino 選項，請執行[文件](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share)中的掛接命令，將 Azure 檔案卸載並再次掛接，然後重新檢查 "/etc/fstab" 項目是否有正確的選項。

<a id="webjobs"></a>

## <a name="accessing-from-other-applications"></a>從其他應用程式存取
### <a name="can-i-reference-the-azure-file-share-for-my-application-through-a-webjob"></a>我可以透過 Web 工作參考我的應用程式的 Azure 檔案共用嗎？
您無法在應用程式服務沙箱中掛接 SMB 共用。 因應措施是將 Azure 檔案共用對應為對應磁碟機，並允許應用程式以磁碟機代號方式存取它。
## <a name="learn-more"></a>詳細資訊
* [在 Windows 上開始使用 Azure 檔案儲存體](storage-dotnet-how-to-use-files.md)
* [在 Linux 上開始使用 Azure 檔案儲存體](storage-how-to-use-files-linux.md)

