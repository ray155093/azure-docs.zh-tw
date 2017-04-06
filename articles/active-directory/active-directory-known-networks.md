---
title: "Azure 傳統入口網站中的已知網路 | Microsoft Docs"
description: "藉由設定已知的網路，您可以避免貴組織擁有的 IP 位址包含在「從多個地理區域登入」和「從具有可疑活動的 IP 位址登入」報告中。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4
ms.lasthandoff: 01/19/2017


---
# <a name="known-networks"></a>已知的網路

> [!div class="op_single_selector"]
> * [Azure 傳統入口網站](active-directory-known-networks.md)
> * [Azure 入口網站](active-directory-known-networks-azure-portal.md)
> 
> 


您可以使用 Azure Active Directory 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。 利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

「從多個地理區域登入」和「從具有可疑活動的 IP 位址登入」可能會不正確地報告貴組織實際擁有的旗標 IP 位址。 

比方說，這可以發生在下列情形： 

* 在波士頓辦公室的使用者已從遠端登入您在舊金山的資料中心，觸發了「從多個地理區域登入」的報告 
* 貴組織的使用者多次嘗試以不正確的密碼登入，觸發了「從具有可疑活動的 IP 位址登入」的報告 

若要避免這些情況下產生誤導的安全性報告，您應該在貴組織的公用 IP 位址清單中新增已知的 IP 位址範圍。    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>若要新增貴組織的公用 IP 位址範圍，請執行下列步驟：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

2. 在左窗格中，按一下 [Active Directory] 。 

    ![已知的網路](./media/active-directory-known-networks/known-netwoks-01.png)

3. 在 [目錄]  索引標籤上，選取您的目錄。

4. 在頂端的功能表中，按一下 [設定] 。 

    ![已知的網路](./media/active-directory-known-networks/known-netwoks-02.png)

5. 在 [組態] 索引標籤上，移至 [貴組織公用 IP 位址範圍] 

    ![已知的網路](./media/active-directory-known-networks/known-netwoks-03.png)

6. 按一下 新增已知的 IP 位址範圍 。

7. 在出現的對話方塊中新增位址範圍，然後在完成時按一下核取按鈕。 

    ![已知的網路](./media/active-directory-known-networks/known-netwoks-04.png)

**其他資源：**

* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)
* [從具有可疑活動的 IP 位址登入](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [從多個地理區域登入](active-directory-reporting-sign-ins-from-multiple-geographies.md)


