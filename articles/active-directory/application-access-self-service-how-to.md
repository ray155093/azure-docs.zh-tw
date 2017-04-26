---
title: "如何設定自助式應用程式指派 | Microsoft Docs"
description: "啟用自助應用程式存取以允許使用者尋找其應用程式"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: af0ec17d319f0a2911e1b50a5ed45dd59dafa752
ms.lasthandoff: 04/11/2017


---

# <a name="how-to-configure-self-service-application-assignment"></a>如何設定自助應用程式指派

您必須針對想要允許使用者進行自助探索並要求存取權的所有應用程式啟用「自助應用程式存取」，使用者才能從其存取面板進行應用程式自助探索。

此絕佳功能有助於節省您 IT 人員的時間與成本，而且我們強烈建議您在搭配 Azure Active Directory 進行現代化應用程式部署時使用。

您可以使用這項功能︰

-   讓使用者不需要 IT 人員的協助，即可從[應用程式存取面板](https://myapps.microsoft.com/)進行應用程式自助探索。

-   將那些使用者新增到預先設定的群組，以便您可以查看哪些人要求存取權、移除存取權，以及管理指派給他們的角色。

-   您可以讓商務核准者核准應用程式存取要求，而不需要由 IT 人員進行。

-   您可以設定最多 10 個可以核准應用程式存取要求的人。

-   您可以讓商務核准者直接從商務核准者的[應用程式存取面板](https://myapps.microsoft.com/)設定密碼，供那些使用者用來登入應用程式。

-   您可以自動將已為其指派自助服務的使用者直接指派到應用程式角色。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>啟用自助應用程式存取以允許使用者尋找其應用程式

自助應用程式存取這個絕佳方式可以讓使用者進行應用程式自助探索，而且可以讓商務群組核准那些應用程式的存取權。 您可以允許商務群組直接從其存取面板針對「密碼單一登入應用程式」管理指派給哪些使用者的認證。

若要啟用對應用程式的自助存取，請依照下列步驟執行：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，並以「全域系統管理員」的身分登入。

2.  透過按一下左邊主瀏覽功能表中的 [更多服務]，以開啟 [Azure Active Directory 擴充]。

3.  在篩選搜尋方塊中輸入 **Azure Active Directory**，然後選取 [Azure Active Directory] 項目。

4.  從 Azure Active Directory 左邊瀏覽功能表，按一下 [企業應用程式]。

5.  按一下 [所有應用程式] 以檢視您的應用程式清單。

  * 若沒有看到您要在這裡顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6.  從該清單選取您要啟用自助存取的應用程式。

7.  應用程式載入之後，請按一下應用程式左邊瀏覽功能表中的 [自助]。

8.  若要為此應用程式啟用自助應用程式存取，請將 [要允許使用者要求此應用程式的存取權嗎?] 切換開關切換為 [是]。

9.  接著，若要為要求存取此應用程式的使用者指派群組，請按一下 [要將指派的使用者新增至哪個群組呢?] 標籤旁的選取控制項，然後選取一個群組。

10. **選擇性：**若要將使用者設定為必須經由商務核准者核准才能存取應用程式，請將 [需要核准才能授予此應用程式的存取權嗎?] 切換開關設定為 [是]。

11. **選擇性：對於只使用密碼單一登入的應用程式，**若要讓那些商務核准者為核准的使用者指定傳送給此應用程式的密碼，請將 [要允許核准者為此應用程式設定使用者的密碼嗎?] 切換開關設定為 [是]。

12. **選擇性：**若要指定商務核准者以核准此應用程式存取權，請按一下 [哪些人員可核准此應用程式的存取權?] 標籤旁的選取控制項以選取最多 10 個商務核准者。

   >[!NOTE]
   >不支援群組。
   >
   >

13. **選擇性：****對於公開角色的應用程式，**若要將已獲得自助存取核准的使用者指派給角色，請按一下 [在此應用程式中應為使用者指派的角色為?] 旁的選取控制項，以選取要為這些使用者指派的角色。

14. 按一下刀鋒視窗頂端的 [儲存] 按鈕以完成此動作。

完成自助應用程式設定之後，使用者可以瀏覽到其[應用程式存取面板](https://myapps.microsoft.com/)並按一下 [+新增] 按鈕以尋找您已啟用自助存取的應用程式。 商務核准者也會在其[應用程式存取面板](https://myapps.microsoft.com/)中看到通知。 您可以啟用電子郵件通知，以便在使用者要求存取應用程式且需要其核准時傳送電子郵件通知。 

這些核准只支援單一核准工作流程，這表示若您指定多個核准者，任何核准者都可以核准應用程式存取。

## <a name="next-steps"></a>後續步驟
[設定 Azure Active Directory 進行自助群組管理](active-directory-accessmanagement-self-service-group-management.md)

