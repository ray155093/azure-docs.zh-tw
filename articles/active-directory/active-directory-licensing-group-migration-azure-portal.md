---

title: "如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權 | Microsoft Docs"
description: "如何使用 Azure Active Directory 從個別使用者授權切換至以群組為基礎的授權"
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
ms.openlocfilehash: d2801351e0ff425b4ee40f995030b3f8541bc3d5
ms.lasthandoff: 02/22/2017


---

# <a name="how-to-migrate-individual-licensed-users-to-group-based-licensing-in-azure-active-directory"></a>如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權

您可能會透過「直接指派」將現有授權部署給組織中的使用者，也就是，使用 PowerShell 指令碼或其他工具來指派個別使用者授權。 如果您想要開始使用以群組為基礎的授權來管理您組織中的授權，您需要移轉方案，以順暢地將現有的解決方案取代為以群組為基礎的授權。

要謹記在心最重要的事情是，您應該避免移轉到以群組為基礎的授權會導致使用者暫時遺失其目前獲得指派授權的情況。 應避免可能造成已指派授權變更的任何處理，以便移除使用者失去服務及其資料存取權的風險。

## <a name="recommended-migration-process"></a>建議的移轉程序

1. 您有現有的自動化 (例如，PowerShell) 管理使用者的授權指派及移除。 保留它的執行原樣。

2. 建立新的授權群組 (或決定要使用哪個現有群組)，並確定所有必要的使用者新增為成員。

3. 將必要的授權指派給這些群組；您的目標應該是反映您現有的自動化 (例如，PowerShell) 的相同授權狀態會套用至這些使用者。

4. 確認授權已套用至這些群組中的所有使用者。 這可以透過檢查每個群組上的處理狀態或檢查稽核記錄來完成。

  - 您可以特別檢查個別使用者，方法是查看他們的授權詳細資料。 您會看到他們具有從群組「直接」和「繼承」指派的相同授權。 這會顯示他們具有平行「指派路徑」，而原始直接指派可以安全地移除。

  - 您可以執行 PowerShell 指令碼，以[確認授權指派給使用者的方式](active-directory-licensing-group-advanced.md)。

  - 當相同的產品授權同時直接和透過群組指派給使用者時，只能有一個授權由使用者取用。 因此不需要額外授權即可執行移轉。

5. 確認沒有授權指派失敗，方法是檢查每個群組中是否有使用者處於錯誤狀態。 如需詳細資訊，請參閱[識別及解決群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)。

6. 請考慮移除原始的直接指派；您可能想要「一波一波」進行，以便先監視使用者子集上的結果。

  您可以離開使用者的原始直接指派，但是當使用者離開其授權群組時，仍會保留原始授權，這可能不是您想要的結果。

## <a name="an-example"></a>範例

我們的組織有 1000 位使用者。 所有使用者都需要 Enterprise Mobility + Security (EMS) 授權。 200 位使用者是在財務部門，而且需要 Office 365 企業版 E3 授權。 我們有內部部署執行中的 PowerShell 指令碼，在使用者就職和離職時新增及移除授權。 我們想要將指令碼取代為以群組為基礎的授權，這樣授權就會自動由 Azure AD 管理。

以下是移轉程序可能的情況︰

1. 使用 Azure 入口網站將 EMS 授權指派給 Azure AD 中的**所有使用者**群組。 將 E3 授權指派給**財務部門**群組，包含所有必要的使用者。

2. 對於每個群組，確認針對所有使用者已完成授權指派。 移至每個群組中的刀鋒視窗，選取 [授權]，然後在 [授權] 刀鋒視窗頂端檢查處理狀態。

  - 尋找「最新的授權變更已套用至所有使用者」以確認處理已完成。

  - 在最上層尋找任何使用者的授權可能尚未成功指派的相關通知。 我們對於某些使用者是否已用盡授權？ 某些使用者是否獲得指派衝突的授權 SKU 阻止繼承群組指派授權？

3. 特別檢查某些使用者以確認其套用直接和群組授權。 移至使用者的刀鋒視窗，選取 [授權]，然後檢查授權狀態。

  - 這是移轉期間預期的使用者狀態︰

      ![預期的使用者狀態](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  這可確認使用者同時具有直接和繼承的授權。 我們看到同時指派 **EMS** 和 **E3**。

  - 選取每個授權，以顯示已啟用服務的相關詳細資料。 這可以用來檢查直接和群組授權是否針對使用者啟用完全相同的服務方案。

      ![檢查服務方案](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. 在確認直接和群組授權是對等的之後，您可以開始從使用者移除直接授權。 您可以藉由直接在入口網站中移除它們以進行測試，然後再執行自動化指令碼，讓它們大量移除。 以下是相同使用者使用入口網站移除直接授權的範例。 請注意，授權狀態會維持不變，但是我們不會再看見直接指派。

  ![直接授權已移除](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的其他案例，請閱讀

* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例](active-directory-licensing-group-advanced.md)

