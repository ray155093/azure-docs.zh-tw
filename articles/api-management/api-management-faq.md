<properties 
	pageTitle="Azure API 管理常見問題集 | Microsoft Azure" 
	description="了解 Azure API 管理常見問題、模式和最佳作法的答案。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
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
-	[如何使用 API 管理達到 API 版本設定？](#how-can-i-achieve-api-versioning-with-api-management)
-	[如何設定多個 API 環境，例如沙箱和生產環境？](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[API 管理中支援 SOAP 嗎？](#is-soap-supported-in-api-management)
-	[API 管理閘道 IP 位址是否固定？ 我可以用在防火牆規則嗎？](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[可以設定具有 ADFS 安全性的 OAUth 2.0 授權伺服器嗎？](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[API 管理在部署到多個地理位置時使用什麼路由方法？](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
-	[可以使用 ARM 範本建立 API 管理服務執行個體嗎？](#can-i-create-an-api-management-service-instance-using-an-arm-template)
-	[是否可以對後端使用自我簽署 SSL 憑證？](#can-i-use-a-self-signed-ssl-certificate-for-a-backend)
-	[為什麼我在嘗試複製 GIT 儲存機制時發生驗證失敗？](#why-am-i-getting-authentication-failure-when-i-try-to-clone-the-git-repository)


### 如何向 API 管理小組詢問問題？

-	您可以將問題張貼在我們的 [API 管理 MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)。
-	您可以傳送電子郵件給我們：`apimgmt@microsoft.com`。
-	您可以傳送[功能要求](https://feedback.azure.com/forums/248703-api-management)。

### 功能「預覽中」是什麼意思？

「預覽中的功能」具備完整功能，但處於預覽狀態，因為我們正積極徵求關於此功能的意見反應。我們可能會進行中斷變更來回應客戶意見反應，因此建議不要根據要在生產環境中執行的功能來決定。如果您對預覽功能有任何意見反應，請使用[如何向 API 管理團隊詢問問題？](#how-can-i-ask-a-question-to-the-api-management-team)中說明的其中一種機制讓我們知道。

### 保護 API 管理閘道和後端服務之間連線安全的支援選項有哪些？

有數個不同的支援選項。

1. 使用 HTTP 基本驗證。如需詳細資訊，請參閱[設定 API 設定](api-management-howto-create-apis.md#configure-api-settings)。
2. 使用 SSL 相互驗證，如[如何使用 Azure API 管理中的用戶端憑證驗證保護後端服務](api-management-howto-mutual-certificates.md)中所述。
3. 在您的後端服務上使用 IP 允許清單。如果您有標準或高階層 API 管理執行個體，閘道的 IP 位址會保持不變，而您可以設定您的允許清單，以允許此 IP 位址。您可以在 Azure 傳統入口網站中的**儀表板**上擷取API 管理執行個體的 IP 位址。
4. 您可以將您的 API 管理執行個體連線至 Azure 虛擬網路 (傳統)。如需詳細資訊，請參閱[如何在 Azure API 管理中設定 VPN 連線](api-management-howto-setup-vpn.md)。

### 我要如何將 API 管理執行個體複製到新的執行個體？

您可以使用數個不同的選項，將 API 管理服務執行個體複製到新的執行個體。

-	使用 API 管理的備份與還原功能。如需詳細資訊，請參閱[如何在 Azure API 管理中使用服務備份和還原實作災害復原](api-management-howto-disaster-recovery-backup-restore.md)。
-	使用 [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) 建立您自己的備份和還原功能，以從您的服務執行個體儲存和還原所需的實體。
-	使用 Git 下載服務組態，並將它重新上傳至新的執行個體。如需詳細資訊，請參閱[如何使用 Git 儲存和設定您的 API 管理服務組態](api-management-configuration-repository-git.md)。

### 我可以透過程式設計方式管理我的 API 管理執行個體嗎？

是的，您可以使用 [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)、[Microsoft Azure API 管理服務管理庫 SDK](http://aka.ms/apimsdk) 和[服務部署](https://msdn.microsoft.com/library/mt619282.aspx)與[服務管理](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell Cmdlet 來加以管理。

### 我要如何新增使用者至系統管理員群組？

透過下列步驟即可實現︰

1. 登入新的 [Azure 入口網站](https://portal.azure.com)
2. 瀏覽至包含所需 API 管理執行個體的資源群組
3. 將所需的使用者新增至「API 管理參與者」角色

完成之後，新增的參與者就可以使用 Azure PowerShell [Cmdlet](https://msdn.microsoft.com/library/mt613507.aspx) 來以系統管理員身分登入︰

1. 使用 `Login-AzureRmAccount` Cmdlet 來登入
2. 使用 `Set-AzureRmContext -SubscriptionID <subscriptionGUID>` 將內容設定為包含服務的訂用帳戶
3. 使用 `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` 取得 SSO 權杖
4. 在瀏覽器中複製貼上 URL，且使用者應該要有系統管理員入口網站存取權


### 為什麼原則編輯器中我想要加入的原則沒有啟用？

如果您想要加入的原則沒有啟用，請確定您是在該原則的正確範圍內。每個原則陳述式都是針對在特定範圍和原則區段中使用所設計。若要檢閱原則的原則區段和範圍，請檢查[原則參考](https://msdn.microsoft.com/library/azure/dn894080.aspx)中該原則的**使用方式**一節。


### 如何使用 API 管理達到 API 版本設定？

-	您可以在 API 管理中設定代表不同版本的不同 API。例如，您可能有 `MyAPI v1` 和 `MyAPI v2` 兩個不同的 API，開發人員可以選擇他們想要使用哪一個版本。
-	您也可以使用例如不包含版本區段的服務 URL 來設定您的 API：`https://my.api`。然後可以在每一個作業的[重寫 URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) 範本上設定版本區段，例如您可以有一個作業的 [URL 範本](api-management-howto-add-operations.md#url-template)為 `/resource`，[重寫 URL](api-management-howto-add-operations.md#rewrite-url-template) 範本為 `/v1/Resource`。如此一來您就可以分別變更每個作業的版本區段值。
-	如果您想要在 API 的服務 URL 中保留「預設」的版本區段，那麼在選取的作業上，您可以設定一個原則，使用[設定後端服務](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService)原則來變更後端要求路徑。

### 如何設定多個 API 環境，例如沙箱和生產環境？

此時，您的選項如下︰

-	您可以在同一個租用戶上裝載不同 API
-	您可以在不同租用戶上裝載相同的 API

### API 管理中支援 SOAP 嗎？

目前，我們對於 Azure API 管理內的 SOAP 提供有限的支援，們目前正在調查此功能。我們很有興趣從您的客戶得到任何範例 WSDL，以及他們所需功能的一些描述，這可以協助我們形成我們的想法。請使用[如何向 API 管理團隊詢問問題？](#how-can-i-ask-a-question-to-the-api-management-team)中參考的連絡人資訊與我們連絡

如果您需要讓它運作，我們有一些社群中有些建議的解決方案，請參閱 [Azure API 管理 - APIM，透過 HTTP 使用 SOAP WCF 服務](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html)。

以此方式實作解決方案需要對原則進行一些手動設定，不支援 WSDL 匯入/匯出，且使用者必須形成使用開發人員入口網站中的測試主控台所做的要求主體。

### API 管理閘道 IP 位址是否固定？ 我可以用在防火牆規則嗎？

在標準和進階層中，API 管理租用戶的公用 IP 位址 (VIP) 在租用戶的存留期內是靜態的，但有下面所列的幾個例外狀況。請注意，針對多重區域部署設定的進階層租用戶，每個區域會被指派一個公用 IP 位址。

在下列情況下，將變更 IP 位址︰

-	服務遭到刪除並重新建立
-	服務訂用帳戶遭到停用 (例如因為未付款) 並恢復
-	加入或移除 VNET 時 (只在進階層中支援 VNET)
-	如果區域被清空然後重新輸入，區域位址會變更 (只在進階層中支援多重區域部署)

IP 位址 (或是多重區域部署情況下的位址) 可以在 Azure 傳統入口網站的租用戶頁面上找到。

### 可以設定具有 ADFS 安全性的 OAUth 2.0 授權伺服器嗎？

如需設定此案例的資訊，請參閱[在 API 管理中使用 ADFS](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)。

### API 管理在部署到多個地理位置時使用什麼路由方法？ 

API 管理使用[效能流量路由方法](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method)。連入流量會傳送至最接近的 API 閘道。如果一個區域離線，連入流量會自動傳送至下一個最接近的閘道。如需路由方法的詳細資訊，請參閱[流量管理員路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

### 可以使用 ARM 範本建立 API 管理服務執行個體嗎？

是，請參閱 [Azure API 管理服務](http://aka.ms/apimtemplate)快速入門範本。

### 是否可以對後端使用自我簽署 SSL 憑證？

是。請遵循下列步驟：

1. 使用管理 API 建立 [Backend](https://msdn.microsoft.com/library/azure/dn935030.aspx) 實體
2. 將 skipCertificateChainValidation 屬性設定為 true
3. 一旦您不再想要允許自我簽署的憑證，您可以刪除 Backend 實體或將 skipCertificateChainValidation 屬性設定為 false

### 為什麼我在嘗試複製 GIT 儲存機制時發生驗證失敗？ 

如果您使用 GIT 認證管理員，或嘗試透過 Visual Studio 複製儲存機制，您可能遇到 Windows 認證對話方塊的已知問題，此對話方塊將密碼長度限制為最多 127 個字元，因此會截斷我們產生的密碼。我們正在努力縮短密碼。目前請使用 GIT Bash 來複製。

<!---HONumber=AcomDC_0810_2016------>