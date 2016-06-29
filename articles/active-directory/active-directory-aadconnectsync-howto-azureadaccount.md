<properties
	pageTitle="Azure AD Connect 同步處理︰如何管理 Azure AD 服務帳戶 | Microsoft Azure"
	description="本主題將說明如何還原 Azure AD 服務帳戶。"
	services="active-directory"
    keywords="如何重設 Azure AD Connect 同步處理連接器服務帳戶的密碼"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect 同步處理︰如何管理 Azure AD 服務帳戶
Azure AD 連接器所使用的服務帳戶應該是免費的服務。但是如果您需要重設其認證，則這個主題適合您。例如，如果全域管理員不小心使用 PowerShell 重設了服務帳戶上的密碼，就會發生此情況。

## 重設認證
如果 Azure AD 連接器上定義的服務帳戶因驗證問題而無法連線至 Azure AD，就可以重設密碼。

1. 登入 Azure AD Connect 同步處理伺服器，並啟動 PowerShell。
2. 執行 `Add-ADSyncAADServiceAccount`。![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. 提供 Azure AD 全域管理員認證。

這個 Cmdlet 將重設服務帳戶的密碼，並在 Azure AD 和同步處理引擎中加以更新。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0615_2016-->