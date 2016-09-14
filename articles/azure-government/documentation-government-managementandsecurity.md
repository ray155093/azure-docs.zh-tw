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


#  Azure Government 管理和安全性

##  金鑰保存庫

下列資訊可識別 Azure 金鑰保存庫的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 使用 Azure 金鑰保存庫金鑰來加密的所有資料都可包含受管制/受控制資料。 | Azure 金鑰保存庫中繼資料不可以包含受到匯出管制的資料。此中繼資料包括建立和維護金鑰保存庫時所輸入的所有組態資料。請勿輸入受管制/受控制資料到下列欄位︰資源群組名稱、金鑰保存庫名稱、訂用帳戶名稱 |

金鑰保存庫已在 Azure Government 中正式推出。和公開版一樣，其中沒有擴充功能，所以只可透過 PowerShell 和 CLI 取得金鑰保存庫。

如需詳細資訊，請參閱 [Azure 金鑰保存庫公開文件](/key-vault-get-started)。

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。</a>

<!---HONumber=AcomDC_0831_2016-->