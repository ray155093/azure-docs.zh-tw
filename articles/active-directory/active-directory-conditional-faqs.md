<properties
	pageTitle="條件式存取常見問題集 | Microsoft Azure"
	description="關於條件式存取的常見問題集"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="femila"/>

# 條件式存取常見問題集

## 哪些應用程式會使用條件式存取原則？
請參閱主題[條件式存取 - 支援哪些應用程式](active-directory-conditional-access-supported-apps.md)。

## 是否會針對 B2B 共同作業與來賓使用者強制套用條件式存取原則？
系統會針對 B2B 共同作業使用者強制套用原則。不過，在某些情況下，使用者可能無法滿足原則需求，例如，如果他們的組織不支援多重要素驗證。目前不會針對 SharePoint 來賓使用者強制套用原則。來賓關聯性是在 SharePoint 內進行維護，因此驗證伺服器上的來賓使用者帳戶不會受限於存取原則。您可以在 SharePoint 上管理來賓存取。

## SharePoint Online 原則也適用於商務用 OneDrive 嗎？
是。
 
## 為什麼我無法在用戶端應用程式 (例如 Word 或 Outlook) 上設定原則？
條件式存取原則會設定存取服務的需求，並在該服務發生驗證時強制套用。因此原則不是直接設定於用戶端應用程式上，而是在它呼叫服務時加以套用。例如，在 SharePoint 上設定的原則將會套用至呼叫 SharePoint 的用戶端，而 Exchange 上設定的原則將會套用至 Outlook。

<!---HONumber=AcomDC_0615_2016-->