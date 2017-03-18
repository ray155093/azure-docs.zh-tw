---
title: "Azure Active Directory Connect 常見問題集 - | Microsoft Docs"
description: "此頁面包含有關 Azure AD Connect 的常見問題集。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c22a8f4a895efc86abc328c6cf82685d7db8c19c
ms.openlocfilehash: 33de5839e1e8fa70f75636488a0769f7aebf8b95
ms.lasthandoff: 02/24/2017


---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Azure Active Directory Connect 的常見問題集

## <a name="general-installation"></a>一般安裝
**問：如果 Azure AD 全域管理員已啟用 2FA，安裝是否能夠運作？**  
從 2016 年 2 月的組建開始便提供這項支援。

**問：是否有方法可自動安裝 Azure AD Connect？**  
它僅支援使用安裝精靈來安裝 Azure AD Connect。 不支援自動和無訊息安裝。

**問：我有一個樹系，但無法連線到其中的網域。如何安裝 Azure AD Connect？**  
從 2016 年 2 月的組建開始便提供這項支援。

**問︰AD DS 健康情況代理程式是否是在伺服器核心上運作？**  
是。 安裝代理程式之後，您可以使用下列 PowerShell Commandlet 來完成註冊程序︰ 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>網路
**問：我的防火牆、網路裝置或其他軟硬體會限制在網路上開啟連線的時間上限。使用 Azure AD Connect 時，用戶端逾時閥值時間應該多長？**  
所有網路軟體、實體裝置或其他軟硬體限制連線開啟時間上限的閥值應該至少為 5 分鐘 (300 秒)，以便讓安裝 Azure AD Connect 用戶端的伺服器與 Azure Active Directory 連線。 這也適用於所有先前發行的  Microsoft Identity 同步處理工具。

**問：是否支援 SLD (單一標籤網域)？**  
否，Azure AD Connect 不支援使用 SLD 的內部部署樹系/網域。

**問：是否支援有句點的 NetBios 名稱？**  
否，Azure AD Connect 不支援 NetBios 名稱包含句點的內部部署樹系/網域。

## <a name="federation"></a>同盟
**問：如果我收到一封電子郵件，要求我更新我的 Office 365 憑證，該怎麼辦？**  
請參考 [更新憑證](active-directory-aadconnect-o365-certs.md) 主題中概述的指導方針來了解如何更新憑證。

**問：我已經針對 O365 信賴憑證者設定「自動更新信賴憑證者」。當我的權杖簽署憑證自動換用時，需要採取任何動作嗎？**  
請參考 [更新憑證](active-directory-aadconnect-o365-certs.md)一文中概述的指導方針。

## <a name="environment"></a>Environment
**問：安裝 Azure AD Connect 之後，是否支援重新命名伺服器？**  
否。 變更伺服器名稱將會導致同步處理引擎無法連接到 SQL 資料庫，服務將無法啟動。

## <a name="identity-data"></a>身分識別資料
**問：Azure AD 中的 UPN (userPrincipalName) 屬性不符合內部部署的 UPN，為什麼？**  
請參閱以下文章：

* [Office 365、Azure 或 Intune 中的使用者名稱不符合內部部署的 UPN 或替代登入識別碼](https://support.microsoft.com/en-us/kb/2523192)
* [在您將使用者帳戶的 UPN 變更為使用不同的同盟網域後，Azure Active Directory 同步作業工具未同步處理變更](https://support.microsoft.com/en-us/kb/2669550)

您也可以將 Azure AD 設定為允許同步處理引擎更新 userPrincipalName，如 [Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md)中所述。

**問：是否支援將內部部署 AD「群組/連絡人」物件與現有的 Azure AD「群組/連絡人」物件進行大致相符比對？**  
否，目前不支援。

**問：是否支援將現有 Azure AD「群組/連絡人」物件上的 ImmutableId 屬性手動設定成與內部部署 AD「群組/連絡人」物件完全相符？**  
否，目前不支援。

## <a name="security"></a>安全性
**問︰帳戶會在特定次數的嘗試失敗之後鎖定，或者是否使用更複雜的策略？**</br>
我們使用更複雜的策略來鎖定帳戶。  此策略是以要求的 IP 和所輸入的密碼為基礎。 鎖定持續期間也會根據它是攻擊的可能性而加長。  

**問︰有些 (通用) 密碼遭到拒絕並出現「此密碼已使用許多次」訊息，這是指使用於目前 Active Directory 的密碼嗎？**</br>
這是指全域通用的密碼，例如 “Password” 和 “123456” 的任何變體。

**問︰來自可疑來源 (殭屍網路、Tor 端點) 的登入要求是否會在 B2C 租用戶中遭到封鎖，或者需要基本或進階版本租用戶？**</br>
我們沒有適用於所有 B2C 租用戶的閘道，可篩選要求並提供殭屍網路的一些防護功能。 

## <a name="custom-configuration"></a>自訂組態
**問：Azure AD Connect 適用的 PowerShell Cmdlet 記載於何處？**  
除了記載於本網站上的 Cmdlet，在 Azure AD Connect 中找到的其他 PowerShell Cmdlet 不支援客戶使用。

**問：我是否可以使用 *Synchronization Service Manager* 中的「伺服器匯出/伺服器匯入」，在伺服器之間移動組態？  
否。 此選項將不會擷取所有組態設定，因此不應使用。 您應該改用精靈在第二部伺服器上建立基底組態，並使用同步處理規則編輯器產生 PowerShell 指令碼，以在伺服器之間移動任何自訂規則。 請參閱[變換移轉](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)。

**問︰是否可針對 Azure 登入頁面密碼快取，而且是否可以因為它包含自動完成 ="false" 屬性的密碼輸入元素而避免此行為？**</br>
我們目前不支援修改密碼輸入欄位的 HTML 屬性，包括自動完成標記。 我們目前正在開發適用於自訂 javascript 的功能，可讓您將任何屬性新增密碼欄位。 應可在 2017 年下半年使用這項功能。

**問︰在 Azure 登入頁面上，會顯示先前登入成功之使用者的使用者名稱。可以關閉此行為嗎？**</br>
我們目前不支援修改登入頁面的 HTML 屬性。 我們目前正在開發適用於自訂 javascript 的功能，可讓您將任何屬性新增密碼欄位。 應可在 2017 年下半年使用這項功能。

**問︰是否辦法避免並行的工作階段？**</br>
否。



## <a name="troubleshooting"></a>疑難排解
**問：如何取得 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* 在 Microsoft 知識庫 (KB) 中搜尋有關 Azure AD Connect 支援的常見協助修正問題的技術解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 按一下 [這裡](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，即可在社群中搜尋和瀏覽技術問題和答案，或提出自己的問題。

[Azure AD Connect 客戶支援](https://manage.windowsazure.com/?getsupport=true)

* 使用此連結透過 Azure 入口網站取得支援。


