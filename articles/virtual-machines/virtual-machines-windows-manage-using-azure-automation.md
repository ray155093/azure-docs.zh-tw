---
title: "使用 Azure 自動化管理 VM | Microsoft Docs"
description: "了解如何使用 Azure 自動化服務大規模地管理 Azure 虛擬機器。"
services: virtual-machines-windows, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: ce49f83a-f409-42ee-af74-a8ea2caa9ae8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d9c161b58419bb85c7ec473d853db4cbc40dd1


---
# <a name="managing-azure-virtual-machines-using-azure-automation"></a>使用 Azure 自動化管理 Azure 虛擬機器
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化您的 Azure 虛擬機器管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。

Azure 自動化提供非常可靠且高度可用的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。 在 Azure 自動化中，可利用手動方式、透過協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

您可以透過 Azure 自動化來自動執行雲端管理工作，以減少營運負擔並釋出 IT 和 DevOps 人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Azure 自動化為何有助於管理 Azure 虛擬機器？
您可以在 Azure 自動化中使用 [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)來管理虛擬機器。 Azure 自動化包含 Azure PowerShell Cmdlet，因此您可以在此服務內執行所有的虛擬機器管理工作。 您也可以將 Azure 自動化中的 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以便在 Azure 服務和協力廠商系統上自動執行複雜的工作。

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure 虛擬機器之後，深入了解：

* [Azure 自動化概觀](../automation/automation-intro.md)
* [我的第一個 Runbook](../automation/automation-first-runbook-graphical.md)
* [Azure 自動化的學習地圖](https://azure.microsoft.com/documentation/learning-paths/automation/)




<!--HONumber=Nov16_HO3-->


