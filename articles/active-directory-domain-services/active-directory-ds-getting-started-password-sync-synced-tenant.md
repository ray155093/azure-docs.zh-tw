---
title: "Azure AD 網域服務︰啟用密碼同步處理 | Microsoft Docs"
description: "開始使用 Azure Active Directory 網域服務"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c43a831768684c6458d5f62557c8a06b2c5ca6b


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>啟用 Azure AD 網域服務的密碼同步處理
在先前工作中，您無法為 Azure AD 租用戶啟用 Azure AD 網域服務。 下一個工作是啟用 Azure AD 網域服務的密碼同步處理。 設定認證同步處理後，使用者即可使用他們的公司認證來登入受管理的網域。

根據組織有僅限雲端的 Azure AD 租用戶，或已設定為使用 Azure AD Connect 來與內部部署目錄同步處理，所需的步驟會有所不同。

<br>

> [!div class="op_single_selector"]
> * [僅限雲端的 Azure AD 租用戶](active-directory-ds-getting-started-password-sync.md)
> * [已同步處理的 Azure AD 租用戶](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>工作 5︰為同步處理的 Azure AD 租用戶啟用 AAD 網域服務的密碼同步處理
已同步處理的 Azure AD 租用戶會設定為使用 Azure AD Connect 來與您組織的內部部署目錄同步處理。 根據預設，Azure AD Connect 不會將 NTLM 和 Kerberos 認證雜湊同步處理到 Azure AD。 若要使用 Azure AD 網域服務，您需要設定 Azure AD Connect 以同步處理 NTLM 和 Kerberos 驗證所需的認證雜湊。 下列步驟能夠將必要的認證雜湊同步處理到 Azure AD 租用戶。

### <a name="install-or-update-azure-ad-connect"></a>安裝或更新 Azure AD Connect
在已加入網域的電腦上安裝建議的最新 Azure AD Connect 版本。 如果您目前已設定 Azure AD Connect 的執行個體，則必須加以更新才能使用最新版的 Azure AD Connect。 若要避免已知問題/錯誤可能已經修復，請確定您使用的是最新的 Azure AD Connect 版本。

**[下載 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

建議版本： **1.1.281.0** - 已於 2016 年 9 月 7 日發佈。

> [!WARNING]
> 您「必須」安裝建議的最新 Azure AD Connect 版本，才能將傳統密碼認證 (NTLM 和 Kerberos 驗證所需的認證) 同步處理到 Azure AD 租用戶。 此功能無法在舊版的 Azure AD Connect 中使用，或與舊版 DirSync 工具搭配使用。
> 
> 

Azure AD Connect 的安裝指示可於下列文章中取得： [開始使用 Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>能夠將 NTLM 和 Kerberos 認證雜湊同步處理到 Azure AD
在每個 AD 樹系上執行下列 PowerShell 指令碼，以強制執行完整密碼同步處理，並能夠讓所有內部部署使用者的密碼雜湊同步處理到 Azure AD 租用戶。 此指令碼能夠讓 NTLM/Kerberos 驗證所需的認證雜湊同步處理到 Azure AD 租用戶。

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

視目錄的大小而定 (使用者、群組等的數目)，將認證雜湊同步處理到 Azure AD 需要花一點時間。 將認證雜湊同步處理到 Azure AD 之後，密碼短時間內就能在 Azure AD 網域服務管理的網域上使用。

<br>

## <a name="related-content"></a>相關內容
* [為僅限雲端的 Azure AD 目錄啟用 AAD 網域服務的密碼同步處理](active-directory-ds-getting-started-password-sync.md)
* [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)
* [將 Windows 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-windows-vm.md)
* [將 Red Hat Enterprise Linux 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)




<!--HONumber=Dec16_HO2-->


