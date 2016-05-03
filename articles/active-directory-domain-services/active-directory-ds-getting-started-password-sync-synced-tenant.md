<properties
	pageTitle="Azure AD 網域服務︰啟用密碼同步處理 | Microsoft Azure"
	description="開始使用 Azure Active Directory 網域服務"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽) - 啟用 Azure AD 網域服務的密碼同步處理

## 工作 5︰為同步處理的 Azure AD 租用戶啟用 AAD 網域服務的密碼同步處理
一旦您已針對 Azure AD 目錄啟用 Azure AD 網域服務之後，下一個工作就是啟用 Azure AD 網域服務的密碼同步處理。這讓使用者能夠使用他們的公司認證來登入網域。

所涉及的步驟會根據組織擁有僅限雲端的 Azure AD 目錄，或已設定為使用 Azure AD Connect 來與內部部署目錄同步處理而有所不同。

<br>

> [AZURE.SELECTOR]
- [僅限雲端的 Azure AD 目錄](active-directory-ds-getting-started-password-sync.md)
- [同步處理的 Azure AD 目錄](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### 同步處理的租用戶 - 能夠將 NTLM 和 Kerberos 認證雜湊同步處理到 Azure AD
如果貴組織的 Azure AD 租用戶已設定為使用Azure AD Connect 來同步處理內部部署目錄，則您必須設定 Azure AD Connect，來同步處理 NTLM 和 Kerberos 驗證所需的認證雜湊。這些雜湊預設不會同步處理到 Azure AD，而下列步驟可讓您將雜湊同步處理到 Azure AD 租用戶。

#### 安裝或更新 Azure AD Connect

您必須在已加入網域的電腦上安裝建議的最新 Azure AD Connect 版本。如果您目前已設定 Azure AD Connect 的執行個體，就必須更新它來使用 Azure AD Connect GA 組建。請確定您使用的是最新的 Azure AD Connect 版本，以免已知問題/錯誤可能已修復。

**[下載 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

建議版本：**1.1.130.0** - 已於 2016 年 4 月 12 日發佈。

  > [AZURE.WARNING] 您「必須」安裝建議的最新 Azure AD Connect 版本，才能將傳統密碼認證 (NTLM 和 Kerberos 驗證所需的認證) 同步處理到 Azure AD 租用戶。此功能無法在舊版的 Azure AD Connect 中使用，或與舊版 DirSync 工具搭配使用。

Azure AD Connect 的安裝指示可於下列文章中取得：[開始使用 Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### 強制執行與 Azure AD 的完整密碼同步處理

若要強制執行完整密碼同步處理並啟用所有的內部部署使用者的密碼雜湊 (包括 NTLM/Kerberos 驗證所需的認證雜湊) 以同步處理到您的 Azure AD 租用戶，請在每個 AD 樹系上執行下列 PowerShell 指令碼。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

視目錄的大小而定 (使用者的數目、群組等)，將認證同步處理到 Azure AD 需要花一點時間。將認證雜湊同步處理到 Azure AD 之後，密碼短時間內就能在 Azure AD 網域服務管理的網域上使用。


<br>

## 相關內容

- [為僅限雲端的 Azure AD 目錄啟用 AAD 網域服務的密碼同步處理](active-directory-ds-getting-started-password-sync.md)

- [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)

- [將 Windows 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-windows-vm.md)

- [將 Red Hat Enterprise Linux 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->