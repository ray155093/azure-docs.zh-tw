<properties 
	pageTitle="開始使用 Microsoft Azure Multi-Factor Auth Provider" 
	description="了解如何建立 Azure Multi-Factor Auth Provider。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/29/2016" 
	ms.author="billmath"/>



# 開始使用 Azure Multi-Factor Auth Provider
依預設，擁有 Azure Active Directory 和 Office 365 使用者的全域管理員可以使用 Multi-Factor Authentication。不過，如果您想要充分利用[進階功能](multi-factor-authentication-whats-next.md)，則必須購買完整版的 Azure MFA。

> [AZURE.NOTE]  Azure Multi-Factor Auth Provider 可用來充分利用完整版 Azure MFA 所提供的功能。它的適用對象是**未透過 Azure MFA、Azure AD Premium 或 EMS 取得授權**的使用者。Azure MFA、Azure AD Premium 和 EMS 預設會包含完整版 Azure MFA。如果您有授權，則不需要 Azure Multi-Factor Auth Provider。

如果您想要下載 SDK，則需要 Azure Multi-Factor Auth Provider。

> [AZURE.IMPORTANT]  如果您想要下載 SDK，您必須建立 Azure Multi-Factor Auth Provider，即使您有 Azure MFA、AAD Premium 或 EMS 授權。如果您為此目的建立 Azure Multi-Factor Auth Provider 並已擁有授權，則必須建立採用 [每個啟用的使用者] 模型的 Provider，並將 Provider 連結至包含 Azure MFA、Azure AD Premium 或 EMS 授權的目錄。這可確保您不須付費，除非使用 SDK 的唯一使用者超過您所擁有的授權數目。
 
使用下列步驟，建立 Azure Multi-Factor Auth Provider。

## 建立 Multi-Factor Auth Provider
--------------------------------------------------------------------------------

1. 以系統管理員身分登入 **Azure 傳統入口網站**。
2. 選取左邊的 [Active Directory]。
3. 在 [Active Directory] 頁面頂端，選取 [Multi-Factor Authentication Provider]。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 在底部按一下 [新增]。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. 在 [應用程式服務] 之下選取 [Multi-Factor Auth Provider]。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. 選取 [快速建立]。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. 填寫下列欄位並選取 [建立]。
	1. **名稱** – Multi-Factor Auth Provider 的名稱。
	2. **使用模式** – Multi-Factor Authentication Provider 的使用模式。
		- 每次驗證 – 購買依每次驗證付費的模式。通常用於在取用者導向應用程式中使用 Azure Multi-factor Authentication 的案例。
		- 每個啟用的使用者 – 購買依每個啟用使用者付費的模式。通常用於員工存取 Office 365 之類的應用程式。
	2. **目錄** – 與 Multi-Factor Authentication Provider 相關聯的 Azure Active Directory 租用戶。請注意以下事項：
		- 您不需要 Azure AD 目錄即可建立 Multi-Factor Auth Provider。如果您只打算使用 Azure Multi-Factor Authentication Server 或 SDK，只需將方塊保留空白。
		- Multi-Factor Auth Provider 必須與 Azure AD 目錄產生關聯，才能利用進階功能。
		- 如果您要讓內部部署 Active Directory 環境與 Azure AD 目錄同步處理，Azure AD Connect、AAD Sync 或 DirSync 或 AAD Sync 只是一項需求。如果您只使用未同步的 Azure AD 目錄，則不需要此項。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. 一旦按一下建立，系統便會建立 Multi-Factor Authentication Provider，而且您應該會看到一則指出「已成功建立 Multi-Factor Authentication Provider」的訊息。按一下 [確定]。![建立 MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0629_2016-->