---
title: "針對使用 Azure AD Connect 同步執行的密碼同步處理進行疑難排解 | Microsoft Docs"
description: "提供有關如何針對密碼同步處理問題進行疑難排解的資訊"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>針對使用 Azure AD Connect 同步執行的密碼同步處理進行疑難排解
本主題提供如何針對密碼同步處理問題進行疑難排解的步驟。 如果密碼未如預期般同步處理，它可適用於部份的使用者或所有的使用者。

* 如果有未同步處理任何密碼的問題，請參閱 [疑難排解未同步處理任何密碼的問題](#no-passwords-are-synchronized)。
* 如果您有個別的物件問題，則參閱[針對一個未同步處理密碼的物件進行疑難排解](#one-object-is-not-synchronizing-passwords)。

## <a name="no-passwords-are-synchronized"></a>未同步處理任何密碼
請依照下列步驟操作，以找出未同步處理任何密碼的原因：

1. Connect 伺服器是否是處於[預備模式](active-directory-aadconnectsync-operations.md#staging-mode)？ 處於預備模式的伺服器不會同步處理任何密碼。
2. 執行[取得密碼同步設定的狀態](#get-the-status-of-password-sync-settings)一節中的指令碼。 它可讓您大致了解密碼同步設定作業。  
![來自密碼同步處理設定的 PowerShell 指令碼輸出](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. 如果未在 Azure AD 中啟用此功能，或未啟用同步通道狀態，則執行 Connect 安裝精靈。 選取 [自訂同步處理選項]  並取消選取密碼同步處理。 這項變更會暫時停用此功能。 然後再次執行精靈並重新啟用密碼同步處理。 再次執行指令碼，確認組態正確無誤。
4. 查看事件記錄檔中是否有錯誤。 尋找下列事件，這些事件會指出問題所在︰
    1. 來源：「目錄同步作業」識別碼：0、611、652、655。如果您看到這些，即表示有連線問題。 事件記錄檔訊息包含發生問題的樹系資訊。 如需詳細資訊，請參閱[連線問題](#connectivity problem)
5. 如果您沒有看到任何活動訊號，或任何其他操作都沒有作用，則請執行[觸發所有密碼的完整同步處理](#trigger-a-full-sync-of-all-passwords)。 您應該只執行此指令碼一次。
6. 閱讀[針對一個未同步處理密碼的物件進行疑難排解](#one-object-is-not-synchronizing-passwords)一節。

### <a name="connectivity-problem"></a>連線問題

1. 您是否能夠與 Azure AD 連線？
2. 帳戶是否具備可讀取所有網域中密碼雜湊的必要權限？ 如果您已使用 [快速設定] 來安裝 Connect，權限應該已經正確。 如果您使用的是自訂安裝，則必須手動設定權限。
    1. 若要尋找「Active Directory 連接器」所使用的帳戶，請啟動 **Synchronization Service Manager**。 移至 [連接器]，然後尋找您要進行疑難排解的內部部署 Active Directory 樹系。 選取 [連接器]，然後按一下 [屬性]。 前往 [連線至 Active Directory 樹系] 。  
    ![AD 連接器所使用的帳戶](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    記下使用者名稱和帳戶所在的網域。
    2. 啟動 [Active Directory 使用者及電腦]。 請確認您在先前步驟中找到的帳戶在您樹系中的所有網域根目錄都已設定下列權限：
        * 複寫目錄變更
        * 複寫目錄變更 (全部)
3. Azure AD Connect 是否可以連線到網域控制站？ 如果 Connect 伺服器無法連線到所有網域控制站，則您應該設定 [只使用慣用的網域控制站]。  
    ![AD 連接器所使用的網域控制站](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    返回 **Synchronization Service Manager**，並**設定目錄磁碟分割**。 在 [選取目錄磁碟分割] 中選取您的網域，選取 [只使用慣用的網域控制站]，然後按一下 [設定]。 在清單中，輸入 Connect 應該用來進行密碼同步的網域控制站。 此相同清單也用於匯入和匯出。 針對您的所有網域執行這些步驟。
4. 如果指令碼顯示沒有活動訊號，則執行 [觸發所有密碼的完整同步處理](#trigger-a-full-sync-of-all-passwords)中的指令碼。

## <a name="one-object-is-not-synchronizing-passwords"></a>一個物件未同步處理密碼
您可以藉由檢閱物件的狀態，輕鬆地疑難排解密碼同步處理問題。

1. 從 [Active Directory 使用者和電腦] 開始。 尋找使用者並確認未選取 [使用者必須在下次登入時變更密碼]  。  
![Active Directory 生產力密碼](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
若已選取，則要求使用者登入並變更密碼。 暫時密碼不會同步處理至 Azure AD。
2. 如果 Active Directory 中正確，則下一步是在同步處理引擎中追蹤使用者。 從內部部署 Active Directory 到 Azure AD 追蹤使用者，您可以查看物件是否有描述性錯誤。
    1. 啟動 **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**。
    2. 按一下 [連接器] 。
    3. 選取使用者所位於的 **Active Directory 連接器** 。
    4. 選取 [搜尋連接器空間] 。
    5. 在 [範圍] 中，選取 [DN 或錨點]。 輸入您要進行疑難排解之使用者的完整 DN。
    ![使用 DN 在 CS 中搜尋使用者](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. 找出您要搜尋的使用者，然後按一下 [屬性] 來查看所有屬性。 如果該使用者不在搜尋結果中，則請確認您的[篩選規則](active-directory-aadconnectsync-configure-filtering.md)，並請務必執行[套用並驗證變更](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)，如此 Connect 中才會顯示該使用者。
    7. 若要查看過去一週物件的密碼同步詳細資料，請按一下 [記錄...] 。  
    ![取得記錄檔詳細資料](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    如果物件記錄檔是空的，則表示 Azure AD Connect 還無法從 Active Directory 讀取密碼雜湊。 請繼續進行[連線錯誤](#connectivity-errors)疑難排解。 如果您看到 [成功] 以外的任何其他值，則請參考[密碼同步記錄](#password-sync-log)中的表格。
    8. 選取 [歷程] 索引標籤，並確認至少有一個同步規則的 [密碼同步] 顯示為 [True]。 在預設組態中，同步規則的名稱是 **In from AD - User AccountEnabled**。  
    ![使用者的相關歷程資訊](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. 按一下 [Metaverse 物件屬性]。 您會看到該使用者中的屬性清單。  
    ![Metaverse 資訊](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    確認沒有任何 **cloudFiltered** 屬性存在。 確定網域屬性 (domainFQDN 和 domainNetBios) 具有預期的值。
    10. 按一下 [連接器] 索引標籤。 確定您看到連至內部部署 AD 和 Azure AD 的連接器。
    ![Metaverse 資訊](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. 選取代表 Azure AD 的資料列，然後按一下 [屬性]。 按一下 [歷程] 索引標籤。 連接器空間物件的輸出規則應該將 [密碼同步] 設為 [True]。 在預設組態中，同步規則的名稱是 **Out to AAD - User Join**。  
    ![使用者的連接器空間屬性](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>密碼同步記錄
[狀態] 欄可以有下列值︰

| 狀態 | 說明 |
| --- | --- |
| 成功 |已成功同步處理密碼。 |
| FilteredByTarget |密碼會設為 [使用者必須在下次登入時變更密碼] 。 未同步處理密碼。 |
| NoTargetConnection |Metaverse 或 Azure AD 連接器空間中沒有任何物件。 |
| SourceConnectorNotPresent |在內部部署 Active Directory 連接器空間中找不到任何物件。 |
| TargetNotExportedToDirectory |尚未匯出 Azure AD 連接器空間中的物件。 |
| MigratedCheckDetailsForMoreInfo |記錄項目建立於組建 1.0.9125.0 之前，並且以其舊版的狀態顯示。 |

## <a name="scripts-to-help-troubleshooting"></a>協助疑難排解的指令碼

### <a name="get-the-status-of-password-sync-settings"></a>取得密碼同步設定的狀態
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>觸發所有密碼的完整同步處理
> [!NOTE]
> 您應該只執行此指令碼一次。 如果您需要執行多次，則表示問題是出在其他地方。 請連絡 Microsoft 支援服務來協助進行問題疑難排解。

您可以使用下列指令碼，對所有密碼觸發完整同步處理︰

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>後續步驟
* [使用 Azure AD Connect 同步處理實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

