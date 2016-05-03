<properties
   pageTitle="如何管理角色啟用設定 | Microsoft Azure"
   description="了解如何利用 Azure AD Privileged Identity Management 擴充功能，變更特殊權限身分識別的預設設定。"
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

# 如何管理 Azure AD Privileged Identity Management 的角色啟用設定

安全性系統管理員可以自訂其組織中的 Azure AD Privileged Identity Management (PIM)，包括變更啟用暫時性角色指派之使用者的體驗。

## 管理角色啟用設定

1. 移至 [Azure 入口網站](https://portal.azure.com)，在儀表板上選取 [Azure AD Privileged Identity Management] 應用程式。
2. 從角色資料表中選取您想要管理的角色。
3. 按一下 [設定]。
4. 調整滑桿或在文字欄位中輸入時數，以設定預設啟用持續時間 (小時)。最大值為 72 小時。
5. 請按一下 [啟用] 或 [停用] 以傳送或不傳送啟用通知給系統管理員。(啟用通知可能有助於偵測未經授權的系統管理員活動)。
6. 按一下 [啟用] 允許系統管理員在啟用要求中輸入票證資訊。(稍後在稽核角色存取權時，這項資訊很有幫助)。
7. 按一下 [啟用] 或 [停用]，即可設定啟用要求需要或不需要 Multi-Factor Authentication。
8. 按一下 [儲存]。

您無法針對 Azure AD 和 Office365 中具有極高權限的角色停用 MFA，包括︰
- 全域管理員  
- 使用者帳戶管理員  
- 目錄寫入器  
- 合作夥伴第 1 層支援  
- 合作夥伴第 2 層支援  
- 計費管理員  
- 安全性系統管理員  
- Exchange 系統管理員  
- 信箱管理員  
- 商務用 Skype 的管理員  
- SharePoint 管理員  
- 規範管理員  

如需關於搭配 PIM 使用 MFA 的詳細資訊，請參閱[如何要求 MFA](active-directory-privileged-identity-management-how-to-require-mfa.md)。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->