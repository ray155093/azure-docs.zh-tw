---
title: "在 Azure Active Directory 的登入和存取面板頁面新增公司商標"
description: "了解如何將公司商標新增至 Azure 登入頁面和存取面板頁面"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>在登入和存取面板頁面新增公司商標
許多組織都想對其管理的網站和服務套用一致的外觀與風格。 Azure Active Directory 提供這項功能，讓 IT 專業人員利用公司標誌和映像來自訂下列網頁的外觀：

* **登入頁面** - 當您的員工和商務來賓登入 Office 365 或其他使用 Azure AD 的應用程式時，便會出現此頁面。
* **存取面板頁面** - 存取面板是網頁型入口網站，可讓您檢視並啟動 Azure AD 系統管理員已授與您存取權的雲端式應用程式。 可在 [https://myapps.microsoft.com](https://myapps.microsoft.com) 找到存取面板。

本主題說明如何自訂登入頁面和存取面板頁面。

> [!NOTE]
> * 公司商標僅在您升級至 Premium 或 Basic 版本的 Azure Active Directory 後，或擁有 Office 365 授權時才能使用。 如需詳細資訊，請參閱 Azure Active Directory 版本。
> 
> * Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。 由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。 如需詳細資訊，請透過 Azure Active Directory 論壇與我們連絡。


## <a name="customizing-the-sign-in-page"></a>自訂登入頁面
您的使用者嘗試存取貴組織訂閱的雲端應用程式和服務時，通常會與 Azure AD 登入頁面互動。

如果您已對登入頁面套用商標變更，最多需要一小時才會出現使用者的變更。

當使用者存取租用戶特定 URL (例如 https://outlook.com/contoso.com) 時，公司商標元素會出現在 Azure AD 登入頁面上。

當使用者造訪一般 URL (例如 www.office.com) 的服務時，登入頁面尚未包含公司商標資訊，因為系統不知道使用者是誰。 不過，使用者輸入其使用者識別碼或選取使用者圖格之後，就會出現公司商標。

> [!NOTE]
> * 在已設定商標之 Azure 傳統入口網站的 [Active Directory]  >  [目錄]  >  [網域] 區段中，您的網域名稱必須為 [作用中]。
> * 登入頁面商標不會延續到個人 Microsoft 帳戶的登入頁面。 如果您的員工或商務來賓使用個人 Microsoft 帳戶登入，其登入頁面將不會反映貴組織的商標。
>

下列螢幕擷取畫面說明如何自訂登入頁面。

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>案例 1：Contoso 員工移至一般應用程式的 URL (例如，www.office.com)

在此範例中，Contoso 使用者登入行動應用程式，或使用一般 URL 登入 web 應用程式。 左側圖例一律代表應用程式，右側的互動窗格會在適當時更新以顯示 Contoso 商標元素。

![自訂前後的 Office 365 登入頁面][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>案例 2：Contoso 員工移至限制為內部使用者的 Contoso 應用程式

在此範例中，Contoso 使用者使用公司特定的 URL 登入內部應用程式。 左側圖例代表公司商標 (Contoso)。 右側的互動窗格已對 Contoso 鎖定，並協助員工登入。

![受限制的應用程式登入頁面][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>案例 3：Contoso 員工移至對外部使用者開放的 Contoso 應用程式

在此範例中，使用者從 Contoso 登入 LoB 應用程式，但使用者可能或可能不是 Contoso 員工。 左側圖例代表資源擁有者 (Contoso)，就像上面的案例 \#2。 但這次，右側的互動窗格並未對 Contoso 鎖定，來傳遞外部使用者可以自由地登入。

![登入以開啟存取][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>案例 4：Fabrikam 商務來賓前往對外部使用者開放的 Contoso 應用程式

在此範例中，Contoso 使用者使用公司特定的 URL 登入內部應用程式。 左側圖例代表公司商標 (Contoso)。 右側的互動窗格已對 Contoso 鎖定，並協助員工登入。

![以外部使用者的身分登入][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>我們可以自訂頁面上的哪些元素？

您可以自訂登入頁面上的下列元素：

![][5]

| 頁面元素 | 頁面上的位置 |
|:--- | --- |
| 橫幅標誌 | 顯示於頁面的右上方。 一旦使用者的組織決定後 (通常，在輸入使用者名稱後)，取代應用程式的標誌。 |
| 背景圖例 | 在登入頁面的左邊顯示為跨頁映像。 取代應用程式的圖例，針對租用戶登入案例 (當使用者存取其自己的組織或其為商務來賓之組織所發行的應用程式)。<br>在低頻寬連線上，會以背景色彩取代背景圖例。 在窄畫面 (例如手機) 上，不會顯示圖例。<br>當使用者調整其瀏覽器大小時，將會裁剪背景圖例。 當您設計圖例時，請將主要視覺元素保留在左上角，以避免它們被裁剪。 | 
| [讓我保持登入] 核取方塊 | 顯示在 [密碼] 方塊之下。 |
| 登入頁面文字 | 要顯示在頁尾上方的 Bboilerplate 文字。 它可以用來傳達有用資訊給使用者，例如服務台的電話號碼或法律聲明。 |

> [!NOTE]
> 所有元素都是選用的。 例如，如果您指定 [橫幅標誌]，但未指定 [背景圖例]，則登入頁面會顯示您的標誌以及目的地網站的圖例 (例如，Office 365 加州高速公路影像)。
>

登入頁面上的 [讓我保持登入] 核取方塊，可讓使用者在關閉並重新開啟其瀏覽器時保持登入狀態，且不會影響工作階段存留期。

核取方塊顯示與否取決於 [隱藏 KMSI] 的設定。

![隱藏 KMSI 設定][6]

若要隱藏此核取方塊，請將此設定設為 [隱藏]。

> [!NOTE]
> SharePoint Online 和 Office 2010 的某些功能取決於能夠選取此核取方塊的使用者。 如果您將此設定設為隱藏，使用者可能會在登入時看見其他和非預期的登入提示。
>
>

您也可以將此頁面上的所有元素都翻成當地使用語。 設定一組「預設」自訂元素之後，就可以設定不同地區設定的其他版本。 您也可以混合使用並符合各種元素。 例如，您可以：

* 建立適用於所有文化的「預設」圖例，然後建立英文和法文的特定版本。 當您將瀏覽器設定為這兩種語言之一時，會出現特定的影像，至於其他所有語言則會出現預設圖例。
* 為您的組織設定不同的標誌 (例如日文或希伯來文版本)。

## <a name="access-panel-page-customization"></a>存取面板頁面自訂
[存取面板] 頁面基本上是入口網站頁面，可供快速存取系統管理員已授與您存取權的雲端應用程式。 在此頁面上，您的應用程式會顯示為可點選的應用程式圖格。

下列螢幕擷取畫面顯示自訂後的存取面板頁面範例：

![][8]

## <a name="configure-your-directory-with-company-branding"></a>使用公司商標來設定目錄
您可以針對 Azure 傳統入口網站中的每個目錄，設定一組預設的可自訂元素。 儲存預設值之後，系統管理員可以針對不同的語言/地區設定新增每個元素的當地語系化版本。 所有可自訂元素都是選用的。

例如，如果您設定預設 [橫幅標誌]，但未設定 [大型圖例]，則登入頁面會將您的標誌顯示在右上角。 不過，會顯示網站的預設圖例。

假設有下列組態︰

* 預設的橫幅標誌和英文登入頁面文字
* 語言特定的德文登入頁面文字

如果您的語言喜好設定是德文，您會得到預設的橫幅標誌但為德文文字。

技術上，雖然您可以針對 Azure AD 所支援的每種語言設定不同的一組，但是基於維護和效能考量，建議您保持低變化數目。
 
**若要將公司商標新增至您的目錄，請執行下列步驟：**

1. 以想要自訂之目錄的管理員身分，登入 [Azure 傳統入口網站](https://manage.windowsazure.com) 。
2. 選取您想要自訂的目錄。
3. 在頂端的工具列中，按一下 [設定] 。
4. 按一下 [自訂商標] 。
5. 修改您想要自訂的元素。 所有欄位都是選用的。
6. 按一下 [儲存] 。

您對登入頁面商標進行的新變更最多需要一個小時才會出現。

**若要新增語言特定公司商標，請執行下列步驟：**

1. 以想要自訂之目錄的管理員身分，登入 [Azure 傳統入口網站](https://manage.windowsazure.com) 。
2. 選取您想要自訂的目錄。
fs3。 在頂端的工具列中，按一下 [設定] 。
4. 按一下 [自訂商標] 。
5. 按一下 [新增特定語言的商標] 。
6. 選取您要自訂標誌的語言，然後按 [下一步] 。
7. 僅編輯您想要設定語言特定覆寫的元素。 所有欄位都是選用的。 如果欄位空白，則會改為顯示自訂預設值 (或者，如果未設定自訂預設值，則為 Microsoft 預設值)。
8. 按一下 [儲存] 。

**若要從您的目錄中移除公司商標，請執行下列步驟：**

1. 以想要自訂之目錄的管理員身分，登入 [Azure 傳統入口網站](https://manage.windowsazure.com) 。
2. 選取您想要自訂的目錄。
3. 在頂端的工具列中，按一下 [設定] 。
4. 按一下 [自訂商標] 。
5. 在 [自訂商標] 頁面上，選取 [編輯現有商標設定]  ，然後移至下一頁。
6. 根據您想要移除的元素，執行下列一或多項動作：

    a. 在 [橫幅標誌] 之下，選取 [移除上傳的標誌]。

    b.這是另一個 C# 主控台應用程式。 在 [圖格標誌] 之下，選取 [移除上傳的標誌]。

    c. 移除所有文字方塊中的文字。

    d. 按 [下一步] 。

    e. 移除所有文字方塊中的文字。
7. 按一下 [儲存]  移除元素。
8. 如有必要，請再按一下 [自訂商標]  ，並針對需要移除的所有語言特定商標重複這些步驟。
    按一下 [自訂商標] 並看到未設定現有設定的 [自訂預設商標] 表單時，已移除所有商標設定。


## <a name="customizable-elements"></a>可自訂元素
公司標誌用於登入和存取面板頁面，而其他元素只用於登入頁面。 下表提供不同可自訂元素的詳細資料。

| 名稱 | 說明 | 條件約束 | 建議 |
| --- | --- | --- | --- |
| 橫幅標誌 |[橫幅標誌] 會顯示在 [登入] 頁面和 [存取面板] 上。 |<p>JPG 或 PNG</p><p>60x280 像素</p><p>10 KB</p> |<p>使用您組織的完整標誌 (包含形符和商標)</p><p>保持低於 30 個像素，避免行動裝置上出現捲軸</p><p>保持低於 4 KB</p><p>使用透明 PNG (不認為登入頁面一律具有白色背景)</p> |
| 圖格標誌 | 目前未使用 |<p>JPG 或 PNG</p><p>120x120 像素</p><p>10 KB</p> |<p>保持簡單 (無小型文字)，因為此影像大小可能會調整為 50% |
| </p> | | | |
| 登入使用者名稱標籤 | 目前未使用 |<p>Unicode 文字，最多 50 個字元</p><p>僅純文字 (沒有連結或 HTML 標記)</p> |<p>保持簡短和簡單</p><p>詢問使用者通常如何參照您提供給他們的工作或學校帳戶。</p> |
| 登入頁面重複使用文字 |此重複使用文字會出現在 [登入] 頁面表單下方，並且可以用來傳達其他指示或可在何處取得說明和支援。 |<p>Unicode 文字，最多 256 個字元</p><p>僅純文字 (沒有連結或 HTML 標記)</p> |保持低於 250 個字元 (約 3 行文字) |
| 登入頁面背景圖例 | 當使用者存取租用戶特定 URL 時，登入頁面右側顯示的大型映像 (對於從右至左的語言則在右側)。 |<p>JPG 或 PNG</p><p>1420x1200</p><p>500 KB</p> |<p>1420x1200 像素</p><p>重要事項：保持越小越好，最好低於 200 KB。 如果此影像太大，則會在未快取影像時影響 [登入] 頁面的效能</p><p>此映像幾乎一律會進行剪裁以配合不同的螢幕外觀比例。 將主要視覺元素保留在左上角。</p> |
| 登入頁面背景色彩 | 在低頻寬連線時，此單色會用來取代背景圖例。 | 必須是十六進位格式的 RGB 色彩 (範例：\#FFFFFF) | 我們建議挑選橫幅標誌的主要色彩。 |

## <a name="next-steps"></a>後續步驟
* [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

