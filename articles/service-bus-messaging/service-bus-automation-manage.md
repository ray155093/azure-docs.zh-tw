---
title: "使用 Azure 自動化管理 Azure 服務匯流排 | Microsoft Docs"
description: "了解如何使用 Azure 自動化服務管理 Azure 服務匯流排。"
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1912275b05948525486e4e30e2065bd7f63630cb


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>使用 Azure 自動化管理 Azure 服務匯流排
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure 服務匯流排的管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](../automation/automation-intro.md) 是一項 Azure 服務，可經由程序自動化和所要的狀態組態簡化雲端管理。 使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。 在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Azure 自動化如何有助於管理 Azure 服務匯流排？
您可以在 Azure 自動化中使用 [服務匯流排 REST API](https://msdn.microsoft.com/library/azure/mt639375.aspx)來管理服務匯流排。 在 Azure 自動化內，您可以利用 REST API 執行 PowerShell 指令碼，以執行許多服務匯流排工作。 您也可以將 Azure 自動化中的這些 REST API 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

以下是使用 PowerShell 來管理 Azure 服務匯流排的一些範例︰

* [自訂 PowerShell Cmdlet 來管理 Azure 服務匯流排佇列](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [如何使用 PowerShell 指令碼來建立服務匯流排佇列、主題及訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [使用 PowerShell 建立 Azure 服務匯流排命名空間](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)下載 PowerShell 模組，用來搭配自動化 Runbook 中的 Azure 服務匯流排運作。

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure 服務匯流排之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化 [入門指南](../automation/automation-first-runbook-graphical.md)
* 請參閱如何 [使用 PowerShell 管理服務匯流排](service-bus-powershell-how-to-provision.md)




<!--HONumber=Nov16_HO3-->


