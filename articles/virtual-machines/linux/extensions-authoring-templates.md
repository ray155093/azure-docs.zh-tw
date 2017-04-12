---
title: "使用 Linux VM 擴充功能編寫範本 | Microsoft Docs"
description: "了解如何使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 322f8f0b-6697-4acb-b5f3-b3f58d28358b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8b017306474670bf8dde1440128e16ec35146f24
ms.lasthandoff: 04/03/2017


---
# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure CLI，執行下列命令︰

      Azure VM extension list

此命令會傳回發行者名稱、擴充功能名稱和版本，如下所示：

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

若要查看 Linux 擴充功能的範例組態，請按一下 [Linux 擴充功能範例](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)文件。

請參閱下列項目以取得 VM 擴充功能的完整範本。

[Linux VM 上的自訂指令碼擴充功能。](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

編寫範本之後，您可以使用 Azure CLI 部署它。


