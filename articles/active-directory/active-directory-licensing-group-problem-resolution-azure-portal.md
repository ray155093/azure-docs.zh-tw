---

title: "識別及解決 Azure Active Directory 中群組的授權問題 | Microsoft Docs"
description: "如何使用 Azure Active Directory 以群組為基礎的授權識別及解決授權指派問題"
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
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 9a434cf35d7934dc5eb759851fb65ad2a9f06eef
ms.lasthandoff: 02/22/2017


---

# <a name="identifying-and-resolving-license-problems-for-a-group-in-azure-active-directory"></a>識別及解決 Azure Active Directory 中群組的授權問題


Azure Active Directory (Azure AD) 中以群組為基礎的授權介紹使用者授權錯誤狀態的概念。 在本文章中，我們會說明為什麼使用者最後都會處於此狀態。 當授權直接指派給個別使用者，而不使用以群組為基礎的授權時，指派作業會失敗。 例如，當系統管理員對使用者執行 PowerShell Cmdlet `Set-MsolUserLicense` 時，此 Cmdlet 可能會因為一些與商務邏輯相關的原因而失敗，例如授權數不足或兩個服務方案之間有衝突，所以無法同時指派。 此問題會立即回報給執行命令的使用者。

當使用以群組為基礎的授權時，可能會發生相同錯誤，但是當 Azure AD 服務指派授權時會在背景中發生；基於這個原因，它們不能立即與系統管理員通訊。 而是會記錄在使用者物件上，然後透過系統管理入口網站報告。 授權使用者的原始目的永遠不會遺失，但是可能會針對未來的調查和解決套用於作用中狀態或記錄在錯誤狀態。

若要針對每個群組尋找處於錯誤狀態的使用者，請開啟每個群組的刀鋒視窗，在 [授權] 底下會顯示是否有任何使用者處於錯誤狀態的通知。 選取通知以開啟檢視，列出所有受影響的使用者，可以逐一檢視以了解基礎問題。 在本文章中，我們將說明每個潛在的問題和它的解決方法。

## <a name="not-enough-licenses"></a>沒有足夠的授權

群組中指定的其中一個產品沒有足夠的可用授權。 您需要為產品購買更多授權，或從其他使用者或群組釋放未使用的授權。

若要查看有多少授權可用，請移至 [Azure Active Directory]**&gt; [授權] &gt; [所有產品]**。

若要查看哪些使用者及群組在取用授權，請按一下產品。 在 [授權的使用者] 底下，您會看到其授權已直接指派或透過一或多個群組指派的所有使用者。 在 [經過授權的群組] 底下，您會看到已指派該產品的所有群組。

## <a name="conflicting-service-plans"></a>衝突的服務方案

群組中指定的其中一個產品包含服務方案，與已透過不同產品指派給使用者的另一個服務方案相衝突。 某些服務方案設定的方式不同，所以它們不能指派給另一個相關服務方案的相同使用者。

請考量下列範例︰使用者擁有直接指派的 Office 365 企業版 **E1** 授權，所有方案皆啟用。 使用者已新增至獲得 Office 365 企業版 **E3** 產品指派的群組。 本產品包含服務方案，不能在 E1 和 E3 之間重疊，因此群組授權指派將會失敗，且具有「衝突的服務方案」錯誤。 在此範例中，衝突的服務方案是︰

-   SharePoint Online (方案 2) 與 SharePoint Online (方案 1) 衝突

-   Exchange Online (方案 2) 與 Exchange Online (方案 1) 衝突

若要解決此衝突，您必須停用這兩個方案，在直接指派給使用者的 E1 授權上停用，或在 E3 授權中修改整個群組授權指派，然後停用方案。 或者，如果在 E3 授權內容中是多餘的，您可能會決定從使用者移除 E1 授權。

如何解決產品授權的衝突一律屬於系統管理員的決策。 Azure AD 不會自動解決授權衝突。

## <a name="other-products-depend-on-this-license"></a>其他產品相依於此授權

群組中指定的其中一個產品包含服務方案，必須在另一個產品中針對另一個服務方案啟用，才能夠運作。 當 Azure AD 嘗試移除基礎服務方案時會發生此錯誤，例如，因為使用者從群組中移除時。

## <a name="usage-location-not-allowed"></a>不允許使用位置

由於當地法律和法規，某些 Microsoft 服務在所有位置無法使用。 在授權可以指派給使用者之前，系統管理員必須為使用者指定「使用位置」屬性。 這可以在 Azure 入口網站中 [使用者]&gt; [設定檔]&gt; [設定] 區段下設定。

使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。 如果您有使用者在其中方案無法使用的位置，請考慮在群組層級修改授權指派以停用受影響的方案。 或者，您可以將這些使用者移至不同群組，它的授權指派不會與位置衝突。

如果您在不同的位置有使用者，請確定先在使用者物件中正確反映，然後再將使用者新增至具有授權的群組。

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>當群組中有 1 個以上的產品授權時，會發生什麼事？

您可以將 1 個以上的產品授權指派給群組。 例如，您可以將 Office 365 企業版 E3 和 Enterprise Mobility + Security 指派給群組，以便輕鬆地為使用者啟用所有已納入的服務。

Azure AD 會嘗試將群組中指定的所有授權指派給每位使用者。 如果我們因為商務邏輯問題而無法指派其中一個產品 (例如，沒有足夠授權皆與使用者已啟用的其他服務衝突)，我們也無法在群組中指派其他授權。

您可以查看哪個使用者的指派失敗，以及檢查哪些產品受到影響。

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>如何強制處理群組中的授權來解決錯誤？

根據解決錯誤所採取的步驟，可能需要手動觸發群組的處理以更新使用者狀態。

例如，如果您購買更多授權來涵蓋所有使用者，您必須觸發先前失敗之群組的處理，才能完整授權所有使用者成員。 若要這樣做，請尋找 [群組] 刀鋒視窗，開啟 [授權]，然後選取工具列中的 [重新處理] 按鈕。


## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的其他案例，請閱讀

* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例](active-directory-licensing-group-advanced.md)

