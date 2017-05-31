---
title: "Azure AD Connect：針對傳遞驗證進行疑難排解 | Microsoft Docs"
description: "本文會說明如何針對 Azure Active Directory (Azure AD) 傳遞驗證進行疑難排解。"
services: active-directory
keywords: "針對 Azure AD Connect 傳遞驗證進行疑難排解, 安裝 Active Directory, Azure AD, SSO, 單一登入的必要元件"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>如何針對 Azure Active Directory 傳遞驗證進行疑難排解

本文可幫助您尋找在安裝、註冊或解除安裝傳遞驗證連接器 (透過 Azure AD Connect 或獨立連接器) 期間所遇到之常見問題的疑難排解資訊。 以及在租用戶上啟用和操作 Azure Active Directory (Azure AD) 傳遞驗證功能期間。

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>安裝連接器 (透過 Azure AD Connect 或獨立連接器) 期間的問題

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD 應用程式 Proxy 連接器已經存在

傳遞驗證連接器無法和 [Azure AD 應用程式 Proxy](../../active-directory/active-directory-application-proxy-get-started.md) 連接器安裝在同一部伺服器上。 您必須在不同的伺服器上安裝傳遞驗證連接器。

### <a name="an-unexpected-error-occured"></a>發生意外的錯誤

從伺服器[收集連接器記錄檔](#collecting-pass-through-authentication-connector-logs)，並連絡 Microsoft 支援服務解決您的問題。

## <a name="issues-during-registration-of-connectors"></a>註冊連接器期間發生的問題

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>由於連接埠遭到封鎖，而使註冊連接器連線失敗

確認已安裝連接器的伺服器能與我們的服務 URL 和連接埠通訊，如[這裡](active-directory-aadconnect-pass-through-authentication.md#prerequisites)所列。

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>因為權杖或帳戶授權錯誤，註冊連接器失敗

請確定您在所有 Azure AD Connect 或獨立連接器安裝和註冊作業中，使用僅限雲端的全域管理員帳戶。 啟用 MFA 的全域管理員帳戶有一個已知的問題，請暫時關閉 MFA (只是為了完成作業) 作為因應措施。

### <a name="an-unexpected-error-occurred"></a>發生意外的錯誤

從伺服器[收集連接器記錄檔](#collecting-pass-through-authentication-connector-logs)，並連絡 Microsoft 支援服務解決您的問題。

## <a name="issues-during-uninstallation-of-connectors"></a>解除安裝連接器期間發生的問題

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>解除安裝 Azure AD Connect 時出現的警告訊息

如果您已在租用戶上啟用傳遞驗證，但您嘗試解除安裝 Azure AD Connect，其將會顯示下列警告訊息：「除非您在其他伺服器上有安裝其他傳遞驗證代理程式，否則使用者將無法登入 Azure AD」。

在您解除安裝 Azure AD Connect 之前，您必須完成[高可用性](active-directory-aadconnect-pass-through-authentication.md)設定以避免中斷使用者登入。

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>啟用傳遞驗證功能時發生的問題

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>因為沒有任何可用的連接器，而使啟用功能失敗

您必須至少有一個使用中的連接器，才能在租用戶上啟用傳遞驗證。 您可以安裝 Azure AD Connect 或獨立連接器來安裝連接器。

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>因為連接埠遭到封鎖，而使功能啟用失敗

確認已安裝 Azure AD Connect 的伺服器能與我們的服務 URL 和連接埠通訊，如[這裡](active-directory-aadconnect-pass-through-authentication.md#prerequisites)所列。

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>因為權杖或帳戶授權錯誤，啟用功能失敗

請確定您使用僅限雲端的全域管理員帳戶來啟用此功能。 啟用 Multi-Factor Authentication (MFA) 的全域管理員帳戶有一個已知的問題，請暫時關閉 MFA (只是為了完成作業) 作為因應措施。

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>操作傳遞驗證功能時發生的問題

### <a name="user-facing-sign-in-errors"></a>使用者對應登入錯誤

功能在 Azure AD 登入畫面上報告下列使用者對應錯誤。 其連同適當的解決步驟詳述如下。

|錯誤|說明|解決方案
| --- | --- | ---
|AADSTS80001|無法連線至 Active Directory|確定連接器伺服器和必須驗證其密碼的使用者都是相同 AD 樹系的成員，而且都能連線到 Active Directory。  
|AADSTS8002|連線至 Active Directory 時發生逾時|請檢查以確定 Active Directory 可用，並且會回應來自連接器的要求。
|AADSTS80004|傳遞給連接器的使用者名稱無效|請確定使用者嘗試用來登入的使用者名稱正確無誤。
|AADSTS80005|驗證發生無法預期的 WebException|這可能是暫時性的錯誤。 重試要求。 如果持續發生失敗，請連絡 Microsoft 支援服務。
|AADSTS80007|和 Active Directory 通訊時發生錯誤|請檢查連接器記錄檔以了解詳細資訊，並確認 Active Directory 如預期般運作。

## <a name="collecting-pass-through-authentication-connector-logs"></a>收集傳遞驗證連接器記錄

根據發生的問題類型，您必須在不同位置中查看傳遞驗證連接器記錄。

### <a name="connector-event-logs"></a>連接器事件記錄檔

如需與連接器有關的錯誤，可開啟伺服器上的 [事件檢視器] 應用程式，並查看 [應用程式及服務記錄檔\Microsoft\AadApplicationProxy\Connector\Admin] 底下。

如需詳細的分析和偵錯記錄檔，您可以啟用「工作階段」記錄檔。 在正常作業期間，請勿在此記錄檔啟用時執行連接器。這只適用於進行疑難排解。 請注意，只有再次停用此記錄檔之後，才能看見記錄檔的內容。

### <a name="detailed-trace-logs"></a>詳細的追蹤記錄檔

若要對使用者登入失敗進行疑難排解，請查看追蹤記錄檔，位於：**C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**。 這些記錄包含使用傳遞驗證功能的特定使用者為什麼會登入失敗的原因。 以下是範例記錄檔項目︰

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

您可以開啟命令提示字元並執行下列命令，以取得錯誤 (上述範例為 '1328') 的描述性詳細資料。 注意︰您必須使用您在記錄檔中看到的實際錯誤號碼取代 '1328'。

`Net helpmsg 1328`

結果應該會看起來像這樣：

![傳遞驗證](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>網域控制站記錄檔

如果已經啟用稽核記錄，您可以在網域控制站的安全性記錄檔中找到其他資訊。 查詢傳遞驗證連接器所傳送登入要求的簡單方式如下所示︰

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

