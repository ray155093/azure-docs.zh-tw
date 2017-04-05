---

title: "解決 Azure Active Directory 中群組的授權問題 | Microsoft Docs"
description: "當您使用 Azure Active Directory 以群組為基礎的授權時，如何識別及解決授權指派問題"
services: active-directory
keywords: "Azure AD 授權"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 68155ebaa6af36500bfe856c9bcd49f5efb6cbc2
ms.lasthandoff: 03/23/2017


---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>識別及解決 Azure Active Directory 中群組的授權指派問題

Azure Active Directory (Azure AD) 中以群組為基礎的授權會介紹使用者授權錯誤狀態的概念。 在本文章中，我們會說明使用者最後都處於此狀態的原因。

當您將授權直接指派給個別使用者，而不使用以群組為基礎的授權時，指派作業會失敗。 例如，當您對使用者執行 PowerShell Cmdlet `Set-MsolUserLicense` 時，此 Cmdlet 可能會因為一些商務邏輯相關原因而失敗。 例如，可能是授權數量不足，或無法同時指派兩個服務方案的衝突。 系統會立即向您回報此問題。

當您使用以群組為基礎的授權時，可能會發生相同錯誤，但是當 Azure AD 服務指派授權時會在背景中發生。 基於這個原因，無法立即向您通知這些錯誤。 而是會記錄在使用者物件上，然後透過系統管理入口網站報告。 請注意，授權使用者的原始目的永遠不會遺失，但是會針對未來的調查和解決記錄於錯誤狀態。

若要針對每個群組尋找處於錯誤狀態的使用者，請開啟每個群組的刀鋒視窗。 [授權] 底下會有一份通知，顯示是否有任何使用者處於錯誤狀態。 選取這份通知，以開啟所有受影響的使用者清單。 您可以個別檢視使用者，以了解根本問題。 在本文中，我們將說明每個潛在的問題和它的解決方法。

## <a name="not-enough-licenses"></a>沒有足夠的授權

**問題：**群組中指定的其中一項產品沒有足夠的可用授權。 您需要為產品購買更多授權，或從其他使用者或群組釋放未使用的授權。

若要查看有多少授權可用，請移至 [Azure Active Directory] > [授權] > [所有產品]。

若要查看哪些使用者及群組在取用授權，請按一下某項產品。 在 [已授權的使用者] 底下，您會看到其授權已直接指派或透過一或多個群組指派的所有使用者。 在 [已授權的群組] 底下，您會看到已被指派該產品的所有群組。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _CountViolation_。

## <a name="conflicting-service-plans"></a>衝突的服務方案

**問題：**群組中指定的其中一個產品包含服務方案，與已透過不同產品指派給使用者的另一個服務方案相衝突。 某些服務方案設定的方式不同，所以無法將它們指派給另一個相關服務方案的相同使用者。

請思考一下下列範例。 使用者擁有直接指派的 Office 365 企業版 **E1** 授權，所有方案皆啟用。 使用者已新增至獲得 Office 365 企業版 **E3** 產品指派的群組。 本產品包含的服務方案不能與 E1 包含的方案重疊，因此群組授權指派將會失敗，且具有「衝突的服務方案」錯誤。 在此範例中，衝突的服務方案是︰

-   SharePoint Online (方案 2) 與 SharePoint Online (方案 1) 衝突。
-   Exchange Online (方案 2) 與 Exchange Online (方案 1) 衝突。

若要解決此衝突，您必須停用這兩個方案，不是在直接指派給使用者的 E1 授權上停用。 就是，必須在 E3 授權中修改整個群組授權指派並停用方案。 或者，如果在 E3 授權內容中是多餘的，您可能會決定從使用者移除 E1 授權。

如何解決產品授權的衝突一律屬於系統管理員的決策。 Azure AD 不會自動解決授權衝突。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _MutuallyExclusiveViolation_。

## <a name="other-products-depend-on-this-license"></a>其他產品相依於此授權

**問題：**群組中指定的其中一個產品包含服務方案，必須在另一個產品中針對另一個服務方案啟用，才能夠運作。 當 Azure AD 嘗試移除基礎服務方案時會發生此錯誤。 例如，因為使用者從群組中移除而造成此問題。

若要解決這個問題，您必須確定所需的方案仍透過其他方法指派給使用者，或已停用這些使用者的相依服務。 之後，您可以適當地移除這些使用者的群組授權。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _DependencyViolation_。

## <a name="usage-location-isnt-allowed"></a>不允許使用位置

**問題：**由於當地法律和法規，無法在所有位置使用某些 Microsoft 服務。 您必須為使用者指定「使用位置」屬性，才可以將授權指派給使用者。 您可以在 Azure 入口網站中 [使用者] > [設定檔] > [設定] 區段之下執行此作業。

當 Azure AD 嘗試將群組授權指派給不支援其使用位置的使用者時，將會發生失敗並將此錯誤記錄於該使用者。

若要解決這個問題，請從授權群組不支援的位置中移除使用者。 或者，如果目前的使用位置值不代表實際使用者的位置，您可以進行修改，以便下一次正確指派授權 (如果支援新的位置)。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _ProhibitedInUsageLocationViolation_。

> [!NOTE]
> 當 Azure AD 指派群組授權時，未指定使用位置的任何使用者會繼承目錄的位置。 我們建議系統管理員先為使用者設定正確的使用位置值，再使用以群組為基礎的授權，以符合當地法規。

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>當群組中有一個以上的產品授權時，會發生什麼事？

您可以將一個以上的產品授權指派給群組。 例如，您可以將 Office 365 企業版 E3 和 Enterprise Mobility + Security 指派給群組，以便輕鬆地為使用者啟用所有已納入的服務。

Azure AD 會嘗試將群組中指定的所有授權指派給每位使用者。 如果 Azure AD 因為商務邏輯問題而無法指派其中一個產品 (例如，沒有足夠授權供所有人使用，或與使用者已啟用的其他服務衝突)，我們也無法在群組中指派其他授權。

您可以查看哪些使用者的指派失敗，以及檢查哪些產品受到影響。

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>如何強制處理群組中的授權來解決錯誤？

根據您為了解決錯誤所採取的步驟，可能需要手動觸發群組的處理以更新使用者狀態。

例如，如果您藉由移除使用者的直接授權指派來釋出一些授權，您必須觸發先前失敗之群組的處理，才能完整授權所有使用者成員。 若要這樣做，請尋找 [群組] 刀鋒視窗，開啟 [授權]，然後選取工具列中的 [重新處理] 按鈕。

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的其他案例，請閱讀下列各項：

* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](active-directory-licensing-group-advanced.md)

