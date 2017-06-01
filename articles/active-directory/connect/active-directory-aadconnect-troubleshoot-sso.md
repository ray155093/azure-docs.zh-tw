---
title: "Azure AD Connect：針對順暢單一登入進行疑難排解 | Microsoft Docs"
description: "本主題說明如何針對 Azure Active Directory 順暢單一登入 (Azure AD 順暢 SSO) 進行疑難排解。"
services: active-directory
keywords: "何謂 Azure AD Connect、安裝 Active Directory、Azure AD、SSO、單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: zh-tw
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>如何針對 Azure Active Directory 順暢單一登入進行疑難排解

## <a name="known-issues"></a>已知問題

- 如果您正使用 Azure AD Connect 同步處理 30 多個 AD 樹系，則用來設定順暢 SSO 的精靈無法正常運作。 您可以在租用戶上[手動啟用](#manual-reset-of-azure-ad-seamless-sso)順暢 SSO 功能，以解決這個問題。
- 將 Azure AD 服務 URL (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) 新增至「信任的網站」區域，而非「近端內部網路」區域。

## <a name="troubleshooting-checklist"></a>疑難排解檢查清單

使用下列檢查清單來針對 Azure AD 順暢 SSO 進行疑難排解︰

1. 在 Azure AD Connect 工具上檢查租用戶是否已啟用順暢 SSO 功能。 如果您無法啟用此功能 (例如，因為連接埠已封鎖)，請確定您已完成所有[必要條件](active-directory-aadconnect-sso.md#pre-requisites)。 如果您仍無法啟用此功能，請連絡 Microsoft 支援服務。
2. 兩個服務 URL (https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net) 已定義到內部網路區域設定中。
3. 確定公司桌面已加入 AD 網域。
4. 確定使用者是使用 AD 網域帳戶來登入桌面。
5. 確定使用者帳戶是來自已設定順暢 SSO 的 AD 樹系。
6. 確定桌面已連線到公司網路。
7. 確定桌面的時間已經與 Active Directory 和網域控制站的時間同步，且彼此的時間差不到 5 分鐘。
8. 從其桌面清除現有的 Kerberos 票證。 這可透過從命令提示字元執行 **klist purge** 命令來完成。 使用者的 Kerberos 票證的有效期通常為 12 個小時；請注意，您可以在 Active Directory 中以不同的方式設定它。
9. 檢閱瀏覽器的主控台記錄 (在 [開發人員工具] 底下)，以協助確定可能的問題。
10. 同時檢閱[網域控制站記錄](#domain-controller-logs)。

### <a name="domain-controller-logs"></a>網域控制站記錄

如果網域控制站已啟用成功稽核，則每次使用者使用順暢 SSO 登入時，系統都會在事件記錄中記錄一個安全性項目 (與電腦帳戶 **AzureADSSOAcc$** 相關聯的事件 4769)。 您可以使用下列查詢來尋找這些安全性事件︰

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>手動重設 Azure AD 順暢 SSO

如果疑難排解無法解決問題，請使用下列步驟，在您的租用戶上手動重設 / 啟用此功能︰

### <a name="1-import-the-seamless-sso-powershell-module"></a>1.匯入流暢 SSO PowerShell 模組

- 首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。
- 接著下載並安裝 [適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。
- 瀏覽到 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
- 使用此命令匯入順暢 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2.取得已啟用順暢 SSO 的 AD 樹系清單

- 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 這會提供一個快顯視窗，以便輸入 Azure AD 租用戶系統管理員認證。
- 呼叫 `Get-AzureADSSOStatus`。 這會提供已啟用這項功能的 AD 樹系清單 (尋找「網域」清單)。

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3.針對已設定順暢 SSO 的每個 AD 樹系停用該功能

- 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 這會提供一個快顯視窗，以便輸入 Azure AD 租用戶系統管理員認證。
- 呼叫 `$creds = Get-Credential`。 這會提供一個快顯視窗，以便輸入預定 Azure AD 樹系的網域系統管理員認證。
- 呼叫 `Disable-AzureADSSOForest -OnPremCredentials $creds`。 這會同時從內部部署 DC 移除 AZUREADSSOACCT 電腦帳戶，以及停用此特定的 AD 樹系的這項功能。
- 針對您已設定此功能的每個 AD 樹系，重複上述步驟。

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4.為每個 AD 樹系啟用順暢 SSO

- 呼叫 `New-AzureADSSOAuthenticationContext`。 這會提供一個快顯視窗，以便輸入 Azure AD 租用戶系統管理員認證。
- 呼叫 `Enable-AzureADSSOForest`。 這會提供一個快顯視窗，以便輸入預定 Azure AD 樹系的網域系統管理員認證。
- 針對您想要設定此功能的每個 AD 樹系，重複上述步驟。

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5.在您的租用戶上啟用順暢 SSO

- 呼叫 `New-AzureADSSOAuthenticationContext`。 這會提供一個快顯視窗，以便輸入 Azure AD 租用戶系統管理員認證。
- 呼叫 `Enable-AzureADSSO` 並且在 `Enable: ` 提示字元輸入"true"，以在租用戶中開啟該功能。

