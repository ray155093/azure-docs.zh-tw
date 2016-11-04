---
title: 使用 Azure 自動化管理 Azure API 管理
description: 了解如何使用 Azure 自動化服務來管理 Azure API 管理。
services: api-management, automation
documentationcenter: ''
author: csand-msft
manager: eamono
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: csand

---
# 使用 Azure 自動化管理 Azure API 管理
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure API 管理。

## 什麼是 Azure 自動化？
[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## Azure 自動化如何協助管理 Azure API 管理？
您可以在 Azure 自動化中利用[適用於 API 管理 API 的 Windows PowerShell Cmdlet](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/) 來管理「API 管理」。在 Azure 自動化內，您可以利用 Cmdlet 撰寫 PowerShell 工作流程指令碼，以執行許多 API 管理工作。您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

以下是幾個使用 API 管理搭配自動化的範例︰

* [Azure API 管理 – 使用 PowerShell 進行備份和還原](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## 後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure API 管理之後，請參考下列連結以深入了解。

* 請參閱 Azure 自動化[快速入門教學課程](../automation/automation-first-runbook-graphical.md)。

<!---HONumber=AcomDC_0810_2016------>