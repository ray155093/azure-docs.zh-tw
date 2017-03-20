---
title: "使用 Azure AD Connect 同步實作密碼同步處理 | Microsoft Docs"
description: "提供有關密碼同步處理如何運作以及如何加以啟用的資訊。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 64b6447608ecdd9bdd2b307f4bff2cae43a4b13f
ms.openlocfilehash: cff066ff2943443749ee8eb2ef71c7ca93bb829c
ms.lasthandoff: 03/01/2017


---
# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步處理實作密碼同步處理
本主題提供您所需資訊，以讓您將使用者密碼從內部部署 Active Directory (AD) 同步處理至雲端式 Azure Active Directory (Azure AD)。

## <a name="what-is-password-synchronization"></a>什麼是密碼同步處理
您因為忘記密碼而無法完成工作的機率，與您必須記住的不同密碼數目有關。 必須記住的密碼越多，將其中之一遺忘的機率就越高。 關於密碼重設和其他密碼相關問題的疑問和來電，佔據了最多的技術服務資源。

密碼同步處理是用來將使用者密碼從內部部署 Active Directory 同步處理至雲端式 Azure Active Directory (Azure AD) 的功能。
使用此功能即可用和登入內部部署 Active Directory 的相同密碼，登入 Azure Active Directory 服務 (如 Office 365、Microsoft Intune、CRM Online 和 Azure AD 網域服務)。

![何謂 Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

密碼同步處理可透過將使用者需要維護的密碼數目減少到只剩一個，協助您︰

* 提升使用者的生產力
* 降低技術支援成本  

此外，如果您選擇使用[**與 AD FS 同盟**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)，則可以選擇性地啟用密碼同步處理，做為 AD FS 基礎結構失敗時的備用方式。

密碼同步處理是 Azure AD Connect 同步處理實作的目錄同步作業功能的延伸。 若要在環境中使用密碼同步處理，您需要︰

* 安裝 Azure AD Connect。  
* 設定您的內部部署 AD 與 Azure Active Directory 之間的目錄同步作業
* 啟用密碼同步處理

如需詳細資訊，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> 如需針對 FIPS 和密碼同步處理設定的 Active Directory 網域服務的詳細資訊，請參閱 [密碼同步處理和 FIPS](#password-synchronization-and-fips)。
>
>

## <a name="how-password-synchronization-works"></a>密碼同步處理如何運作
Active Directory 網域服務是以代表使用者實際密碼的雜湊值格式儲存密碼。 雜湊值是單向數學函式 (「雜湊演算法」) 的計算結果。 沒有任何方法可將單向函式的結果還原為純文字版本的密碼。 您無法使用密碼雜湊來登入您的內部部署網路。

為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 擷取您的密碼雜湊。 在將密碼雜湊與 Azure Active Directory 驗證服務同步之前，會進行額外的安全性處理。 密碼會以每個使用者為基礎，依照時間先後順序來進行同步處理。

密碼同步處理程序的實際資料流程，就類似同步處理 DisplayName 或電子郵件地址等使用者資料。 不過，相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。 密碼同步處理程序每 2 分鐘會執行一次。 您無法修改此程序的執行頻率。 當您同步處理密碼時，它便會覆寫現有的雲端密碼。

第一次啟用密碼同步功能時，它會對範圍內的所有使用者執行初次的密碼同步處理。 您無法明確定義一小組要同步處理的使用者密碼。

當您變更內部部署密碼時，更新後的密碼將會進行同步處理，而這個動作大多會在幾分鐘內完成。
密碼同步處理功能會自動重試失敗的同步處理嘗試。 若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。
目前的雲端服務工作階段不會立即受到同步處理的密碼變更之影響，會在您登入雲端服務時才會受到影響。 不過，當雲端服務要求您重新驗證時，就需要提供新的密碼。

> [!NOTE]
> 只有 Active Directory 的物件類型使用者才支援密碼同步。 不支援 iNetOrgPerson 物件類型。
>
>

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>密碼同步處理對 Azure AD 網域服務的運作方式
您也可以使用密碼同步處理功能，將內部部署密碼同步處理到 [Azure AD 網域服務](../../active-directory-domain-services/active-directory-ds-overview.md)。 此案例可讓 Azure AD 網域服務，以內部部署 AD 中所有可用的方法驗證雲端中的使用者。 此案例的體驗類似於在內部部署環境中使用 Active Directory 遷移工具 (ADMT)。

### <a name="security-considerations"></a>安全性考量
同步密碼的時候，純文字版本的密碼不會向密碼同步處理功能、Azure AD 或任何相關服務公開。

此外，內部部署 Active Directory 不需使用可反轉加密的格式儲存密碼。 Active Directory 密碼雜湊的摘要會用於內部部署 AD 和 Azure Active Directory 之間的傳輸。 密碼雜湊的摘要無法用來存取內部部署環境內的資源。

### <a name="password-policy-considerations"></a>密碼原則考量
啟用密碼同步處理會影響兩種類型的密碼原則：

1. 密碼複雜性原則
2. 密碼到期原則

**Password complexity policy**  
當您啟用密碼同步處理時，在內部部署 Active Directory 中的密碼複雜性原則，會覆寫已同步處理的使用者在雲端中的複雜性原則。 您可以使用內部部署 Active Directory 的所有有效密碼，來存取 Azure AD 服務。

> [!NOTE]
> 直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。
>
>

**Password expiration policy**  
如果使用者位於密碼同步處理範圍內，則雲端帳戶的密碼會設為「永不到期」。
您可以使用內部部署環境中已過期的同步處理密碼，繼續登入雲端服務。 您的雲端密碼會於下一次您在內部部署環境中變更密碼時更新。

### <a name="overwriting-synchronized-passwords"></a>覆寫已同步的密碼
系統管理員可以使用 Windows PowerShell 手動重設您的密碼。

在此情況下，新的密碼會覆寫您已同步處理的密碼，且雲端中定義的所有密碼原則都會套用到新的密碼。

如果您再次變更內部部署密碼，則新密碼會同步到雲端並覆寫手動更新的密碼。

## <a name="enabling-password-synchronization"></a>啟用密碼同步處理
當您使用 **快速設定**安裝 Azure AD Connect 時，便會自動啟用密碼同步處理。 如需詳細資訊，請參閱[使用快速設定開始使用 Azure AD Connect](active-directory-aadconnect-get-started-express.md)。

如果您在安裝 Azure AD Connect 時使用自訂設定，則必須在使用者登入頁面上啟用密碼同步處理。 如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)。

![啟用密碼同步處理](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>密碼同步處理和 FIPS
如果已經根據美國聯邦資訊處理標準 (FIPS) 鎖定您的伺服器，則已停用 MD5。

**若要針對密碼同步處理啟用 MD5，請執行下列步驟︰**

1. 移至 **%programfiles%\Azure AD Sync\Bin**。
2. 開啟 **miiserver.exe.config**。
3. 移至 **configuration/runtime** 節點 (位於檔案結尾)。
4. 新增下列節點︰ `<enforceFIPSPolicy enabled="false"/>`
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

## <a name="troubleshooting-password-synchronization"></a>針對密碼同步處理進行疑難排解
如果您在進行密碼同步處理時發生問題，則請參閱[針對密碼同步處理進行疑難排解 (英文)](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)。

## <a name="next-steps"></a>後續步驟
* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

