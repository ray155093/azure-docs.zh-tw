---
title: "針對已加入 Azure AD 網域之 Windows 10 和 Windows Server 2016 電腦的自動註冊進行疑難排解 | Microsoft Docs"
description: "針對已加入 Azure AD 網域之 Windows 10 和 Windows Server 2016 電腦的自動註冊進行疑難排解。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.contentlocale: zh-tw
ms.lasthandoff: 03/10/2017

---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>針對已加入 Azure AD 網域之電腦的自動註冊進行疑難排解 – Windows 10 和 Windows Server 2016

本主題適用於下列用戶端：

-   Windows 10
-   Windows Server 2016

針對其他 Windows 用戶端，請參閱[針對已加入 Azure AD 網域之 Windows 下層用戶端電腦的自動註冊進行疑難排解](active-directory-device-registration-troubleshoot-windows-legacy.md)。

本主題假設您已經依照[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-device-registration-get-started.md)所述，設定讓已加入網域的裝置自動註冊，以支援下列案例：

- [裝置型條件式存取](active-directory-conditional-access-automatic-device-registration-setup.md)

- [設定的企業漫遊](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello 企業版](active-directory-azureadjoin-passport-deployment.md)


本文件提供有關如何解決潛在問題的疑難排解指引。 

Windows 10 的 2015 年 11 月更新和更新版本皆支援註冊。  
建議使用「年度更新」來啟用上述案例。

## <a name="step-1-retrieve-the-registration-status"></a>步驟 1：擷取註冊狀態 

**擷取註冊狀態：**

1. 以系統管理員身分開啟命令提示字元。

2. 輸入 **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>步驟 2：評估註冊狀態 

請檢閱下列欄位，並確定它們具有預期的值：

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

此欄位顯示裝置是否已向 Azure AD 註冊。 如果值顯示為 ‘NO’，即表示尚未完成註冊。 

**可能的原因：**

- 驗證要註冊的電腦失敗。

- 組織中有電腦探索不到的 HTTP Proxy。

- 電腦無法連線到 Azure AD 來進行驗證，或無法連線到 Azure DRS 來進行註冊

- 電腦可能不在組織的內部網路上，或不在可直接看到內部部署 AD 網域控制站的 VPN 上。

- 如果電腦有 TPM，它可能是處於不正常狀態。

- 在本文件稍早提到的服務中可能有設定錯誤的情況，您將必須重新確認。 常見範例包括：

    - 您的同盟伺服器未啟用 WS-Trust 端點

    - 您的同盟伺服器可能不允許來自您網路中使用「整合式 Windows 驗證」之電腦的輸入驗證。

    - 沒有「服務連接點」物件指向電腦所屬 AD 樹系之 Azure AD 中已驗證的網域名稱

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

此欄位顯示裝置是否已加入內部部署 Active Directory。 如果值顯示為 **NO**，即表示裝置無法自動向 Azure AD 註冊。 請先確認將裝置加入內部部署 Active Directory，如此它才能向 Azure AD 註冊。 如果您想要將電腦直接加入 Azure AD，請了解 Azure Active Directory Join 的功能。

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

此欄位顯示裝置是否已向 Azure AD 註冊，但是是以個人裝置的形式註冊 (標示為 [已加入工作場所])。 如果針對已向 Azure AD 註冊的已加入網域電腦，這個值應該顯示為 ‘NO’，但卻顯示為 YES，即表示在電腦完成註冊之前已新增工作或學校帳戶。 在此情況下，如果使用的是 Windows 10「年度更新」版 (在 [執行] 視窗或命令提示字元視窗中執行 WinVer 命令時會顯示 1607)，將會忽略該帳戶。

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES 和 AzureADPrt : YES
  
這些欄位顯示使用者在登入裝置時，已順利向 Azure AD 進行驗證。 如果顯示為 ‘NO’，則可能的原因如下：

- 註冊時，與裝置關聯之 TPM 中的儲存體金鑰 (STK) 無效 (請在提高權限執行的情況下，檢查 KeySignTest)。

- 替代登入識別碼

- 找不到 HTTP Proxy

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[自動裝置註冊常見問題集](active-directory-device-registration-faq.md) 
