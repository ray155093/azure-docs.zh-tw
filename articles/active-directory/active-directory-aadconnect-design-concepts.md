<properties
   pageTitle="Azure AD Connect：設計概念 |Microsoft Azure"
   description="本主題詳細說明特定的實作設計領域"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="andkjell"/>

# Azure AD Connect：設計概念
本主題旨在說明在 Azure AD Connect 實作設計期間必須考量的領域。這個主題是特定領域的深入探討，而在其他主題中也會簡短描述這些概念。

## sourceAnchor
sourceAnchor 屬性的定義是*在物件存留期間不會變更的屬性*。它可將物件唯一識別為內部部署和 Azure AD 中的相同物件。此屬性也稱為 **immutableId**，兩個名稱可交換使用。

在本主題中，不可變 (亦即無法變更) 這個字非常重要。由於此屬性的值在設定之後就無法變更，所以請務必挑選支援您案例的設計。

此屬性用於下列案例︰

- 在建置新的同步處理引擎伺服器，或在災害復原案例後進行重建時，這個屬性會連結 Azure AD 中的現有物件與內部部署的物件。
- 如果您從僅限雲端的身分識別移至已同步處理的身分識別模型，則這個屬性可讓物件「完全比對」Azure AD 中的現有物件與內部部署的物件。
- 如果您使用同盟，則這個屬性會與 **userPrincipalName** 一起用於宣告來唯一識別使用者。

本主題只討論與使用者相關的 sourceAnchor。相同的規則適用於所有的物件類型，但僅限於通常有此問題的使用者。

### 選取良好的 sourceAnchor 屬性
此屬性值必須遵循下列規則：

- 長度小於 60 個字元
    - 系統會將 a-z、A-Z 或 0-9 以外的字元編碼並計為 3 個字元
- 不包含特殊字元︰&#92; ! # $ % & * + / = ? ^ &#96; { }| ~ < > ( ) ' ; : , [ ] " @ _
- 必須是全域唯一的
- 必須是字串、整數或二進位
- 不應以使用者的名稱、這些變更為基礎
- 不應區分大小寫，並避免可能會因大小寫而改變的值
- 建立物件時應該要予以指派

如果選取的 sourceAnchor 不是字串類型，則 Azure AD Connect 會將此屬性值進行 Base64Encode 處理，以確保不會出現特殊字元。如果您使用 ADFS 以外的其他同盟伺服器，請確定您的伺服器也能夠將此屬性進行 Base64Encode 處理。

sourceAnchor 屬性會區分大小寫。"JohnDoe" 與 "johndoe" 是不同的值。但是您不應該擁有兩個只有大小寫有差異的不同物件。

如果您有單一內部部署樹系，應該使用的屬性為 **objectGUID**。這也是您在 Azure AD Connect 中使用快速設定時所用的屬性，而且也是 DirSync 所用的屬性。

如果您有多個樹系，而且不會在樹系和網域之間移動使用者，則 **objectGUID** 是適合使用的屬性 (即使在此情況下)。

如果您在樹系和網域之間移動使用者，則必須尋找不會變更的屬性，或在移動時可隨使用者移動的屬性。建議的方法是引入綜合的屬性。可保存 GUID 之類項目的屬性也可能適用。在物件建立期間，會建立新的 GUID 建立並於使用者加上戳記。可以在同步處理引擎伺服器中建立自訂同步規則，以根據 **objectGUID** 建立這個值，然後在 ADDS 中更新選取的屬性。當您移動物件時，請務必同時複製此值的內容。

另一個解決方案是挑選您知道不會變更的現有屬性。常用的屬性包括 **employeeID**。如果您考慮使用含有字母的屬性，請確定屬性值的大小寫 (大寫與小寫) 沒機會變更。不該使用的不合適屬性，包括含使用者姓名的屬性。結婚或離婚時，此名稱可能會變更，所以不適用於此屬性。這也就是不可能在 Azure AD Connect 安裝精靈中選取 **userPrincipalName**、**mail** 和 **targetAddress** 之類屬性的原因之一。這些屬性也會包含 @ 字元，而 sourceAnchor 中不允許此字元。

### 變更 sourceAnchor 屬性
在 Azure AD 中建立物件並同步處理身分識別之後，無法變更 sourceAnchor 屬性值。

基於這個理由，下列限制適用於 Azure AD Connect：

- 只能在初始安裝期間設定 sourceAnchor 屬性。如果您重新執行安裝精靈，這個選項會是唯讀選項。如果您需要變更此設定，就必須解除安裝後再重新安裝。
- 如果您安裝其他 Azure AD Connect 伺服器，您必須選取如先前所用的相同 sourceAnchor 屬性。如果您稍早已使用 DirSync 並移至 Azure AD Connect，則必須使用 **objectGUID**，因為這是 DirSync 所用的屬性。
- 如果 sourceAnchor 值在物件匯出至 Azure AD 之後變更，則 Azure AD Connect Sync 會擲回錯誤，並在修正問題且於來源目錄中將 sourceAnchor 變回之前，不允許對此物件進行任何其他變更。

## Azure AD 登入
整合您的內部部署目錄與 Azure AD 時，請務必了解同步處理設定對使用者驗證的方式有何影響。Azure AD 使用 userPrincipalName (UPN) 來驗證使用者。不過，當您同步處理使用者時，必須小心選擇要用於 userPrincipalName 值的屬性。

### 選擇 userPrincipalName 的屬性
當您選取屬性以便提供要用於 Azure 的 UPN 值時，應確保

- 屬性值符合 UPN 語法 (RFC 822)，其格式應該是 username@domain
- 這些值的尾碼符合 Azure AD 中其中一個已驗證的自訂網域

在快速設定中，屬性的假定選擇會是 userPrincipalName。如果 userprincipalname 屬性不包含您希望使用者用於登入 Azure 的值，則必須選擇 [自訂安裝]。

### 自訂網域狀態和 UPN
請務必確保 UPN 尾碼有已驗證的網域。

John 是 contoso.com 中的使用者。在您將使用者同步至 Azure AD 目錄 contoso.onmicrosoft.com 之後，您希望 John 使用內部部署 UPN john@contoso.com 來登入 Azure。若要這樣做，您必須將 contoso.com 新增為 Azure AD 中的自訂網域並加以驗證，才能開始同步處理使用者。舉例來說，如果 John 的 UPN 尾碼是 contoso.com，不符合 Azure AD 中已驗證的網域，則 Azure AD 會以 contoso.onmicrosoft.com 取代 UPN 尾碼。

### 無法路由傳送的內部部署網域與 Azure AD 的 UPN
有些組織有無法路由傳送的網域，例如 contoso.local 或簡單單一標籤網域，例如 contoso。您無法確認在 Azure AD 中無法路由傳送的網域。Azure AD Connect 可以僅同步至 Azure AD 中已驗證的網域。當您建立 Azure AD 目錄時，它會建立可路由傳送的網域，而該網域會成為 Azure AD 的預設網域，例如 contoso.onmicrosoft.com。因此，如果您不想要同步至預設的 .onmicrosoft.com 網域，則必須在此類案例中驗證所有其他可路由傳送的網域。

如需有關如何新增和驗證網域的詳細資訊，請參閱[將您的自訂網域名稱新增至 Azure Active Directory](active-directory-add-domain.md)。

Azure AD Connect 會偵測您是否在無法路由傳送的網域環境中執行，並且會適當地警告您不要繼續進行快速設定。如果您是在不可路由傳送的網域中操作，則使用者的 UPN 可能也有無法路由傳送的尾碼。例如，如果您是在 contoso.local 之下執行，Azure AD Connect 會建議您使用自訂設定，而不是使用快速設定。使用自訂設定，在使用者同步至 Azure AD 之後，您能夠指定應做為 UPN 以供登入 Azure 的屬性。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0914_2016-->