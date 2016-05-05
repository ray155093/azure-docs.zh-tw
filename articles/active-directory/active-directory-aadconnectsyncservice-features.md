<properties
	pageTitle="Azure AD Connect 同步處理服務功能與組態 | Microsoft Azure"
	description="描述 Azure AD Connect 同步處理服務的服務端功能。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="andkjell;markusvi"/>

# Azure AD Connect 同步處理服務功能

Azure AD connect 同步處理功能有兩個元件：

- 名為 **Azure AD Connect 同步處理**的內部部署元件，也稱為**同步處理引擎**。
- 位於 Azure AD 的服務，也稱為 **Azure AD Connect 同步處理服務**

本主題說明 **Azure AD Connect 同步處理服務**的下列功能如何運作，以及如何使用 Windows PowerShell 進行設定。

這些設定會由[適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組](http://aka.ms/aadposh)進行設定，而您必須從 Azure AD Connect 個別下載並安裝此模組，才能加以設定。說明的 Cmdlet 已導入 [2016 年 3 月版本 (組建 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)。如果您沒有本主題說明的 Cmdlet，或它們未產生相同的結果，請確認您執行的是最新版本。

若要查看 Azure AD 目錄中的組態，請執行 `Get-MsolDirSyncFeatures`。![Get-MsolDirSyncFeatures 結果](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

其中許多設定只能由 Azure AD Connect 進行變更。

以下是可在 `Set-MsolDirSyncFeature` 中配置的設定：

| DirSyncFeature | 註解 |
| --- | --- |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | 如果屬性是另一個物件的複本，即會將該屬性隔離，而不會在匯出期間導致整個物件失敗。 |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) | 不只主要 SMTP 位址，還允許物件加入 userPrincipalName。 |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | 可讓同步處理引擎更新受管理/授權 (非同盟) 使用者的 userPrincipalName 屬性。 |

啟用功能後，即無法將其停用。

下列設定是由 Azure AD Connect 所設定，而且無法由 `Set-MsolDirSyncFeature` 修改：

| DirSyncFeature | 註解 |
| --- | --- |
| DeviceWriteback | [Azure AD Connect：啟用裝置回寫](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions | [Azure AD Connect 同步處理：目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md) |
| PasswordSync | [使用 Azure AD Connect 同步處理實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md) |
| UnifiedGroupWriteback | [預覽：群組回寫](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback | 目前不支援。 |

## 重複屬性恢復功能
含重複 UPNs / proxyAddresses 的物件，會將該屬性「隔離」，並視需要指派暫時的值，而不會讓佈建物件失敗。解決衝突時，會自動將暫時 UPN 修正為適當的值。您可以分別啟用 UPN 和 proxyAddress 的這項行為。如需詳細資訊，請參閱[身分識別同步處理和重複屬性恢復功能](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)。

## UserPrincipalName 大致比對
啟用這項功能時，會針對 UPN 以及[主要 SMTP 位址](https://support.microsoft.com/kb/2641663)套用大致比對，並一律啟用。大致比對功能是用來針對 Azure AD 中現有的雲端使用者與內部部署使用者進行比對。

如果您需要比對內部部署 AD 帳戶與雲端中建立的現有帳戶，卻不能使用 Exchange Online 時，啟用此功能特別實用。在此案例中，您通常不需要在雲端中設定 SMTP 屬性。

在新建立的 Azure AD 目錄中，預設會開啟這項功能。您可以執行下列項目，查看是否已啟用此功能︰
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

如果您的 Azure AD 目錄未啟用這項功能，您可以執行下列項目加以啟用︰
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## 同步處理 userPrincipalName 更新
在過去，除非下列兩項條件都成立，否則皆會將透過內部部署使用同步處理服務的 UserPrincipalName 屬性更新封鎖：

- 使用者受到管理 (非同盟)
- 使用者尚未指派授權。

如需詳細資訊，請參閱 [Office 365、Azure 或 Intune 中的使用者名稱不符合內部部署的 UPN 或替代登入識別碼](https://support.microsoft.com/kb/2523192)。

除非您使用同盟的網域，否則啟用此功能可讓同步處理引擎在內部部署變更 userPrincipalName 時加以更新。

在新建立的 Azure AD 目錄中，預設會開啟這項功能。您可以執行下列項目，查看是否已啟用此功能︰
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

如果您的 Azure AD 目錄未啟用這項功能，您可以執行下列項目加以啟用︰
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

啟用這項功能之後，現有的 userPrincipalName 值會保持不變。下次 userPrincipalName 屬性的內部部署變更時，使用者的一般差異同步處理即會更新 UPN。

## 未來的變更
這些設定將在未來所有 Azure AD 目錄中啟用。

## 另請參閱

- [Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)

- [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0420_2016-->