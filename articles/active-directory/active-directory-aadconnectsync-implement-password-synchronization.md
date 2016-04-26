<properties
	pageTitle="使用 Azure AD Connect 同步處理實作密碼同步處理 | Microsoft Azure"
	description="提供有關密碼同步處理如何運作以及如何加以啟用的資訊。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/15/2016"
	ms.author="markusvi;andkjell"/>


# 使用 Azure AD Connect 同步處理實作密碼同步處理

透過密碼同步處理，您的內部部署 Active Directory 密碼也可以用來登入 Azure Active Directory。

本主題提供在您的環境中啟用及針對密碼同步處理所需的資訊進行疑難排解。


## 什麼是密碼同步處理

密碼同步處理是用來將使用者密碼從內部部署 Active Directory 同步處理至雲端式 Azure Active Directory (Azure AD) 的功能。此功能可讓您以用來登入您內部部署網路的相同密碼登入 Azure Active Directory 服務 (如 Office 365、Microsoft Intune 和 CRM Online)。若要使用此功能，您需要安裝 Azure Active Directory Connect 同步處理服務 (Azure AD Connect 同步處理)。


> [AZURE.NOTE] 如需針對 FIPS 和密碼同步處理設定的 Active Directory 網域服務的詳細資訊，請參閱[密碼同步處理和 FIPS](#password-synchronization-and-fips)。

## 密碼同步處理如何運作

密碼同步處理是 Azure AD Connect 同步處理實作的目錄同步作業功能的延伸。這項功能需要設定您的內部部署 AD 與 Azure Active Directory 之間的目錄同步作業。

Active Directory 網域服務是以代表使用者實際密碼的雜湊值格式儲存密碼。您無法使用密碼雜湊來執行下列動作︰

- 登入您的內部部署網路

- 將它還原成純文字版本的密碼

為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 擷取您的密碼雜湊。密碼在同步到 Azure Active Directory 驗證服務之前，會先套用額外的安全性處理。密碼同步處理程序的實際資料流程，就類似同步處理 DisplayName 或電子郵件地址等使用者資料。

密碼的同步處理原則如下︰

- 以每個使用者為基礎

- 依時間順序

- 比其他屬性的標準目錄同步作業週期更加頻繁

同步處理後的密碼會覆寫現有的雲端密碼。

第一次啟用密碼同步功能時，它會對範圍內的所有使用者執行初次的密碼同步處理。您無法明確定義一小組要同步處理的使用者密碼。

當您變更內部部署密碼時，更新後的密碼將會進行同步處理，而這個動作大多會在幾分鐘內完成。密碼同步處理功能會自動重試失敗的使用者密碼同步。若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。在您登入雲端服務時，若系統同步處理了密碼變更，您的雲端服務工作階段並不會立即受此作業影響。不過，一旦雲端服務要求您重新驗證，您就需要提供新的密碼。

> [AZURE.NOTE] 只有 Active Directory 的物件類型使用者才支援密碼同步。不支援 iNetOrgPerson 物件類型。

### 密碼同步處理對 Azure AD 網域服務的運作方式

如果您在 Azure AD 中啟用這項服務，則需要密碼同步處理選項，才能獲得單一登入體驗。啟用此服務時，密碼同步處理的行為會變更，而且也會從您的內部部署 Active Directory 依原樣同步處理密碼雜湊到 Azure AD 網域服務。此功能類似於 Active Directory 遷移工具 (ADMT)，並可讓 Azure AD 網域服務能夠以內部部署 AD 中可用的所有方法驗證使用者。

### 安全性考量

同步密碼的時候，純文字版本的密碼不會向密碼同步處理功能、Azure AD 或任何相關服務公開。

此外，內部部署 Active Directory 不需使用可反轉加密的格式儲存密碼。Active Directory 密碼雜湊的摘要會用於內部部署 AD 和 Azure Active Directory 之間的傳輸。密碼雜湊的摘要無法用來存取內部部署環境內的資源。

### 密碼原則考量

啟用密碼同步處理會影響兩種類型的密碼原則：

1. 密碼複雜性原則
2. 密碼到期原則

**密碼複雜性原則**

當您啟用密碼同步處理之後，內部部署 Active Directory 中設定的密碼複雜性原則，會覆寫任何可能針對已同步使用者在雲端定義的複雜性原則。這表示任何在客戶內部部署 Active Directory 環境中有效的密碼，都可用來存取 Azure AD 服務。

> [AZURE.NOTE] 直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

**密碼到期原則**

如果使用者在密碼同步處理範圍內，雲端帳戶的密碼會設定為「永不過期」。這表示您可以繼續使用已在您的內部部署環境中過期的同步處理密碼登入雲端服務。

您的雲端密碼會於下一次您在內部部署環境中變更密碼時更新。

### 覆寫已同步的密碼

系統管理員可以使用 PowerShell 手動重設您的密碼。

在此情況下，新的密碼會覆寫您已同步處理的密碼，且雲端中定義的所有密碼原則都會套用到新的密碼。

如果您再次變更內部部署密碼，則新密碼會同步到雲端並覆寫手動更新的密碼。


## 啟用密碼同步處理

若要啟用密碼同步處理，您有兩個選項︰

- 如果您在安裝 Azure AD Connect 時使用快速設定，則會依預設啟用密碼同步處理。

- 如果您在安裝 Azure AD Connect 時使用自訂設定，則必須在使用者登入頁面上啟用密碼同步處理。

<br> ![啟用密碼同步處理](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png) <br>

如果您選擇使用 [與 AD FS 同盟]，則可以選擇性地啟用密碼同步，做為 AD FS 基礎結構失敗時的備用方式。如果您打算使用 Azure AD 網域服務，您也可以啟用它。

### 密碼同步處理和 FIPS

如果已經根據 FIPS (美國聯邦資訊處理標準) 鎖定您的伺服器，則已停用 MD5。若要啟用此密碼同步處理，請在 C:\\Program Files\\Azure AD Sync\\Bin 的 miiserver.exe.config 中加入 enforceFIPSPolicy 索引鍵。

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

可以在組態檔的結尾找到執行階段組態/節點。

如需安全性和 FIPS 的詳細資訊，請參閱 [AAD 密碼同步、加密和 FIPS 法規遵循](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)


## 針對密碼同步處理進行疑難排解

**若要針對密碼同步處理進行疑難排解，請執行下列步驟︰**

1. 開啟 [同步處理服務管理員]。

2. 按一下 [連接器]。

3. 選取使用者所位於的 Active Directory 連接器。

4. 選取 [搜尋連接器空間]。

5. 找出您要尋找的使用者。

6. 選取 [歷程] 索引標籤，並確認至少有一個同步規則的 [密碼同步] 顯示為 [True]。在預設組態中，同步規則的名稱是 **In from AD - User AccountEnabled**。

    ![使用者的相關歷程資訊](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. 您也應該透過 Metaverse [跟隨著使用者](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system)一直到 Azure AD 連接器空間。連接器空間物件的輸出規則應該將 [密碼同步處理] 設為 [True]。在預設組態中，同步規則的名稱是 **Out to AAD - User Join**。

    ![使用者的連接器空間屬性](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. 若要查看物件的密碼同步詳細資料，請按一下 [記錄...] 按鈕。<br> 這將會建立具有過去一週的使用者密碼同步狀態歷程記錄檢視的頁面。

    ![取得記錄檔詳細資料](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

[狀態] 欄可以有下列值︰

| 狀態 | 說明 |
| ---- | ----- |
| 成功 | 已成功同步處理密碼。 |
| FilteredByTarget | 密碼會設為 [使用者必須在下次登入時變更密碼]。未同步處理密碼。 |
| NoTargetConnection | Metaverse 或 Azure AD 連接器空間中沒有任何物件。 |
| SourceConnectorNotPresent | 在內部部署 Active Directory 連接器空間中找不到任何物件。 |
| TargetNotExportedToDirectory | 尚未匯出 Azure AD 連接器空間中的物件。 |
| MigratedCheckDetailsForMoreInfo | 記錄項目建立於組建 1.0.9125.0 之前，並且以其舊版的狀態顯示。 |


## 觸發所有密碼的完整同步處理

一般而言，並不需要強制所有密碼進行完整同步處理。<br> 但若有必要，您可以使用下列指令碼對所有密碼觸發完整同步處理作業︰

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## 後續步驟

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0420_2016-->