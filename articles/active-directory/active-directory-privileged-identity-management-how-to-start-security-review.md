<properties
   pageTitle="如何開始安全性檢閱 | Microsoft Azure"
   description="了解如何使用 Azure Privileged Identity Management 應用程式為特殊權限身分識別建立安全性檢閱。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何開始安全性檢閱

當使用者擁有不再需要的特殊存取權時，角色指派就會變成「過時」。為了降低與這些過時的角色指派相關聯的風險，特殊權限角色管理員應該定期檢閱使用者獲授與的角色。本文件涵蓋在 Azure AD Privileged Identity Management (PIM) 中開始安全性檢閱的步驟。

## 開始安全性檢閱
> [AZURE.NOTE] 如果您尚未在 Azure 入口網站的儀表板中加入 PIM 應用程式，請參閱[開始使用 Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) 中的步驟。

在 PIM 應用程式的主頁面，有三種方式可以開始安全性檢閱︰

- [安全性檢閱] > [檢閱] > [檢閱] 按鈕
- [角色] > [檢閱] 按鈕
- 從角色清單中選取要檢閱的角色 > [檢閱] 按鈕

當您按一下 [檢閱] 按鈕時，隨即會出現 [開始檢閱角色] 和 [選取要檢閱的角色] 刀鋒視窗。為您選取 [選取要檢閱的角色] 項目。

### 選取要檢閱的角色

1. 在 [選取要檢閱的角色] 刀鋒視窗中，從角色清單中選取角色。您一次只能選擇一個角色。[選取要檢閱的角色] 刀鋒視窗將會以 [選取檢閱者] 刀鋒視窗來取代。您在選取檢閱者時有下列兩個選項：
  - [Me] \(我) - 如果您想要預覽安全性檢閱的運作方式，但不牽涉到其他系統管理員，請使用這個選項。
  - [Self review by role members] \(由角色成員自行檢閱) - 使用這個選項讓使用者檢閱自己的角色指派。
2. 選取這其中一個，開始著手檢閱詳細資料。隨即會出現 [變更預設值] 刀鋒視窗。

### 透過 [我] 來檢閱

如果您選取了 [我] 選項做為檢閱者，則請繼續進行安全性檢閱。如需更多完成檢閱的詳細資訊，請參閱 [Azure 特殊權限身分識別管理：如何執行安全性檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### 由角色成員自行檢閱

如果您選擇讓使用者檢閱他們自己的角色指派，請依下列步驟設定檢閱並傳送通知。

1. 在 [名稱] 欄位中輸入檢閱名稱，來為檢閱命名。給檢閱一個描述該檢閱且可輕鬆追蹤該檢閱的獨特名稱。
2. 在 [開始日期] 欄位中，輸入檢閱的開始日期。
3. 在 [結束日期] 欄位中，輸入檢閱的結束日期。在設定檢閱的結束日期時，您應該考量下列一些事項：
  - 要檢閱多少位使用者？
  - 使用者在 Azure 入口網站加入 PIM 應用程式並完成檢閱的最快速度？
4. 按一下 [變更預設值] 刀鋒視窗中的 [確定] 按鈕。它將會關閉。
5. 按一下 [啟動角色的檢閱] 刀鋒視窗中的 [確定] 按鈕。它將會關閉。Azure 入口網站的主功能表中隨即出現一則通知。按一下 [重新整理] 按鈕來重新整理儀表板，而安全性檢閱將會出現在 [安全性檢閱] 區段中。
6. 通知角色中的每個人，他們將需要加入 PIM 應用程式，然後[檢閱他們自己的系統管理存取權](active-directory-privileged-identity-management-how-to-perform-security-review.md)。  

## 管理安全性檢閱

當檢閱者在 Azure AD PIM 儀表板的 [安全性檢閱] 區段中完成檢視時，您就可以追蹤進度。目錄中的存取權限不會變更，直到[完成檢閱](active-directory-privileged-identity-management-how-to-complete-review.md)。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## PIM 目錄
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->