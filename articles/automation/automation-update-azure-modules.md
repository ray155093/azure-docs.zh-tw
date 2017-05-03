---
title: "更新 Azure 自動化中的 Azure 模組 | Microsoft Docs"
description: "本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。"
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.lasthandoff: 04/21/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

每個自動化帳戶依預設都會提供最通用的 Azure PowerShell 模組。  Azure 小組會定期更新 Azure 模組，因此在自動化帳戶中，我們會提供方法供您在入口網站有新的版本可供使用時，更新帳戶中的模組。  

## <a name="updating-azure-modules"></a>更新 Azure 模組

1. 自動化帳戶的 [模組] 刀鋒視窗中有一個稱為**更新 Azure 模組**的選項。  它一律為啟用狀態。<br><br> ![[模組] 刀鋒視窗中的 [更新 Azure 模組] 選項](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. 按一下 [更新 Azure 模組]，您便會看到確認通知，詢問您是否要繼續。<br><br> ![更新 Azure 模組通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 按一下 [是]便會開始模組更新程序。  更新程序大約需要 15-20 分鐘來更新下列模組︰

  * Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    如果模組已是最新狀態，此程序在幾秒鐘內便會完成。  更新程序完成時，您會收到通知。<br><br> ![更新 Azure 模組更新狀態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

每當您建立排程時，該排程上執行的任何後續作業，會使用排程建立時在自動化帳戶中的模組。  若要開始搭配使用更新的模組與排程的 Runbook，您必須將排程取消連結，然後再將它與該 Runbook 重新連結。   

如果您在 Runbook 中使用來自這些 Azure PowerShell 模組的 Cmdlet 來管理 Azure 資源，則您會想要每隔大約一個月執行一次此更新程序，以確保您擁有最新模組。

## <a name="next-steps"></a>後續步驟

若要深入了解整合模組，以及如何建立自訂模組以進一步整合自動化與其他系統、服務或解決方案，請參閱[整合模組](automation-integration-modules.md)。
