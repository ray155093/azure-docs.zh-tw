<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="本主題說明何謂 Azure AD Privileged Identity Management，以及如何使用 PIM 改善雲端安全性。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="kgremban"/>

# Azure AD 特殊權限身分識別管理

Azure Active Directory (AD) Privileged Identity Management 可讓您管理、控制和監視特殊權限身分識別，以及存取 Azure AD 和 Office 365 或 Microsoft Intune 等其他 Microsoft Online 服務的資源。

使用者有時候需要在 Azure 或 Office 365 資源或其他 SaaS 應用程式中執行特殊權限的作業。這通常表示組織必須授與他們永久的 Azure AD 特殊權限存取權。這會提高雲端資源的安全性風險，因為組織無法滴水不漏地監視這些使用者利用其管理員權限的所作所為。此外，如果擁有特殊權限存取權的使用者帳戶遭到入侵，這個缺口可能會影響其整體的雲端安全性。Azure AD 特殊權限身分識別管理有助於解決此風險。

Azure AD Privileged Identity Management 可讓您：

- 查看哪些使用者是 Azure AD 管理員
- 視需要啟用 Office 365 和 Intune 等 Microsoft Online Services 的 "just-in-time" 系統管理存取權限
- 取得有關系統管理員存取記錄與系統管理員指派變更的報告
- 取得有關特殊權限角色存取的警示

Azure AD Privileged Identity Management 可以管理內建的 Azure AD 組織角色，包括：

- 全域管理員
- 計費管理員
- 服務管理員  
- 使用者管理員
- 密碼管理員

## 即時管理員存取權

在過去，您可以透過舊版的 Azure 管理入口網站或 Windows PowerShell 將使用者指派給管理員角色。因此，該使用者會成為該角色的**永久管理員**，其指派角色一律處於作用狀態。Azure AD Privileged Identity Management 引入了**暫時管理員**角色的概念，這是需要完成指派角色啟用程序的使用者。啟用程序會在指定的時段內，將 Azure AD 中角色的使用者指派從非作用中變更為作用中，如 8 小時。

## 啟用目錄的 Privileged Identity Management

存取 [Azure 入口網站](https://portal.azure.com/)，即可開始使用 Azure AD Privileged Identity Management。舊版的傳統入口網站不顯示 Azure AD Privileged Identity Management。

>[AZURE.NOTE] 您必須是具有組織帳戶、非 Microsoft 帳戶的全域系統管理員，才能啟用目錄的 Azure AD Privileged Identity Management。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 如果貴組織有多個目錄，請按一下 Azure 入口網站右上角的使用者名稱，選取您要使用 Azure AD Privileged Identity Management 的目錄。
3. 按一下左側導覽列中的 [新增] 圖示。
4. 選取 [安全性 + 識別]。
5. 選取 [Azure AD Privileged Identity Management]。
6. 選取 [釘選到儀表板] 選項，然後按一下 [建立] 按鈕。[特殊權限身分識別管理] 儀表板隨即開啟。

如果您是在目錄中使用 Azure AD Privileged Identity Management 的第一個人，則[安全性精靈](active-directory-privileged-identity-management-security-wizard.md)會引導您完成初始指派體驗。之後，您就會自動成為目錄的第一個**安全性系統管理員**。

只有安全性系統管理員可以使用 PIM 應用程式管理其他系統管理員的存取權。請參閱 [How to give access to manage Azure AD Privileged Identity Management (如何提供管理 PIM 的存取權)](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。

## 特殊權限身分識別管理儀表板

Azure AD 特殊權限身分識別管理員有一個儀表板可提供重要資訊，例如：

- 指派給每個特殊權限角色的使用者數目  
- 暫時和永久管理員的數目
- 每位系統管理員的存取記錄

![PIM 儀表板 - 螢幕擷取畫面][2]

## 特殊權限角色管理

利用 Azure AD 特殊權限身分識別管理，您可以藉由新增或移除每個角色的永久或暫存管理員來管理管理員。

![PIM 新增/移除系統管理員 - 螢幕擷取畫面][3]

## 設定角色啟用設定

您可以使用角色啟用設定來設定暫時的角色啟用屬性，包括：

- 角色啟用期間的持續時間
- 角色啟用通知
- 使用者在角色啟用程序期間所需提供的資訊  

![PIM 設定 - 系統管理員啟動 - 螢幕擷取畫面][4]

## 角色啟用  

若要啟用角色，暫時管理員必須要求對角色進行有時效性的「啟用」。使用 Azure AD 特殊權限身分識別管理中的 [**啟用我的角色**] 選項，即可要求啟用。

想要啟用角色的管理員必須在 Azure 入口網站中初始化 Azure AD Privileged Identity Management。

任何類型的管理員均可使用 Azure AD 特殊權限身分識別管理來啟用自己的角色。

角色啟用是有時效性的。在 [角色啟用] 設定中，您可以設定啟用的長度，以及管理員必須提供才能啟用角色的必要資訊。

![PIM 系統管理員要求角色啟用 - 螢幕擷取畫面][5]

## 角色啟用歷程記錄

使用 Azure AD Privileged Identity Management，也可以追蹤特殊權限角色指派和角色啟用歷程記錄的變更。使用稽核記錄選項即可進行：

![PIM 啟動歷程記錄 - 螢幕擷取畫面][6]

## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0420_2016-->