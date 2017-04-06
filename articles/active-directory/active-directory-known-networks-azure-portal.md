---
title: "Azure Active Directory 中的具名網路 | Microsoft Docs"
description: "藉由設定具名網路，您可以避免貴組織擁有的 IP 位址產生「從多個地理區域登入」和「從具有可疑活動的 IP 位址登入」風險事件的誤判。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Azure Active Directory 中的具名網路

> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-known-networks-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-known-networks.md)
>
>


Azure Active Directory 會針對每一個偵測到的[風險事件](active-directory-identity-protection-risk-events.md)建立記錄。 利用 Azure Active Directory 安全報告可取得的風險事件資訊，您可以深入了解環境中使用者帳戶遭入侵的可能性。   

針對您組織實際擁有的 IP 位址，Azure Active Directory 可能會偵測到*不可能進入非慣用位置*和*從具有可以活動的 IP 位址登入*[風險事件類型](active-directory-reporting-risk-events.md#risk-event-types)的誤判。 

比方說，這可以發生在下列情形：

- 在波士頓辦公室的使用者已從遠端登入您在舊金山的資料中心，產生了*從多個地理區域登入*風險事件

- 貴組織的使用者多次嘗試以不正確的密碼登入，產生了*從具有可疑活動的 IP 位址登入*風險事件

若要避免這些情況下產生誤導的風險事件，您應該在貴組織的公用 IP 位址清單中新增具名的 IP 位址範圍。    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>若要新增貴組織的公用 IP 位址範圍，請執行下列步驟：

1. 登入 Azure 管理入口網站。

2. 在左窗格中，按一下 [Active Directory] 。

    ![已知的網路](./media/active-directory-known-networks-azure-portal/01.png)

3. 在 [目錄] 刀鋒視窗中的 [管理] 區段中，按一下 [具名網路]。

    ![已知的網路](./media/active-directory-known-networks-azure-portal/02.png)


4. 按一下 [新增位置]

    ![已知的網路](./media/active-directory-known-networks-azure-portal/03.png)

5. 在 [新增]刀鋒視窗中，執行︰

    ![已知的網路](./media/active-directory-known-networks-azure-portal/04.png)

    a. 在 [名稱] 文字方塊中，輸入名稱。

    b.這是另一個 C# 主控台應用程式。 在 [IP 範圍] 文字方塊中，輸入 IP 範圍。 IP 範圍必須採用 CIDR 格式。 針對大量更新，您可以上傳含有 IP 範圍的 CSV 檔。 透過上傳，會將檔案中的 IP 位址新增到清單中，而不是覆寫清單。

    c. 按一下 [建立] 。


**其他資源：**

* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)
* [從具有可疑活動的 IP 位址登入](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [從多個地理區域登入](active-directory-reporting-sign-ins-from-multiple-geographies.md)

