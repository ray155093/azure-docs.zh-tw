<properties 
	pageTitle="Azure API 管理常見問題集 | Microsoft Azure" 
	description="了解 Azure API 管理常見問題、模式和最佳作法的答案。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

# Azure API 管理常見問題集

了解 Azure API 管理常見問題、模式和最佳作法的答案。

## 常見問題集

-	[如何向 API 管理小組詢問問題？](#how-can-i-ask-a-question-to-the-api-management-team)
-	[功能「預覽中」是什麼意思？](#what-does-it-mean-if-a-feature-is-in-preview)
-	[保護 API 管理閘道和後端服務之間連線安全的支援選項有哪些？](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[我要如何將 API 管理執行個體複製到新的執行個體？](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[我可以透過程式設計方式管理我的 API 管理執行個體嗎？](#can-i-manage-my-api-management-instance-programmatically)
-	[我要如何新增使用者至系統管理員群組？](#how-can-i-add-a-user-to-the-administrators-group)
-	[為什麼原則編輯器中的「我想要加入」原則沒有啟用？](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)

### 如何向 API 管理小組詢問問題？

-	您可以將問題張貼在我們的 [API 管理 MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)。
-	您也可以傳送電子郵件給我們：`apimgmt@microsoft.com`。

### 功能「預覽中」是什麼意思？

「預覽中的功能」具備完整功能，但處於預覽狀態，因為我們正積極徵求關於此功能的意見反應。我們可能會進行中斷變更來回應客戶意見反應，因此建議不要根據要在生產環境中執行的功能來決定。如果您對預覽功能有任何意見反應，請使用[如何向 API 管理小組詢問問題？](#how-can-i-ask-a-question-to-the-api-management-team)中說明的其中一種機制讓我們知道。

### 保護 API 管理閘道和後端服務之間連線安全的支援選項有哪些？

有數個不同的支援選項。

1. 使用 HTTP 基本驗證。如需詳細資訊，請參閱[設定 API 設定](api-management-howto-create-apis.md#configure-api-settings)。
2. 使用 SSL 相互驗證，如[如何使用 Azure API 管理中的用戶端憑證驗證保護後端服務](api-management-howto-mutual-certificates.md)中所述。
3. 在您的後端服務上使用 IP 允許清單。如果您有標準或高階層 API 管理執行個體，閘道的 IP 位址會保持不變，而您可以設定您的允許清單，以允許此 IP 位址。您可以在 Azure 傳統入口網站中的「儀表板」上擷取API 管理執行個體的 IP 位址。
4. 您可以將您的 API 管理執行個體連線至 Azure 虛擬網路 (傳統)。如需詳細資訊，請參閱[如何在 Azure API 管理中設定 VPN 連線](api-management-howto-setup-vpn.md)。

### 我要如何將 API 管理執行個體複製到新的執行個體？

您可以使用數個不同的選項，將 API 管理服務執行個體複製到新的執行個體。

-	使用 API 管理的備份與還原功能。如需詳細資訊，請參閱[如何在 Azure API 管理中使用服務備份和還原實作災害復原](api-management-howto-disaster-recovery-backup-restore.md)。
-	使用 [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) 建立您自己的備份和還原功能，以從您的服務執行個體儲存和還原所需的實體。
-	使用 Git 下載服務組態，並將它重新上傳至新的執行個體。如需詳細資訊，請參閱[如何使用 Git 儲存和設定您的 API 管理服務組態](api-management-configuration-repository-git.md)。

### 我可以透過程式設計方式管理我的 API 管理執行個體嗎？

是的，您可以使用 [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) 以及[服務部署](https://msdn.microsoft.com/library/mt619282.aspx)和[服務管理](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell Cmdlet 來加以管理。

### 我要如何新增使用者至系統管理員群組？

此時，系統管理員限制為透過 Azure 傳統入口網站，以包含 API 管理執行個體之 Azure 訂用帳戶的系統管理員或共同管理員身分登入的使用者。發行者入口網站中建立的使用者無法指定為系統管理員，或加入系統管理員群組。


### 為什麼原則編輯器中我想要加入的原則沒有啟用？

如果您想要加入的原則沒有啟用，請確定您是在該原則的正確範圍內。每個原則陳述都是針對在特定範圍和原則區段使用所設計。若要檢視原則區段和原則的範圍，請檢查[原則參考](https://msdn.microsoft.com/library/azure/dn894080.aspx)中該原則的「使用方式」區段。

<!---HONumber=AcomDC_0413_2016-->