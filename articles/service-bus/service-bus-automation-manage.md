<properties
	pageTitle="使用 Azure 自動化管理 Azure 服務匯流排 | Microsoft Azure"
	description="了解如何使用 Azure 自動化服務管理 Azure 服務匯流排。"
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="csand"/>

# 使用 Azure 自動化管理 Azure 服務匯流排

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure 服務匯流排的管理。

## 什麼是 Azure 自動化？

[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化和所要的狀態組態簡化雲端管理。使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## Azure 自動化如何有助於管理 Azure 服務匯流排？

您可以在 Azure 自動化中使用[服務匯流排 REST API](https://msdn.microsoft.com/library/azure/hh780717.aspx) 來管理服務匯流排。在 Azure 自動化內，您可以利用 REST API 執行 PowerShell 指令碼，以執行許多服務匯流排工作。您也可以將 Azure 自動化中的這些 REST API 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

以下是使用 PowerShell 來管理 Azure 服務匯流排的一些範例︰
* [自訂 PowerShell Cmdlet 來管理 Azure 服務匯流排佇列](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues/)
* [如何使用 PowerShell 指令碼來建立服務匯流排佇列、主題及訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [使用 PowerShell 建立 Azure 服務匯流排命名空間](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)
* [可供新增組態節點以建立 Azure 服務匯流排的 DSCResource 模組](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)

## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure 服務匯流排之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化[快速入門教學課程](https://azure.microsoft.com/documentation/learning-paths/automation/)。
* 請參閱如何[使用 PowerShell 管理服務匯流排](service-bus-powershell-how-to-provision.md)

<!---HONumber=AcomDC_0518_2016-->