---
title: "部署建議的安全電子郵件原則 | Microsoft Docs"
description: "說明 Microsoft 對部署安全電子郵件原則和組態的建議和核心概念。"
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: a5eb77c68c48e9cca2805fa64075279560179e14
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-recommended-secure-email-policies"></a>部署建議的安全電子郵件原則

本節討論如何在新佈建的環境中部署建議的原則。 在個別實驗室環境中設定這些原則可讓您在生產前和生產環境中首度推出之前，了解和評估建議的原則。 新佈建的環境可能僅限雲端或混合式環境。  

若要成功部署建議的原則，您需要在 Azure 入口網站中採取行動，以符合先前所述的必要條件。 具體而言，您需要：
* 設定具名網路，以確保 Azure Identity Protection 可正確地產生風險分數
* 要求所有使用者進行註冊，以進行多重要素驗證 (MFA)
* 設定密碼同步和自助式密碼重設，讓使用者可以自己重設密碼

您可以以特定使用者群組為目標，同時實行 Azure AD 和 Intune 原則。 我們建議分段實行先前定義的原則。 如此一來，您可以以累加方式驗證原則和與原則相關之支援團隊的效能。

## <a name="baseline-conditional-access-policy"></a>基準條件式存取原則

若要建立新的條件式存取原則，請使用系統管理員認證登入 Microsoft Azure 入口網站。 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。 

您可以新增新的原則 ([+ 新增])，如下列螢幕擷取畫面所示：

![基準 CA 原則](./media/secure-email/baseline-ca-policy.png)

下表說明表示每個保護層級需要的原則所需的適當設定。

### <a name="medium-and-above-risk-requires-mfa"></a>中等以上風險需要進行 MFA

下表說明要實行此原則的條件式存取原則設定。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**指派**|使用者和群組|包含|選取使用者和群組 – 選取包含目標使用者的特定安全性群組|從包括試驗使用者的安全性群組開始。|
|||排除|例外狀況安全性群組；服務帳戶 (應用程式識別碼)|視暫時需求修改成員資格|
||雲端應用程式|包含|選取應用程式 - 選取 Office 365 Exchange Online||
||條件|已設定|是|針對環境和需求進行設定|
||登入風險|風險層級|高、中等|同時勾選|
|**存取控制**|授與|授與存取權|True|已選取|
|||需要 MFA|True|勾選|
|||需要相容的裝置|False||
|||需要加入網域的裝置|False||
|||需要所有選取的控制項|True|已選取|
|**啟用原則**|||另一|部署條件式存取原則|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要相容或加入網域的裝置

若要建立適用於 Exchange Online 的新 Intune 條件式存取原則，請使用系統管理員認證登入 [Microsoft 管理入口網站 (http://manage.microsoft.com)](http://manage.microsoft.com/)，然後瀏覽至 [原則] > [條件式存取] > [Exchange Online 原則]。

![Exchange Online 原則](./media/secure-email/exchange-online-policy.png)

您必須在 Intune 管理入口網站中設定特別適用於 Exchange Online 的條件式存取原則，以要求相容或加入網域的裝置。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**應用程式存取**|使用新式驗證的 Outlook 及其他應用程式|所有平台|True|已選取|
|||Windows 必須符合下列需求|必須為加入網域或相容的裝置|已選取 (清單)|
|||選取的平台|False||
||Outlook Web Access (OWA)|封鎖與 Outlook 在相同平台但不相容的裝置|True|勾選|
||使用基本驗證的 Exchange ActiveSync 應用程式|封鎖在 Microsoft Intune 支援的平台上但不相容的裝置|True|勾選|
|||封鎖其他所有在 Microsoft Intune 不支援平台上的裝置|True|勾選|
|**原則部署**|目標群組|選取此原則的目標 Active Directory 群組|||
|||所有使用者|False||
|||選取的安全性群組|True|已選取|
|||修改|選取包含目標使用者的特定安全性群組||
||豁免的群組|選取豁免套用此原則的 Active Directory 群組 (覆寫目標群組清單的成員)|||
|||沒有豁免的使用者|True|已選取|
|||選取的安全性群組|False|||

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>適用於 Exchange Online 的行動應用程式管理條件式存取

您必須在 Intune 管理入口網站中設定特別適用於 Exchange Online 的條件式存取原則，以管理行動裝置應用程式。

若要管理行動裝置應用程式，請使用系統管理員認證登入 Microsoft Azure 入口網站，然後瀏覽至 [Intune 應用程式防護] > [設定] > [條件式存取] > [Exchange Online]。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**應用程式存取**|允許的應用程式|啟用應用程式存取|允許支援 Intune 應用程式原則的應用程式|已選取 (清單) – 這會產生 Intune 應用程式原則支援的應用程式/平台組合清單|
|**使用者存取**|允許的應用程式|受限制的使用者群組|新增使用者群組 – 選取包含目標使用者的特定安全性群組|從包括試驗使用者的安全性群組開始|
|||豁免的使用者群組|例外安全性群組|||

#### <a name="apply-to"></a>套用對象

完成試驗專案之後，您應該將這些原則套用至組織中的所有使用者。

## <a name="sensitive-conditional-access-policy"></a>敏感性條件式存取原則

### <a name="low-and-above-risk-requires-mfa"></a>低等以上風險需要進行 MFA
下表說明要實行低風險以上原則的條件式存取原則設定。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**指派**|使用者和群組|包含|選取使用者和群組 – 選取包含目標使用者的特定安全性群組|從包括試驗使用者的安全性群組開始|
|||排除|例外狀況安全性群組；服務帳戶 (應用程式識別碼)|視暫時需求修改成員資格|
||雲端應用程式|包含|選取應用程式 - 選取 Office 365 Exchange Online||
||條件|已設定|是|針對環境和需求進行設定|
||登入風險|已設定|是|針對環境和需求進行設定|
|||風險層級|低、中等、高|勾選所有三個項目|
|**存取控制**|授與|授與存取權|True|已選取|
|||需要 MFA|True|勾選|
|||需要相容的裝置|False||
|||需要加入網域的裝置|False||
|||需要所有選取的控制項|True|已選取|
|**啟用原則**|||另一|部署條件式存取原則|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要相容或加入網域的裝置
(請參閱基準指示)

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>適用於 Exchange Online 的行動應用程式管理條件式存取

(請參閱基準指示)

#### <a name="apply-to"></a>套用對象

完成試驗專案之後，您應該將這些原則套用至組織中需要存取視為敏感性電子郵件的使用者。

## <a name="highly-regulated-conditional-access-policy"></a>嚴格規範條件式存取原則
### <a name="mfa-required"></a>需要 MFA

下表說明要實行嚴格規範原則的條件式存取原則設定。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**指派**|使用者和群組|包含|選取使用者和群組 – 選取包含目標使用者的特定安全性群組|從包括試驗使用者的安全性群組開始|
|||排除|例外狀況安全性群組；服務帳戶 (應用程式識別碼)|視暫時需求修改成員資格|
||雲端應用程式|包含|選取應用程式 - 選取 Office 365 Exchange Online||
|**存取控制**|授與|授與存取權|True|已選取|
|||需要 MFA|True|勾選|
|||需要相容的裝置|False|勾選|
|||需要加入網域的裝置|False||
|||需要所有選取的控制項|True|已選取|
|**啟用原則**|||另一|部署條件式存取原則|

### <a name="require-a-compliant-or-domain-joined-device"></a>需要相容或加入網域的裝置
(請參閱基準指示)
### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>適用於 Exchange Online 的行動應用程式管理條件式存取
(請參閱基準指示)
#### <a name="apply-to"></a>套用對象
完成試驗專案之後，您應該將這些原則套用至組織中需要存取視為嚴格規範電子郵件的使用者。

## <a name="user-risk-policy"></a>使用者風險原則
### <a name="high-risk-users-must-change-password"></a>高風險使用者必須變更密碼
若要確保在所有高風險使用者遭盜用帳戶登入時強制執行密碼變更，您必須套用下列原則。 

使用系統管理員認證登入 [Microsoft Azure 入口網站 (http://portal.azure.com)](http://portal.azure.com/)，然後瀏覽至 [Azure AD Identity Protection] > [使用者風險原則]。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**指派**|使用者|包含|所有使用者|已選取|
|||排除|None||
||條件|使用者風險|高|已選取|
|**控制項**|Access|允許存取|True|已選取|
||Access|需要密碼變更|True|勾選|
|**檢閱**|N/A|N/A|N/A|N/A|
|**強制執行原則**|||另一|啟動強制執行原則|

## <a name="additional-configurations"></a>其他組態
除了上述原則之外，您也必須設定下列本節中討論的行動應用程式和裝置管理設定。 

### <a name="intune-mobile-application-management"></a>Intune 行動應用程式管理 

若要確保電子郵件在每個安全性和資料保護層中都受到先前所述的原則建議保護，您必須從 Azure 入口網站內建立 Intune 應用程式防護原則。

若要建立新的應用程式保護原則，請使用系統管理員認證登入 Microsoft Azure 入口網站，然後瀏覽至 [Intune 應用程式防護] > [設定] > [條件式存取] > [應用程式原則]。

新增原則 ([+ 新增])，如下列螢幕擷取畫面所示：

![Intune 行動應用程式管理](./media/secure-email/intune-mobile-app-mgmt.png)

>[!NOTE]
>iOS 和 Android 的應用程式保護原則選項具有些微差異。 下列原則適用於 Android。
>

下表詳細說明表示每個保護層級需要的原則所需的適當設定。
| 下表說明建議的 Intune 應用程式防護原則設定。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**一般**|電子郵件|名稱|適用於 Android 的安全電子郵件原則|輸入原則名稱|
|||說明||輸入可說明原則的文字|
|||平台|Android|iOS 和 Android 的應用程式保護原則選項具有些微差異；此原則適用於 Android|
|**應用程式**|應用程式|應用程式|Outlook|已選取 (清單)|
|**設定**|資料重新配置|防止 Android 備份|是|在 iOS 上，這會特別呼叫 iTunes 和 iCloud|
||||允許應用程式將資料傳送至其他應用程式|受原則管理的應用程式||
|||允許應用程式接收其他應用程式的資料|受原則管理的應用程式||
|||防止「另存新檔」|是||
|||限制利用其他應用程式剪下、複製及貼上|受原則管理的應用程式||
|||限制 Web 內容在 Managed Browser 中顯示|否||
|||加密應用程式資料|是|在 iOS 上，請選取下列選項：[鎖定裝置時]|
|||停用連絡人同步|否||
||Access|需要 PIN 才能存取|是||
|||重設 PIN 前的嘗試次數|3||
|||允許簡單的 PIN|否||
|||PIN 長度|6||
|||允許指紋而非 PIN|是||
|||需要公司認證才能存取|否||
|||封鎖受管理的應用程式在經過破解或刷機的裝置上執行|是||
|||重新檢查存取需求前的時間 (分鐘)|30||
|||離線寬限期|720||
|||抹除應用程式資料前的離線間隔 (天)|90||
|||封鎖螢幕擷取及 Android 助手|否|在 iOS 上無法使用此選項|

完成時，請記得按一下 [建立]。 重複上述步驟，並以 iOS 取代選取的平台 (下拉式清單)。 這會建立兩個應用程式原則，因此您建立原則之後，便可將群組指派給原則並進行部署。

### <a name="intune-mobile-device-management"></a>Intune 行動裝置管理
您可以使用系統管理員認證登入 [Microsoft 管理入口網站 (http://manage.microsoft.com)](https://manage.microsoft.com/)，以建立下列組態和相容性原則。

#### <a name="ios-email-profile"></a>iOS 電子郵件設定檔
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [組態原則] > [新增] > [iOS] > [電子郵件設定檔 (iOS 8 和更新版本)] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**電子郵件設定檔**|Exchange Active Sync|主機 (#)|Outlook.office365.com||
|||帳戶名稱 (#)|SecureEmailAccount|系統管理員選項|
|||使用者名稱|使用者主體名稱|已選取 – 下拉式清單|
|||電子郵件地址|主要 SMTP 位址|已選取 – 下拉式清單|
|||驗證方法|使用者名稱和密碼|已選取 – 下拉式清單|
|||使用 S/MIME|False||
||同步處理設定|電子郵件同步處理天數|兩週|已選取 – 下拉式清單|
|||使用 SSL|True|勾選|
|||允許訊息移到其他電子郵件帳戶|False||
|||允許從協力廠商應用程式傳送電子郵件|True||
|||同步處理最近使用的電子郵件地址|True|勾選|

#### <a name="ios-app-sharing-profile"></a>iOS 應用程式共用設定檔
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [組態原則] > [新增] > [iOS] > [一般組態 (iOS 8.0 和更新版本)] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**安全性**|全部|全部|未設定||
|**雲端**|全部|全部|未設定||
|**應用程式**|[瀏覽器]|全部|未設定||
||應用程式|允許安裝應用程式|未設定||
|||需要密碼才能存取應用程式存放區|未設定||
|||所有應用程式內購買|未設定||
|||允許在其他受管理的應用程式中使用受管理的文件 (iOS 8.0 和更新版本)|否|已選取 – 下拉式清單|
|||允許在其他受管理的應用程式中使用未受管理的文件|未設定||
|||允許視訊會議|未設定||
|||允許使用者信任新的企業應用程式作者|未設定||
||遊戲|全部|未設定||
||媒體內容|全部|未設定|||

#### <a name="android-email-profile"></a>Android 電子郵件設定檔
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [組態原則] > [新增] > [iOS] > [電子郵件設定檔 (Samsung KNOX Standard 4.0 和更新版本)] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**電子郵件設定檔**|Exchange Active Sync|主機 (#)| Outlook.office365.com|
|||帳戶名稱 (#)|SecureEmailAccount|系統管理員選項|
|||使用者名稱|使用者主體名稱|已選取 – 下拉式清單|
|||電子郵件地址|主要 SMTP 位址|已選取 – 下拉式清單|
|||驗證方法|使用者名稱和密碼|已選取 – 下拉式清單|
|||使用 S/MIME|False||
||同步處理設定|電子郵件同步處理天數|兩週|已選取 – 下拉式清單|
|||同步排程|未設定|已選取 – 下拉式清單|
|||使用 SSL|True|勾選|
|||要同步處理的內容類型|||
|||電子郵件|True|勾選 (已鎖定)|
|||連絡人|True|勾選|
|||行事曆|True|勾選|
|||工作|True|勾選|
|||注意事項|True|勾選|

#### <a name="android-for-work-email-profile"></a>Android for Work 電子郵件設定檔
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [組態原則] > [新增] > [iOS] > [電子郵件設定檔 (Android for Work - Gmail)] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**電子郵件設定檔**|Exchange Active Sync|主機 (#)| Outlook.office365.com|
|||帳戶名稱 (#)|SecureEmailAccount|系統管理員選項|
|||使用者名稱|使用者主體名稱|已選取 – 下拉式清單|
|||電子郵件地址|主要 SMTP 位址|已選取 – 下拉式清單|
|||驗證方法|使用者名稱和密碼|已選取 – 下拉式清單|
||同步處理設定|電子郵件同步處理天數|兩週|已選取 – 下拉式清單|
|||使用 SSL|True|勾選|

#### <a name="android-for-work-app-sharing-profile"></a>Android for Work 應用程式共用設定檔
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [組態原則] > [新增] > [iOS] > [一般組態 (Android for Work)] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**安全性**|密碼|密碼長度下限|未設定||
|||移除工作設定檔前的重複登入失敗次數|未設定||
|||裝置鎖定前的非使用狀態分鐘數|未設定||
|||密碼到期 (天)|未設定||
|||記住密碼歷程記錄|未設定||
|||需要密碼才能解除鎖定行動裝置|未設定||
|||允許指紋解除鎖定 (Android 6.0 +)|未設定||
|||允許 Smart Lock 及其他信任代理程式 (Android 6.0 +)|未設定||
||工作設定檔設定|允許工作和個人設定檔之間的資料共用|工作設定檔中的應用程式可處理來自個人設定檔的共用要求|已選取 – 下拉式清單|
|||鎖定裝置時隱藏工作設定檔通知 (Android 6.0 +)|未設定||
|||設定預設應用程式權限原則 (Android 6.0 +)|未設定|||

#### <a name="device-compliance-policy"></a>裝置相容性原則
在 [Intune 管理入口網站 (https://manage.microsoft.com)](https://manage.microsoft.com/)的 [原則] > [合規性原則] > [新增] 中，建立下列組態原則。

|類別|類型|屬性|值|注意事項|
|:---------|:---|:---------|:-----|:----|
|**系統安全性**|密碼|需要密碼才能解除鎖定行動裝置 (...)|是|已選取 – 下拉式清單|
|||允許簡單密碼 (...)|否|已選取 – 下拉式清單|
|||密碼長度下限 (...)|6|已選取 – 清單|
||進階密碼設定|全部|未設定||
||加密|需要加密行動裝置 (...)|是|已選取 – 下拉式清單|
||電子郵件設定檔|必須由 Intune 管理電子郵件帳戶 (iOS 8.0 +)|是| 已選取 – 下拉式清單|
|||選取 (#)||對於 iOS 必須選取的電子郵件組態原則：iOS 電子郵件原則 (請參閱上述的組態原則)|
|**裝置健康情況**|Windows 裝置健康情況證明|需要裝置回報為狀況良好 (Windows 10 桌面版和行動裝置版及更新版本)|是||
||裝置安全性設定|全部|未設定||
||裝置威脅防護|全部|未設定||
||破解|裝置不得經過破解或刷機 (iOS 8.0+、Android 4.0+)|是||
|**裝置屬性**|作業系統版本|全部|未設定|||

上述所有原則要被視為已部屬，都必須以使用者群組為目標。 您可以建立原則 ([儲存]) 或稍後選取 [原則] 區段中的 [管理部署] \(與 [新增] 層級相同\) 以完成此作業。

## <a name="remediating-events-that-have-results-in-medium-or-high-risk-access"></a>修復導致中等或高風險存取的事件
如果使用者回報其現在要執行 MFA 但之前並不需要，支援團隊可以從風險觀點檢閱其狀態。  

具有全域管理員或安全性系統管理員角色之組織內的使用者可以使用 Azure AD Identity Protection 檢閱讓計算的風險分數提高的具風險事件。 如果其識別已標幟為可疑但確認為有效的某些事件 (例如，員工在休假時從不熟悉的位置登入)，系統管理員可以解決該事件，讓其不再提高風險分數。

## <a name="next-steps"></a>後續步驟
[深入了解 Office 365 和 EMS 服務](secure-email-ems-office365-service-descriptions.md)

