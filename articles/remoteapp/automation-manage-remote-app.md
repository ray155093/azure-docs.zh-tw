---
title: "使用 Azure 自動化管理 Azure RemoteApp | Microsoft Docs"
description: "了解如何使用 Azure 自動化服務管理 Azure RemoteApp。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 589f01ef-f9c1-4e7b-a040-1b46862d3544
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 59ac11f153c0bd74a1106400dbbf5790968b657c
ms.lasthandoff: 03/31/2017


---
# <a name="managing-azure-remoteapp-using-azure-automation"></a>使用 Azure 自動化管理 Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure RemoteApp 的管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](../automation/automation-intro.md) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 使用 Azure 自動化，可以自動執行手動、經常重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、 效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。 在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Azure 自動化為何有助於管理 Azure RemoteApp？
Azure RemoteApp 可透過 [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)工具中提供的 PowerShell Cmdlet，在 Azure 自動化中受到管理。 Azure 自動化的這些 RemoteApp PowerShell Cmdlet 都是內建的，以便您在服務內執行所有 RemoteApp 管理工作。 您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure RemoteApp 之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化 [入門指南](../automation/automation-first-runbook-graphical.md)


