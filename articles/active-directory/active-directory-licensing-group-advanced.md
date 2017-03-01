---

title: "Azure Active Directory 群組型授權其他案例 | Microsoft Docs"
description: "Azure Active Directory 群組型授權的更多案例"
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
ms.sourcegitcommit: dad9a176f5f60b9165c5a55628929460047e5fdd
ms.openlocfilehash: a1510240350d88ee140acb11b3f86fd7985b9427
ms.lasthandoff: 02/22/2017


---

# <a name="azure-active-directory-group-based-licensing-additional-scenarios"></a>Azure Active Directory 群組型授權其他案例

## <a name="group-based-licensing-using-dynamic-groups"></a>使用動態群組的群組型授權

群組型授權可搭配使用任何安全性群組，這表示可與 Azure AD 動態群組結合。 動態群組會針對使用者物件屬性執行規則，以從群組自動新增並移除使用者。

例如，您可以建立多個動態群組，每一組您想要指派給使用者的產品建立一個。 每個群組包含一個查看使用者特定屬性的規則，描述他們應該會收到的授權集。 屬性可以內部部署指派並與 Azure AD 同步處理，或直接在雲端中管理。

指定屬性時，使用者會新增至一或多個這些動態的授權群組。 授權會在不久之後指派給使用者。 當移除該屬性時，使用者會離開群組，且會將授權移除。

### <a name="example"></a>範例

在此範例中，我有內部部署身分識別管理解決方案，可決定哪些使用者應該存取哪些 Microsoft 線上服務。 它會使用 extensionAttribute1 來儲存字串值，表示使用者應該擁有的授權和與 Azure AD 進行同步的 Azure AD Connect。 我想要發佈 Office 365 E5 與 EMS 授權給我的使用者。 在 Azure AD 中，我建立了兩個動態群組，每項產品各一個，因為某些使用者可能需要其中一項產品，但不需要另一項產品。 我針對每個群組指定了動態成員資格規則和授權設定。 它看起來如下︰

#### <a name="o365-e5--base-services"></a>O365 E5 – 基本服務

![O365 E5 基本服務](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5 – 授權使用者

![O365 E5 授權使用者](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

我內部部署修改一位使用者，並將其 extensionAttribute1 設定為 `EMS;E5_baseservices;` 的值，因為我希望他們有兩個授權。 在變更與雲端進行同步處理之後，使用者會自動新增至這兩個群組，然後指派授權。

![設定使用者的 extensionAttribute1](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>修改動態群組成員資格規則

修改現有的群組成員資格規則時需要注意。 變更規則時，會從群組移除所有的使用者、評估規則，且會根據新的條件將使用者新增至群組。

如果群組已指派授權，所有使用者會在程序期間移除該授權。 僅在評估新的規則且使用者加回之後，才會套用新的授權。 這表示使用者可能會遇到服務遺失，或在某些情況下資料遺失。

我們建議您不要變更用於授權指派的群組成員資格規則。 相反地，使用新的規則建立新群組，並指定與原始群組上相同的授權設定。 等到成員已新增且授權已套用至所有使用者。 只有在這時候，您才可以繼續刪除原始的群組。 這個方法可確保安全的預備轉換至新成員資格規則，而不會遺失任何存取權或使用者資料。


## <a name="multiple-groups-and-multiple-licenses"></a>多個群組和多個授權

使用者可以是多個具有授權之群組的成員。 以下是要考量的一些事項：

1. 相同產品的多個授權可以重疊，且將導致所有已啟用的服務套用到使用者。 例如，我們建立兩個授權群組︰E3 – 基本服務包含我們想要先部署至所有使用者的基礎服務；E3 – 擴充服務包含我們想嘗試用於某些使用者的其他服務 (Sway 與規劃)，而 Yammer 仍為已停用以進行未來部署。 在此範例中，已將使用者新增至這兩個群組︰

  ![檢視已啟用服務](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  因此，他們在產品中具有 12 項服務其中之 7 項，卻僅使用此產品的一個授權。

2. 選取 E3 授權會顯示更多詳細資訊，包括哪些群組會造成針對使用者啟用什麼服務的資訊。

  ![依群組檢視已啟用服務](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>與群組授權共存的直接授權

當使用者從群組繼承授權時，無法直接在使用者物件上移除或修改該授權指派。 相反地，任何變更必須在群組中進行，然後由系統傳播給所有使用者。

不過，除了繼承授權使用者之外，可以將相同的 SKU 授權直接指派給使用者。 例如，這可用來僅針對一個使用者啟用來自 SKU 的其他服務，而不會影響其他使用者。

可以移除直接指派的授權，但不會影響繼承的授權。 我們來看看下列從群組中繼承 Office 365 Enterprise E3 授權的使用者，可啟用少數幾個服務。

1. 一開始，使用者只能從 E3 – 基本服務**群組繼承授權。 這會啟用下面所列的 4 個服務計劃︰

  ![E3 群組已啟用服務](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. 按一下 [指派] 按鈕，我們可以直接將 E3 授權指派給使用者 – 在此情況下，我們會停用 Yammer Enterprise 以外的所有服務計劃。

  ![將授權指派給使用者](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 如此一來，使用者仍僅取用 E3 產品的 1 個授權，不過直接指派只會啟用該使用者的 Yammer Enterprise 服務。 您可以在清單中看到群組成員資格所啟用的服務與直接指派︰

  ![繼承與直接指派](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 會使用直接指派允許下列作業︰

  a. Yammer Enterprise 可以直接在使用者物件上關閉。 請注意，相對於其他的服務切換，會啟用開啟/關閉切換 – 這是因為此服務會直接在使用者上啟用，因此可以進行修改。

  b. 其他服務也可啟用，做為直接指派授權的一部分。

  c. [移除] 按鈕可用來移除使用者的直接授權。 您可以看到使用者現在只有繼承的群組授權，且只有原始服務保持啟用。

    ![移除直接指派](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>使用位置

某些 Microsoft 服務無法在所有位置使用。系統管理員必須先指定使用者的 “Usage location” (使用位置) 屬性，才可以將授權指派給使用者。 這可以在 [Azure 入口網站](https://portal.azure.com)中 [使用者]-&gt;[設定檔]-&gt;[設定] 區段下設定。

使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。 如果您在不同的位置有使用者，請確定先在使用者物件中正確反映，然後再將使用者新增至具有授權的群組。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>使用 PowerShell 查看誰具有繼承和直接授權

在公開預覽期間，PowerShell 無法用來完全掌控群組授權指派。 不過，它可以用來探索關於使用者狀態的基本資訊，以及授權是否繼承自群組還是直接指派。 下列程式碼範例會示範系統管理員可以如何產生有關授權指派的基本報告。

1. 執行 `connect-msolservice` cmdlet 來進行驗證，並連線到您的租用戶。

2. `Get-MsolAccountSku` 可用於探索所有租用戶中佈建的 SKU 授權。

  ![使用 Get-msolaccountsku cmdlet](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 在此範例中，我們想要了解哪些使用者是直接指派 EMS 授權，哪些使用者是從群組，或兩者。 我們將使用 PowerShell 指令碼，其中包含兩個函式，可針對使用者物件和 SKU 回答此問題
  ```
  \# Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)

      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if (\$license.GroupsAssigningLicense.Count -eq 0)
              {
                  return \$true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  if (\$assignmentSource -ieq \$user.ObjectId)
                  {
                      return \$true
                  }

              }
              return \$false
          }
      }
      return \$false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)
      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if (\$assignmentSource -ine \$user.ObjectId)
                  {
                      return \$true
                  }
              }
              return \$false
          }
      }
      return \$false
  }
  ```
4. 其餘的指令碼會取得所有使用者，並對每一個使用者執行這些函式，然後將輸出格式化為資料表。

  ```
  \# the license SKU we are interested in
  \$skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {\$\_.isLicensed -eq \$true -and \$\_.Licenses.AccountSKUID -eq \$skuId} | select \`
      ObjectId, \`
      @{Name="SkuId";Expression={\$skuId}}, \`
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly \$\_ \$skuId)}}, \`
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup \$\_ \$skuId)}}
  ```

5. 會出現完整指令碼的輸出，如下所示︰

  ![PowerShell 指令碼輸出](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>限制與已知問題

1. 群組型授權目前不支援「巢狀群組」(包含其他群組的群組)。 如果您將授權套用至巢狀群組，則只有群組的立即第一層級使用者成員會套用授權。

2. 群組型授權目前僅透過 [Azure 入口網站](https://portal.azure.com)公開。 目前，不能使用 PowerShell 來設定或修改群組的授權。

3. [Office 365 系統管理入口網站](https://portal.office.com )目前不支援群組型授權。 如果使用者從群組繼承授權，此授權會顯示在 Office 系統管理入口網站中，做為一般使用者授權。 如果您嘗試修改該授權 (例如，停用授權中的服務，或嘗試移除授權)，則入口網站會傳回錯誤訊息 (因為繼承的群組授權無法直接在使用者上修改)。

  `To assign a license that contains Azure Information Protection Plan 1, you must also assign one of the following service plans: Azure Rights Management.`

4. 當使用者從群組移除且遺失授權時，來自該授權 (例如 Exchange Online 或 SharePoint Online) 的服務計劃會設定為「擱置」狀態，而不是最終已停用狀態。 這是做為預防措施以當系統管理員在群組成員資格管理犯錯時，可避免意外移除使用者資料。

  我們將實作工作流程，其中這些服務計劃的狀態最後將會針對這些使用者完全停用。 直到可以使用以前，針對從群組中移除且不再擁有授權的使用者，某些服務可能會繼續操作。

5. 當授權指派或在極大的使用者群組 (例如 100,000 位使用者) 上修改時，Azure AD 自動化所產生的大量變更可能會對 Azure AD 與內部部署系統之間的目錄同步作業效能產生負面影響。 這可能會在您的環境中造成目錄同步作業的延遲。

6. 授權管理自動化並不會自動對環境中所有類型的變更做出回應。 例如，您可能已用盡授權，且某些使用者處於「沒有足夠的授權」錯誤狀態。 接著，您可以移除部分其他使用者的直接指派授權，以釋出可用的授權數量。 不過，系統將不會自動回應這項變更，並修正該錯誤狀態中的使用者。

  為解決這些類型的限制，您可以前往 Azure AD 中的 [群組] 刀鋒視窗，然後按一下 [重新處理] 按鈕。 這會處理該群組中的所有使用者，並解決錯誤狀態 (如果可能)。

## <a name="next-steps"></a>後續步驟

若要深入了解其他透過群組型授權來管理授權的案例，請參閱

* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](active-directory-licensing-group-migration-azure-portal.md)

