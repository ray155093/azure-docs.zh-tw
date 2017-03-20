---

title: "將授權指派給 Azure Active Directory 中的群組 | Microsoft Docs"
description: "如何使用 Azure Active Directory 群組型授權指派授權"
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
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 909402b0c603433883757dc3082676462a7c6683
ms.lasthandoff: 03/08/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>將授權指派給 Azure Active Directory 中的使用者群組

在本文中，我們要逐步解說將產品授權指派給群組，以及確認已正確授權群組的所有成員之基本案例。

在本範例中，租用戶包含稱為**人力資源部門**的安全性群組，其包括人力資源部門的所有成員 (在此案例中大約是 1,000 位使用者)。 系統管理員想要將 Office 365 Enterprise E3 授權指派給整個部門；產品中包含的 Yammer Enterprise 服務需要暫時停用，直到稍後部門準備好要開始使用它為止。 系統管理員也要將 Enterprise Mobility + Security 授權部署到相同群組的使用者。

## <a name="step-1-assign-the-required-licenses"></a>步驟 1︰指派所需的授權

1. 使用系統管理員帳戶登入 [**Azure 入口網站**](https://portal.azure.com)。 若要管理授權，帳戶必須是全域管理員角色或使用者帳戶管理員角色。

2. 選取左側瀏覽窗格中的 [更多服務]，然後選取 [Azure Active Directory]。 您可以將此刀鋒視窗加入「我的最愛」，方法為按一下星狀圖示，或將它釘選至入口網站儀表板。

3. 在 [Azure Active Directory] 刀鋒視窗中，選取 [授權]。 這會開啟一個刀鋒視窗，您可以看到並管理租用戶中的所有可授權產品。

4. 在 [所有產品] 下，選取產品名稱，以選取 Office 365 Enterprise E3 和 Enterprise Mobility + Security。 選取刀鋒視窗頂端的 [指派]來啟動工作指派。

  ![所有產品、指派授權](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. 在 [指派授權] 刀鋒視窗中，按一下 [使用者和群組]以開啟 [使用者和群組] 刀鋒視窗。 搜尋群組名稱人力資源部門、選取群組，然後請務必按一下刀鋒視窗底部的 [選取] 來確認。

  ![選取群組](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. 在 [指派授權] 刀鋒視窗中，按一下 [指派選項 (選用)]，其會顯示我們先前選取的兩個產品中所包含的所有服務方案。 尋找 Yammer Enterprise 並將它**關閉**以從產品授權停用該服務。 按一下 [指派選項] 底部的 [確定] 來確認。

  ![指派選項](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. 最後，在 [指派授權] 刀鋒視窗中，按一下刀鋒視窗底部的 [指派] 來完成這項作業。

8. 在右上角顯示的通知會顯示狀態和程序的結果。 如果無法完成指派給群組 (例如，由於群組中已存在的授權)，按一下通知以檢視失敗的詳細資料。

我們現在已指定人事部門群組上的授權範本。 在 Azure AD 中的背景處理已開始處理該群組的所有現有成員。 此初始作業可能需要一些時間，依群組的目前大小而定。 在下一個步驟中，我們將說明如何確認處理程序已完成，以及是否需要進一步注意以解決問題。

> [!NOTE]
> 可以從 Azure AD 中其他位置︰**使用者和群組**啟動相同的指派。 移至 **Azure Active Directory &gt; 使用者和群組 &gt; 所有群組，**找到群組，加以選取並移至 [授權] 索引標籤。 刀鋒視窗頂端的 [指派] 按鈕會開啟 [授權指派] 刀鋒視窗。

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>步驟 2︰確認已完成初始設定

1. 移至 **Azure Active Directory &gt; 使用者和群組 &gt; 所有群組**並尋找授權所指派的人力資源部門群組。

2. 在人力資源部門群組刀鋒視窗中，選取 [授權] 以快速確認授權是否已完全指派給使用者，以及是否有任何錯誤需要探究。 可用資訊如下：

  - 目前指派給群組的產品授權清單。 選取項目來顯示已啟用且要進行變更的特定服務。

  - 對群組進行的最新授權變更狀態︰變更是否要進行處理，或是否已完成處理所有使用者的成員。

  - 如果發生錯誤，無法指派授權給處於錯誤狀態之使用者的相關資訊。

  ![指派選項](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. 關於授權處理的詳細資訊位於 **Azure Active Directory &gt; Users and groups &gt;
    *groupname* &gt; 稽核記錄檔**：

  - 活動︰**開始將群組型授權套用至使用者**。 當我們的系統拾取群組的授權指派變更，並開始將其套用到所有使用者成員時，它會進行記錄。 它包含已進行之變更的相關資訊。

  - 活動︰**完成將群組型授權套用至使用者**。 當系統完成處理群組中的所有使用者時，它會進行記錄。 它包含已成功處理的使用者人數和無法指派群組授權的使用者人數之摘要。

## <a name="step-3-checking-for-license-problems-and-resolving-them"></a>步驟 3︰檢查授權問題及解決這些問題

1. 移至 **Azure Active Directory &gt; 使用者和群組 &gt; 所有群組**並尋找授權所指派的人力資源部門群組。

2. 在 [人力資源部門] 群組刀鋒視窗中，選取 [授權]。 刀鋒視窗頂端的通知顯示有 10 位使用者無法指派授權。 按一下隨即開啟一份此群組為授權錯誤狀態的所有使用者。

3. **無法指派**資料行告訴我們，兩個產品授權都無法指派給使用者。 **排名在前的失敗原因**包含失敗的原因，在此案例中為**衝突服務方案**。

  ![失敗的指派](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. 選取使用者以開啟 [授權] 刀鋒視窗，顯示目前指派給使用者的所有授權。 在此範例中，我們可以看到使用者擁有繼承自 **Kiosk 使用者** 群組的 Office 365 Enterprise E1 授權。 這會與系統嘗試從**人力資源部門**群組套用的 E3 授權衝突；如此一來，該群組沒有任何授權會指派給使用者。

  ![檢視使用者的授權](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. 若要解決這個問題，我們會從 **Kiosk 使用者**群組移除使用者。 在 Azure AD 處理變更之後，會正確指派**人力資源部門**授權。

  ![已正確指派的授權](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的功能集，請閱讀

* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何將個別的已授權使用者移轉成 Azure Active Directory 中的群組型授權 (英文)](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](active-directory-licensing-group-advanced.md)

