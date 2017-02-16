---
title: "Azure AD Connect Health 操作。"
description: "本文說明可以在您部署 Azure AD Connect Health 之後執行的其他操作。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/31/2017
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: e4d2c77fca602661ae3e061c1c0fc84ebb786d32
ms.openlocfilehash: 1c43589455e8f5c744538634d1eaad7a7f05cf5d


---
# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect Health 操作
下列主題說明可以使用 Azure AD Connect Health 執行的各種操作。

## <a name="enable-email-notifications"></a>啟用電子郵件通知
您可以設定 Azure AD Connect Health 服務在產生表示您的身分識別基礎結構狀況不良的警示時傳送電子郵件通知。 產生警示時，以及當該警示標示為已解決時，會發生這個情況。 請依照下列指示，設定電子郵件通知。

![Azure AD Connect Health 電子郵件通知探索](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> 電子郵件通知預設為停用狀態。
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>啟用 Azure AD Connect Health 電子郵件通知
1. 為您希望接收電子郵件通知的服務開啟 [警示] 刀鋒視窗。
2. 按一下動作列中的 [通知設定] 按鈕。
3. 將 [電子郵件通知] 開換切換到 [開啟]。
4. 選取設定所有全域系統管理員接收電子郵件通知的核取方塊。
5. 如果您希望從其他任何電子郵件地址收到電子郵件通知，您可以在 [其他電子郵件收件者] 方塊中指定這些電子郵件地址。 若要從此清單中移除某個電子郵件地址，以滑鼠右鍵按一下該項目，然後選取 [刪除]。
6. 若要完成變更，按一下 [儲存]。 只有在您選取 [儲存] 之後，所有變更才會生效。

## <a name="delete-a-server-or-service-instance"></a>刪除伺服器或服務執行個體

在某些情況下，您可能希望某個伺服器不受到監視。 請依照下列指示，從 Azure AD Connect Health 服務移除伺服器。

刪除伺服器時，請注意下列事項：

* 這個動作將會「停止」從該伺服器收集任何後續的資料。 此伺服器將會從監視服務移除。 進行此動作之後，您將無法檢視此伺服器的新警示、監視或使用情況分析資料。
* 這個動作將「不會」從伺服器解除安裝或移除 Health 代理程式。 如果您在執行此步驟之前還未解除安裝 Health 代理程式，您可能會在伺服器上看到與 Health 代理程式相關的錯誤事件。
* 這個動作將「不會」刪除已從此伺服器收集的資料。 根據 Microsoft Azure 資料保留原則，將會刪除該資料。
* 執行此動作之後，如果您希望再次開始監視相同的伺服器，您必須先解除安裝此伺服器上的 Health 代理程式，然後再重新安裝。

### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>從 Azure AD Connect Health 服務刪除伺服器
適用於 AD FS 和 Azure AD Connect (Sync) 的 Azure AD Connect Health：

1. 選取要移除的伺服器名稱，以便從 [伺服器清單] 刀鋒視窗開啟 [伺服器] 刀鋒視窗。
2. 在 [伺服器] 刀鋒視窗上，按一下動作列中的 [刪除] 按鈕。
3. 在確認方塊中輸入伺服器名稱，以確認刪除伺服器的動作。
4. 按一下 [刪除] 按鈕。

適用於 AD DS 的 Azure AD Connect Health：

1. 開啟網域控制站儀表板。
2. 選取要移除的網域控制站。
3. 按一下動作列中的 [刪除所選的] 按鈕。
4. 確認刪除伺服器的動作。
5. 按一下 [刪除] 按鈕。

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>從 Azure AD Connect Health 服務刪除服務執行個體
在某些情況下，您可能希望移除某個服務執行個體。 請依照下列指示，從 Azure AD Connect Health 服務移除服務執行個體。

刪除服務執行個體時，請注意下列事項：

* 這個動作將會從監視服務移除目前的服務執行個體。
* 這個動作將「不會」從當做此服務執行個體一部分受到監視的任何伺服器解除安裝或移除 Health 代理程式。 如果您在執行此步驟之前還未解除安裝 Health 代理程式，您可能會在伺服器上看到與 Health 代理程式相關的錯誤事件。
* 根據 Microsoft Azure 資料保留原則，將會從此伺服器執行個體刪除所有資料。
* 執行此動作之後，如果您希望開始監視服務，請先解除安裝所有伺服器上將受到監視的 Health 代理程式，然後再重新安裝。 執行此動作之後，如果您希望再次開始監視相同的伺服器，您必須先解除安裝此伺服器上的 Health 代理程式，然後再重新安裝。

#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>從 Azure AD Connect Health 服務刪除服務執行個體
1. 選取您想要移除的服務識別項 (伺服陣列名稱)，以便從 [服務清單] 刀鋒視窗開啟 [服務] 刀鋒視窗。
2. 在 [伺服器] 刀鋒視窗上，按一下動作列中的 [刪除] 按鈕。
3. 在確認方塊中輸入服務名稱以進行確認。 (例如：sts.contoso.com)
4. 按一下 [刪除] 按鈕。
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>使用角色型存取控制管理存取權
### <a name="overview"></a>Overview
[角色型存取控制](../role-based-access-control-configure.md) ，可為全域系統管理員以外的使用者和/或群組提供 Azure AD Connect Health 服務的存取。 這是透過指派角色給目標使用者和/或群組所達成，並提供一個機制來限制您目錄內的全域系統管理員。

#### <a name="roles"></a>角色
Azure AD Connect Health 支援下列內建角色。

| 角色 | 權限 |
| --- | --- |
| 擁有者 |擁有者可以在 Azure AD Connect Health 內檢視***管理存取*** (例如將角色指派給使用者/群組)、從入口網站***檢視所有資訊*** (例如檢視警示) 以及***變更設定*** (例如電子郵件通知)。 <br>根據預設，Azure AD 全域系統管理員會被指派此角色，而且無法變更。 |
| 參與者 |參與者可以在 Azure AD Connect Health 內***檢視入口網站中的所有資訊*** (例如檢視警示) 以及***變更設定*** (例如電子郵件通知)。 |
| 讀取者 |讀者可以從入口網站的 Azure AD Connect Health 內***檢視所有資訊*** (例如檢視警示)。 |

其他即使有在入口網站體驗中提供的所有角色 (例如「使用者存取系統管理員」或「DevTest Labs 使用者」)，對 Azure AD Connect Health 內的存取則沒有影響。

#### <a name="access-scope"></a>存取範圍
Azure AD Connect 支援兩個層級的管理存取：

* ***所有服務執行個體***：這是針對大多數客戶建議的路徑，可跨所有角色類型控制將由 Azure AD Connect Health 監視的所有服務執行個體 (例如 ADFS 伺服器陣列) 的存取。
* ***服務執行個體***：在某些情況下，您可能需要根據角色類型或服務執行個體來隔離存取。 在此情況下，您可以管理服務執行個體層級的存取。  

如果使用者有權存取目錄或服務執行個體層級，則會被授與權限。

### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>如何允許使用者或群組存取 Azure AD Connect Health
#### <a name="steps-1-select-the-appropriate-access-scope"></a>步驟 1：選取適當的存取範圍
若要允許 Azure AD Connect Health 內的*所有服務執行個體*層級使用者存取，請開啟 Azure AD Connect Health 中的主要刀鋒視窗。<br>

#### <a name="step-2-add-users-groups-and-assign-roles"></a>步驟 2：新增使用者、群組及指派角色
1. 按一下 [設定] 區段中的 [使用者] 部分。<br>
   ![Azure AD Connect Health RBAC 主要刀鋒視窗](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. 選取 [新增]
3. 選取「角色」(例如「擁有者」)<br>
   ![Azure AD Connect Health RBAC 新增使用者](./media/active-directory-aadconnect-health/RBAC_add.png)
4. 輸入目標使用者或群組的名稱或識別碼。 您可以同時選取一或多個使用者或群組。 按一下 [選取]。
   ![Azure AD Connect Health RBAC 選取使用者](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. 選取 [確定]。<br>
6. 一旦完成角色指派，使用者和/或群組將會在清單中顯示。<br>
   ![Azure AD Connect Health RBAC 使用者清單](./media/active-directory-aadconnect-health/RBAC_user_list.png)

這些步驟可為列出的使用者和群組提供存取權 (依據被指派的角色)。

> [!NOTE]
> * 全域系統管理員一律擁有所有作業的完整存取權，但全域系統管理員帳戶不會出現在上述清單中。
> * Azure AD Connect Health 內「不」支援 [邀請使用者] 功能。
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>步驟 3：與使用者或群組共用刀鋒視窗位置
1. 指派權限之後，使用者可以前往 [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth)以存取 Azure AD Connect Health。
2. 在刀鋒視窗上，使用者可以釘選刀鋒視窗或其他組件到儀表板，只要按一下 [釘選到儀表板] 即可<br>
   ![Azure AD Connect Health RBAC 釘選刀鋒視窗](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> 被指派「讀取者」角色的使用者將無法執行「建立」作業，以從 Azure Marketplace 取得 Azure AD Connect Health 擴充。 這位使用者仍可前往上述連結以存取刀鋒視窗。 為方便之後使用，使用者可以將刀鋒視窗釘選到儀表板。
>
>

### <a name="remove-users-andor-groups"></a>移除使用者和/或群組
您可以移除新增到 Azure AD Connect Health 角色型存取控制組件的使用者或群組，方法是以滑鼠右鍵按一下並選取 [移除]。<br>
![Azure AD Connect Health RBAC 移除使用者](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>相關連結
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用 Azure AD Connect Health 進行同步處理](active-directory-aadconnect-health-sync.md)
* [在 AD DS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本歷程記錄](active-directory-aadconnect-health-version-history.md)


<!--HONumber=Dec16_HO3-->


