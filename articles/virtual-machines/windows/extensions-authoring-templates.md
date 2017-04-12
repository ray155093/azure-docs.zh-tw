---
title: "使用 Windows VM 擴充功能編寫範本 | Microsoft Docs"
description: "了解如何使用 Windows VM 擴充功能編寫 Azure Resource Manager 範本"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 338668df33783d8ef62c6710f4d96ce805296fe5
ms.lasthandoff: 03/31/2017


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>使用 Windows VM 擴充功能編寫 Azure Resource Manager 範本
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure PowerShell，執行下列 Azure PowerShell Cmdlet：

      Get-AzureVMAvailableExtension


此 Cmdlet 會傳回發行者名稱、擴充功能名稱和版本，如下所示：

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性分別對應至上述範本程式碼片段中的 "publisher"、"type" 和 "typeHandlerVersion"。

> [!NOTE]
> 建議一律使用最新的擴充功能版本，以取得最新的功能。
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>識別擴充功能組態參數的結構描述
編寫擴充功能範本的下一個步驟是識別用於提供組態參數的格式。 每個延伸模組都支援自己的參數集。

若要查看 Windows 擴充功能的範例組態，請按一下 [Windows 擴充功能範例](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

請參閱下列項目以取得 VM 擴充功能的完整範本。

[Windows VM 上的自訂指令碼延伸模組](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

編寫範本之後，您可以使用 Azure PowerShell 部署它。


