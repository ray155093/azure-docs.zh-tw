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
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>使用群組來管理 Azure Active Directory 授權的案例、限制及已知問題

使用下列資訊和範例，以更深入了解 Azure Active Directory (Azure AD) 群組型授權。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>對動態群組使用群組型授權

您可以對任何安全性群組使用群組型授權，這表示可與 Azure AD 動態群組結合。 動態群組會針對使用者物件屬性執行規則，以自動新增和移除群組的使用者。

例如，您可以建立多個動態群組，每一組您想要指派給使用者的產品建立一個。 每個群組包含一個查看使用者特定屬性的規則，描述他們應該會收到的授權集。 您可以在內部部署指派屬性，再將它與 Azure AD 同步，或者，您也可以直接在雲端管理屬性。

當您指定屬性時，使用者會新增至一或多個這些動態授權群組。 之後，授權會很快指派給使用者。 移除屬性時，使用者會離開群組，且會移除授權。

### <a name="example"></a>範例

假設有一個內部部署身分識別管理解決方案，可決定哪些使用者可存取哪些 Microsoft Web 服務。 它使用 **extensionAttribute1** 來儲存字串值，代表使用者應該擁有的授權。 Azure AD Connect 會將它與 Azure AD 同步。

例如，目標是將 Office 365 企業版 E5 和 Enterprise Mobility + Security 授權分發給使用者。 在 Azure AD 中建立兩個動態群組，每個產品各一個。 這是因為某些使用者可能需要其中一個產品，而非另一個產品。 然後，在每個群組上指定動態成員資格規則和授權設定。 它看起來如下︰

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 企業版 E5︰基本服務

![Office 365 企業版 E5 基本服務的螢幕擷取畫面](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security︰授權的使用者

![Enterprise Mobility + Security 授權的使用者螢幕擷取畫面](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

在此範例中，修改一個使用者，並將其 extensionAttribute1 的值設為 `EMS;E5_baseservices;`。 這是因為您想要這位使用者擁有兩個授權。 請注意，您可以在內部部署做此修改。 在變更與雲端同步之後，使用者會自動新增至這兩個群組，並且指派授權。

![顯示如何設定使用者的 extensionAttribute1 的螢幕擷取畫面](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>修改動態群組成員資格規則

修改現有群組的成員資格規則時請小心。 變更規則時，將會從群組中移除所有使用者。 此規則經過評估，然後根據新的條件，將使用者新增至群組。

如果群組已獲派授權，則所有使用者都會具備過程中移除的那些授權。 僅在評估新的規則並加回使用者之後，才會套用新的授權。 這表示使用者可能會遇到服務遺失，或在某些情況下遺失資料。

最好不要在用於授權指派的群組上變更成員資格規則。 相反地，使用新的規則建立新群組，並指定與原始群組上相同的授權設定。 等到成員已新增且授權已套用至所有使用者。 唯有如此，您才可以刪除原始群組。 這個方法可確保以安全、分階段的方式轉換至新的成員資格規則，使用者完全不會遺失存取權或資料。


## <a name="multiple-groups-and-multiple-licenses"></a>多個群組和多個授權

使用者可以是多個具有授權之群組的成員。 以下是要考量的一些事項：

- 相同產品的多個授權可能重疊，導致所有已啟用的服務套用至使用者。 下列範例顯示兩個授權群組︰「E3 基本服務」包含最先部署給所有使用者的基礎服務。 「E3 擴充服務」包含只部署給某些使用者的其他服務 (Sway 和 Planner)。 請注意，在未來的部署中，Yammer 仍然停用。 在此範例中，已將使用者新增至這兩個群組︰

  ![已啟用服務的螢幕擷取畫面](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  因此，使用者在產品的 12 項服務中已啟用 7 項，但只對此產品使用一個授權。

- 選取 E3 授權會顯示更多詳細資訊，包括哪些群組造成對使用者啟用什麼服務的相關資訊。

  ![依群組啟用服務的螢幕擷取畫面](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>直接授權與群組授權共存

當使用者從群組繼承授權時，無法直接在使用者物件上移除或修改該授權指派。 相反地，任何變更必須在群組中進行，然後由系統傳播給所有使用者。

不過，除了繼承的授權，還可以將相同產品授權直接指派給使用者。 例如，這可用來僅針對一個使用者啟用產品中的其他服務，而不會影響其他使用者。

可以移除直接指派的授權，但不會影響繼承的授權。 例如，下列使用者從群組繼承 Office 365 企業版 E3 授權。

1. 一開始，使用者只從「E3 基本服務」群組繼承授權。 這會啟用 4 個服務方案，如下圖所示。

  ![E3 群組啟用的服務的螢幕擷取畫面](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. 您可以按一下 [指派]，直接將 E3 授權指派給使用者。 在此案例中，您準備停用 Yammer Enterprise 以外的所有服務方案。

  ![如何直接將授權指派給使用者的螢幕擷取畫面](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. 因此，使用者仍然只使用 E3 產品的一個授權。 但是，直接指派只會針對該使用者啟用 Yammer Enterprise 服務。 下圖顯示依群組成員資格和直接指派分別啟用哪些服務。

  ![繼承和直接指派的螢幕擷取畫面](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 使用直接指派時允許下列作業︰

  a. 可以直接在使用者物件上關閉 Yammer Enterprise。 請注意，這項服務已啟用 [開啟/關閉] 切換，不同於其他服務切換。 這是因為此服務直接在使用者上啟用，因此可以修改。

  b. 其他服務也可啟用，做為直接指派授權的一部分。

  c. [移除] 按鈕可用來移除使用者的直接授權。 您可以看到使用者現在只有繼承的群組授權，且只有原始服務保持啟用。

    ![顯示如何移除直接指派的螢幕擷取畫面](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>使用位置

並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須指定使用者的 [使用位置]屬性，才能將授權指派給使用者。 這可以在 [Azure 入口網站](https://portal.azure.com)的 [使用者]&gt; [設定檔]&gt; [設定] 區段下設定。

如果是群組授權指派，未指定使用位置的任何使用者會繼承目錄的位置。 如果您在不同的位置有使用者，請確定先在使用者物件中正確反映，然後再將使用者新增至具有授權的群組。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>使用 PowerShell 查看誰具有繼承和直接授權

在 Azure AD 版本的公開預覽期間，無法使用 PowerShell 來完全掌控群組授權指派。 不過，它可以用來探索關於使用者狀態的基本資訊，以及判斷授權是繼承自群組還是直接指派。 下列程式碼範例示範系統管理員如何產生有關授權指派的基本報告。

1. 執行 `connect-msolservice` Cmdlet 來驗證並連線至您的租用戶。

2. `Get-MsolAccountSku` 可用於探索租用戶中佈建的所有產品授權。

  ![Get-Msolaccountsku Cmdlet 的螢幕擷取畫面](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. 在此範例中，我們想要查明哪些使用者的 Enterprise Mobility + Security 授權是直接指派、從群組指派或兩者都有。 您可以使用下列 PowerShell 指令碼。
  
  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              #If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses)
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. 指令碼的其餘部分會取得所有使用者，並對每一個使用者執行這些函式。 然後將輸出格式化為資料表。
    
  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. 會出現完整指令碼的輸出，如下所示︰

  ![PowerShell 指令碼輸出的螢幕擷取畫面](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>限制與已知問題

如果您使用群組型授權，最好先熟悉下列的限制和已知問題清單。

- 群組型授權目前不支援「巢狀群組」(包含其他群組的群組)。 如果您將授權套用至巢狀群組，則只有群組的直接第一層級使用者成員會套用授權。

- 一或多個授權繼承自群組成員資格，無法修改。 若要檢視或修改群組型授權，請瀏覽 Azure 管理入口網站。

- [Office 365 系統管理入口網站](https://portal.office.com )目前不支援群組型授權。 如果使用者從群組繼承授權，此授權在 Office 系統管理入口網站中會顯示為一般使用者授權。 如果您嘗試修改該授權 (例如，停用授權中的服務)，或嘗試移除授權，入口網站會傳回錯誤訊息。 無法直接修改使用者繼承的群組授權。

- 當使用者從群組移除且失去授權時，來自該授權的服務方案 (例如 Exchange Online 或 SharePoint Online) 會設定為「已暫止」狀態。 服務方案不會設定為最終的已停用狀態。 這是預防措施，目的是避免系統管理員在群組成員資格管理時犯錯，而意外移除使用者資料。

- 當指派或修改巨大群組 (例如 100,000 個使用者) 的授權時，這可能會影響效能。 具體來說，Azure AD 自動化所產生的大量變更，可能會降低 Azure AD 和內部部署系統之間目錄同步作業的效能。 這可能會在您的環境中造成目錄同步作業的延遲。

- 授權管理自動化並不會自動對環境中所有類型的變更做出回應。 例如，您可能已用盡授權，導致某些使用者處於錯誤狀態。 若要釋出可用的授權數量，您可以移除其他使用者的一些直接指派授權。 不過，系統不會自動回應這項變更，也不會修正處於該錯誤狀態的使用者。

  若要解決這幾種限制，您可以前往 Azure AD 中的 [群組] 刀鋒視窗，然後按一下 [重新處理]。 這會處理該群組中的所有使用者，並解決錯誤狀態 (如果可能)。

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組型授權來管理授權的其他案例，請參閱：

* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](active-directory-licensing-group-migration-azure-portal.md)

