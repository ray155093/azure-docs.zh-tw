<properties
	pageTitle="Azure Government 文件 | Microsoft Azure"
	description="這為 Azure Government 的開發應用程式提供功能和指引的比較"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Azure Government 的資料和儲存體

##  儲存體

下列資訊可識別 Azure 儲存體的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 在 Azure 儲存體產品內輸入、儲存及處理的資料可以包含受到匯出管制的資料。靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。用來管理 Azure 平台元件之憑證的私密金鑰。其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 | Azure 儲存體中繼資料不可以包含受到匯出管制的資料。此中繼資料包括建立和維護儲存體產品時所輸入的所有組態資料。請勿輸入受管制/受控制資料到下列欄位︰資源群組、部署名稱、資源名稱、資源標籤  

如需詳細資訊，請參閱 <a href=https://azure.microsoft.com/zh-TW/documentation/services/storage/>Azure 儲存體公開文件</a>。

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。</a>

##  SQL Database

下列資訊可識別 Azure 儲存體的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 所有在 Microsoft Azure SQL 中儲存及處理的資料都可以包含受 Azure Government 管制的資料。您必須使用資料庫工具來傳輸受 Azure Government 管制的資料。 | Azure SQL 中繼資料不可以包含受到匯出管制的資料。此中繼資料包括建立和維護儲存體產品時所輸入的所有組態資料。請勿輸入受管制/受控制資料到下列欄位︰資料庫名稱、訂用帳戶名稱、資源群組、伺服器名稱、伺服器管理員登入、部署名稱、資源名稱、資源標籤

SQL Database v11 已在 Azure Government 中正式推出。

請參閱 <a href="https://msdn.microsoft.com/zh-TW/library/bb510589.aspx">SQL Database 引擎的 Microsoft 資訊安全中心</a>和 <a href="https://azure.microsoft.com/zh-TW/documentation/services/sql-database/">Azure SQL Database 公開文件</a>，以取得關於中繼資料可見性組態和保護最佳作法的其他指引。

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。</a>

<!---HONumber=AcomDC_0831_2016-->