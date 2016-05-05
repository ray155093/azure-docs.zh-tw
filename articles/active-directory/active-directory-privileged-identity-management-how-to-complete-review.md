<properties
   pageTitle="如何完成安全性檢閱 | Microsoft Azure"
   description="了解如何使用 Azure Privileged Identity Management 應用程式完成檢閱。"
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何完成安全性檢閱


只要[開始安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)，安全性系統管理員就可以檢閱特殊權限存取。Azure AD Privileged Identity Management (PIM) 會自動傳送電子郵件，提示使用者檢閱其存取權。如果使用者未收到電子郵件，您可以將 [Azure AD Privileged Identity Management：如何執行安全性檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)中的指示傳送給他們。

安全性檢閱時間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟管理檢閱並查看結果。

## 管理安全性檢閱

1. 請移至 [Azure 入口網站](https://portal.azure.com/)，在儀表板上選取 [Azure AD Privileged Identity Management] 應用程式。
2. 選取儀表板的 [安全性檢閱] 區段。隨即會出現 [安全性檢閱] 刀鋒視窗。
3. 選取您想要管理的安全性檢閱。隨即會出現安全性檢閱的詳細資料刀鋒視窗。

安全性檢閱的詳細資料刀鋒視窗上，有三個管理檢視的選項。您可以完成檢閱、匯出詳細資料，或刪除檢閱。

### 完成或停止檢閱

如果使用者拒絕其存取，您可以完成檢閱，移除目錄中這些使用者的角色指派。[停止檢閱] 按鈕會封存檢閱。

### 匯出檢閱

如果想要手動套用安全性檢閱的結果，您可以匯出檢閱。[匯出] 按鈕會開始下載 CSV 檔案。您可以在 Excel 或開啟 CSV 檔案的其他程式中管理結果。

### 刪除檢視

> [AZURE.IMPORTANT] 刪除執行前，您不會收到警告，因此請務必確定您想要刪除該檢閱。

如果對檢閱不再有任何興趣，請刪除它。[刪除] 按鈕會從 PIM 應用程式中移除檢閱。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->