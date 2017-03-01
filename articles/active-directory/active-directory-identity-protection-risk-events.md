---
title: "Azure Active Directory 風險事件 | Microsoft Docs"
description: "本主題詳述何謂風險事件。"
services: active-directory
keywords: "azure active directory identity protection, 安全性, 風險, 風險層級, 弱點, 安全性原則"
author: MarkusVi
manager: femila
ms.assetid: ce3b054c-7772-401f-9b06-3d24f6e7ca86
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: e37f1716b459ea8eb0f2d0a5a12e7a3a8ab2c083
ms.openlocfilehash: e2ec0cefb6e009f22c3406ccd1570748dc467ed0
ms.lasthandoff: 02/22/2017


---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory 風險事件

Azure Active Directory 可協助您保護您的身分識別。 保護程序的其中一部分是偵測與您使用者帳戶有關的可疑動作。 該偵測是以調適性機器學習演算法和啟發學習法為基礎。 偵測到的每個可疑動作會儲存在名為*風險事件*的記錄中。 


![風險事件](./media/active-directory-identity-protection-risk-events/91.png)


目前，Azure Active Directory 可偵測六種風險事件類型。

| 風險事件類型 | 風險層級 | 偵測類型 |
| :-- | --- | --- |
| [認證外洩的使用者](#leaked-credentials) | 高 | 離線 |
| [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) | 中型 | 即時 |
| [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) | 中型 | 離線 |
| [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) | 中型 | 即時 |
| [從受感染的裝置登入](#sign-ins-from-infected-devices) | 低 | 離線 |
| [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) | 中型 | 離線|

## <a name="risk-level"></a>風險層級

風險層級屬性是風險事件嚴重性的指標 (高、中或低)。 這個屬性可協助您排定必須採取之動作的優先順序。 

風險事件的嚴重性代表身分識別入侵 (結合它通常引發的雜訊) 預測的訊號強度。

* **高**：高信賴度和高嚴重性風險事件。 這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

* **中**：高嚴重性，但信賴度較低的風險事件，或反之亦然。 這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

* **低**：低信賴度和低嚴重性風險事件。 此事件可能不需要採取立即行動，但與其他風險事件結合時，可能強烈指出身分識別遭到入侵。

![風險層級](./media/active-directory-identity-protection-risk-events/01.png)


## <a name="detection-type"></a>偵測類型

偵測型別屬性是風險事件的偵測時間範圍指標 (即時、離線)。  
目前，大部分的風險事件都是在風險事件發生之後，在後處理作業中以離線方式偵測的。

下表列出偵測類型要在相關報告中顯示所花費的時間。

| 偵測類型 | 報告延遲 |
| --- | --- |
| 即時 | 5 至 10 分鐘 |
| 離線 | 2 至 4 小時 |



## <a name="risk-event-types"></a>風險事件類型

風險事件類型屬性是風險事件記錄已為之建立的可疑動作的識別碼。  
目前，Azure Active Directory 可偵測六種事件類型。

Microsoft 針對偵測程序的持續投資的結果是︰

- 改善現有風險事件的偵測精確度 
- 未來將會新增的新風險事件類型

### <a name="leaked-credentials"></a>認證外洩
Microsoft 安全性研究人員發現外洩的認證公開張貼於黑暗網路 (Dark Web)。 這些認證通常在純文字中找到。 它們會根據 Azure AD 認證進行檢查，如果有相符項目，則會在 Identity Protection 中回報為「認證外洩」。

認證外洩風險事件會被歸類為「高」嚴重性風險事件，因為它們清楚指出攻擊者可使用使用者名稱和密碼。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入
此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。 此風險事件類型的風險層級為「**中**」，因為匿名 IP 本身並未強烈指出帳戶遭到入侵。


### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動
此風險事件類型會識別來自距離遙遠的位置的兩次登入，而根據使用者過去的行為，其中至少有一個位置可能不尋常。 此外，兩次登入之間的時間比使用者從第一個位置移到第二個位置所需的時間還要短，這表示有不同的使用者正在使用相同的認證。 

這種機器學習演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。

不可能的移動通常會明顯指出駭客已能夠成功登入。 不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。 另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。 這些誤判以致此風險事件的風險層級為「**中**」。

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入
此風險事件類型是一種即時登入評估機制，它會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的 / 不熟悉的位置。 系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 從不在熟悉位置清單中的位置登入時，甚至會觸發此風險。 系統有為期 14 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 

不熟悉的位置可以強烈指出攻擊者可嘗試使用遭竊的身分識別。 當使用者正在移動，並試用新裝置或使用新的 VPN 時，可能會發生誤判。 這些誤判以致此事件類型的風險層級為「**中**」。



### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入
此風險事件類型會識別從感染惡意程式碼的裝置登入，已知這類登入會主動與 Bot 伺服器通訊。 讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。 

此風險事件可識別 IP 位址，而不是使用者裝置。 如果單一 IP 位址背後有數個裝置，而只有某些裝置受 Bot 網路控制，則來自其他裝置的登入可能會不必要地觸發此事件，這就是將此風險事件歸類為「**低**」的原因。  

建議您連絡使用者並掃描使用者的所有裝置。 使用者的個人裝置也可能受到感染，或如前所述，可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。 受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)。


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
此風險事件類型會識別在短期內透過多個使用者帳戶多次嘗試登入失敗的 IP 位址。 這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。 這種機器學習演算法會忽略明顯的「誤判」，例如，組織中的其他使用者定期使用的 IP 位址。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。 此事件類型的風險層級為「**中**」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。 





## <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 異常活動報告

在 Azure 傳統入口網站中，有些風險事件已可透過 Azure AD 異常活動報告取得。 

下表列出各種風險事件類型和對應的 **Azure AD 異常活動報告** 。 

| Identity Protection 風險事件類型 | 對應的 Azure AD 異常活動報告 |
|:--- |:--- |
| 認證外洩 |認證外洩的使用者 |
| 不可能到達非典型位置的移動 |異常的登入活動 |
| 從受感染的裝置登入 |從可能受感染的裝置登入 |
| 從匿名 IP 位址登入 |從不明來源登入 |
| 從具有可疑活動的 IP 位址登入 |從具有可疑活動的 IP 位址登入 |
| 從不熟悉的位置登入 |- |


下列 Azure AD 異常活動報告不會納入為 Azure AD Identity Protection 中的風險事件，因此不會透過 Identity Protection 提供。 這些報告仍可在 Azure 傳統入口網站中取得。 不過，這些報告將在未來某個時候淘汰，因為它們正由 Identity Protection 中的風險事件所取代。

* 在多次失敗後登入
* 從多個地理區域登入






## <a name="next-steps"></a>後續步驟
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


