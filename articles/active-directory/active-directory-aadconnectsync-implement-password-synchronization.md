<properties
	pageTitle="使用 Azure AD Connect 同步處理實作密碼同步處理 | Microsoft Azure"
	description="提供有關密碼同步處理如何運作以及如何加以啟用的資訊。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="markusvi;andkjell"/>


# 使用 Azure AD Connect 同步處理實作密碼同步處理
本主題提供您所需資訊，以讓您將使用者密碼從內部部署 Active Directory (AD) 同步處理至雲端式 Azure Active Directory (Azure AD)。

## 什麼是密碼同步處理
您因為忘記密碼而無法完成工作的機率，與您必須記住的不同密碼數目有關。必須記住的密碼越多，將其中之一遺忘的機率就越高。關於密碼重設和其他密碼相關問題的疑問和來電，佔據了最多的技術服務資源。

密碼同步處理是用來將使用者密碼從內部部署 Active Directory 同步處理至雲端式 Azure Active Directory (Azure AD) 的功能。使用此功能即可用和登入內部部署 Active Directory 的相同密碼，登入 Azure Active Directory 服務 (如 Office 365、Microsoft Intune、CRM Online 和 Azure AD 網域服務)。

![何謂 Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

密碼同步處理可透過將使用者需要維護的密碼數目減少到只剩一個，協助您︰

- 提升使用者的生產力
- 降低技術支援成本

此外，如果您選擇使用[**與 AD FS 同盟**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)，則可以選擇性地啟用密碼同步處理，做為 AD FS 基礎結構失敗時的備用方式。

密碼同步處理是 Azure AD Connect 同步處理實作的目錄同步作業功能的延伸。若要在環境中使用密碼同步處理，您需要︰

- 安裝 Azure AD Connect。
- 設定您的內部部署 AD 與 Azure Active Directory 之間的目錄同步作業
- 啟用密碼同步處理

如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] 如需針對 FIPS 和密碼同步處理設定的 Active Directory 網域服務的詳細資訊，請參閱[密碼同步處理和 FIPS](#password-synchronization-and-fips)。

## 密碼同步處理如何運作
Active Directory 網域服務是以代表使用者實際密碼的雜湊值格式儲存密碼。雜湊值是單向數學函式 (「雜湊演算法」) 的計算結果。沒有任何方法可將單向函式的結果還原為純文字版本的密碼。您無法使用密碼雜湊來登入您的內部部署網路。

為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 擷取您的密碼雜湊。在將密碼雜湊與 Azure Active Directory 驗證服務同步之前，會進行額外的安全性處理。密碼會以每個使用者為基礎，依照時間先後順序來進行同步處理。

密碼同步處理程序的實際資料流程，就類似同步處理 DisplayName 或電子郵件地址等使用者資料。不過，相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。密碼同步處理程序每 2 分鐘會執行一次。您無法修改此程序的執行頻率。當您同步處理密碼時，它便會覆寫現有的雲端密碼。

第一次啟用密碼同步功能時，它會對範圍內的所有使用者執行初次的密碼同步處理。您無法明確定義一小組要同步處理的使用者密碼。

當您變更內部部署密碼時，更新後的密碼將會進行同步處理，而這個動作大多會在幾分鐘內完成。密碼同步處理功能會自動重試失敗的同步處理嘗試。若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。目前的雲端服務工作階段不會立即受到同步處理的密碼變更之影響，會在您登入雲端服務時才會受到影響。不過，當雲端服務要求您重新驗證時，就需要提供新的密碼。

> [AZURE.NOTE] 只有 Active Directory 的物件類型使用者才支援密碼同步。不支援 iNetOrgPerson 物件類型。

### 密碼同步處理對 Azure AD 網域服務的運作方式
您也可以使用密碼同步處理功能，將內部部署密碼同步處理到 [Azure AD 網域服務](../active-directory-domain-services/active-directory-ds-overview.md)。此案例可讓 Azure AD 網域服務，以內部部署 AD 中所有可用的方法驗證雲端中的使用者。此案例的體驗類似於在內部部署環境中使用 Active Directory 遷移工具 (ADMT)。

### 安全性考量
同步密碼的時候，純文字版本的密碼不會向密碼同步處理功能、Azure AD 或任何相關服務公開。

此外，內部部署 Active Directory 不需使用可反轉加密的格式儲存密碼。Active Directory 密碼雜湊的摘要會用於內部部署 AD 和 Azure Active Directory 之間的傳輸。密碼雜湊的摘要無法用來存取內部部署環境內的資源。

### 密碼原則考量
啟用密碼同步處理會影響兩種類型的密碼原則：

1. 密碼複雜性原則
2. 密碼到期原則

**密碼複雜性原則** 當您啟用密碼同步處理時，在內部部署 Active Directory 中的密碼複雜性原則，會覆寫已同步處理的使用者在雲端中的複雜性原則。您可以使用內部部署 Active Directory 的所有有效密碼，來存取 Azure AD 服務。

> [AZURE.NOTE] 直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

**密碼到期原則** 如果使用者位於密碼同步處理範圍內，則雲端帳戶的密碼會設為「永不到期」。您可以使用內部部署環境中已過期的同步處理密碼，繼續登入雲端服務。您的雲端密碼會於下一次您在內部部署環境中變更密碼時更新。

### 覆寫已同步的密碼
系統管理員可以使用 Windows PowerShell 手動重設您的密碼。

在此情況下，新的密碼會覆寫您已同步處理的密碼，且雲端中定義的所有密碼原則都會套用到新的密碼。

如果您再次變更內部部署密碼，則新密碼會同步到雲端並覆寫手動更新的密碼。

## 啟用密碼同步處理
當您使用**快速設定**安裝 Azure AD Connect 時，便會自動啟用密碼同步處理。如需詳細資訊，請參閱[使用快速設定開始使用 Azure AD Connect](active-directory-aadconnect-get-started-express.md)。

如果您在安裝 Azure AD Connect 時使用自訂設定，則必須在使用者登入頁面上啟用密碼同步處理。如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。

![啟用密碼同步處理](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### 密碼同步處理和 FIPS
如果已經根據美國聯邦資訊處理標準 (FIPS) 鎖定您的伺服器，則已停用 MD5。

**若要針對密碼同步處理啟用 MD5，請執行下列步驟︰**

1. 移至 **%programfiles%\\Azure AD Sync\\Bin**。
2. 開啟 **miiserver.exe.config**。
3. 移至 **configuration/runtime** 節點 (位於檔案結尾)。
4. 新增下列節點︰`<enforceFIPSPolicy enabled="false"/>`
5. 儲存您的變更。

如需參考，此程式碼片段應如下所示︰

```
	<configuration>
		<runtime>
			<enforceFIPSPolicy enabled="false"/>
		</runtime>
	</configuration>
```

如需安全性和 FIPS 的詳細資訊，請參閱 [AAD 密碼同步、加密和 FIPS 法規遵循](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## 針對密碼同步處理進行疑難排解
如果密碼未如預期般同步處理，它可適用於部份的使用者或所有的使用者。

- 如果您有個別的物件問題，則查看[疑難排解未同步處理密碼的一個物件](#troubleshoot-one-object-that-is-not-synchronizing-passwords)。
- 如果有未同步處理任何密碼的問題，請參閱[疑難排解未同步處理任何密碼的問題](#troubleshoot-issues-where-no-passwords-are-synchronized)。

### 疑難排解未同步處理密碼的一個物件
您可以藉由檢閱物件的狀態，輕鬆地疑難排解密碼同步處理問題。

從 [Active Directory 使用者和電腦] 開始。尋找使用者並確認未選取 [使用者必須在下次登入時變更密碼]。![Active Directory 生產力密碼](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) 若已選取，則要求使用者登入並變更密碼。暫時密碼不會同步處理至 Azure AD。

如果 Active Directory 中正確，則下一步是在同步處理引擎中追蹤使用者。從內部部署 Active Directory 到 Azure AD 追蹤使用者，您可以查看物件是否有描述性錯誤。

1. 啟動 **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**。
2. 按一下 [連接器]。
3. 選取使用者所位於的 **Active Directory 連接器**。
4. 選取 [搜尋連接器空間]。
5. 找出您要尋找的使用者。
6. 選取 [歷程] 索引標籤，並確認至少有一個同步規則的 [密碼同步] 顯示為 [True]。在預設組態中，同步規則的名稱是 **In from AD - User AccountEnabled**。![使用者的相關歷程資訊](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. 然後[追蹤使用者](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)一直到 Azure AD 連接器空間。連接器空間物件的輸出規則應該將 [密碼同步] 設為 [True]。在預設組態中，同步規則的名稱是 **Out to AAD - User Join**。![使用者的連接器空間屬性](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. 若要查看過去一週物件的密碼同步詳細資料，請按一下 [記錄...]。![取得記錄檔詳細資料](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

[狀態] 欄可以有下列值︰

狀態 | 說明
---- | -----
成功 | 已成功同步處理密碼。
FilteredByTarget | 密碼會設為 [使用者必須在下次登入時變更密碼]。未同步處理密碼。
NoTargetConnection | Metaverse 或 Azure AD 連接器空間中沒有任何物件。
SourceConnectorNotPresent | 在內部部署 Active Directory 連接器空間中找不到任何物件。
TargetNotExportedToDirectory | 尚未匯出 Azure AD 連接器空間中的物件。
MigratedCheckDetailsForMoreInfo | 記錄項目建立於組建 1.0.9125.0 之前，並且以其舊版的狀態顯示。

### 疑難排解未同步處理任何密碼的問題
從執行[取得密碼同步設定的狀態](#get-the-status-of-password-sync-settings)一節中的指令碼開始。它可讓您大致了解密碼同步設定作業。![來自密碼同步處理設定的 PowerShell 指令碼輸出](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) 如果未在 Azure AD 中啟用此功能，或未啟用同步通道狀態，則執行 Connect 安裝精靈。選取 [自訂同步處理選項] 並取消選取密碼同步處理。這項變更會暫時停用此功能。然後再次執行精靈並重新啟用密碼同步處理。再次執行指令碼，確認組態正確無誤。

如果指令碼顯示沒有活動訊號，則執行[觸發所有密碼的完整同步處理](#trigger-a-full-sync-of-all-passwords)中的指令碼。此指令碼也可以用於其他情況：組態正確無誤，但未同步處理密碼。

#### 取得密碼同步設定的狀態

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

#### 觸發所有密碼的完整同步處理
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

## 後續步驟

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->