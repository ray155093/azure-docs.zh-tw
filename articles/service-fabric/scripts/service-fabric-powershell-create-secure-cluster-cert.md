---
title: "Azure PowerShell 指令碼範例 - 建立 Service Fabric 叢集 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 建立 Service Fabric 叢集。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 7322570c90b3c83fc79d428a7ac0b57ecb2e4b75
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集

這個範例指令碼會建立 Service Fabric 叢集，這是使用 X.509 憑證保護的五節點叢集。  此命令會建立自我簽署的憑證，並將它上傳到新的金鑰保存庫。 憑證也會複製到本機目錄。  設定 *-OS* 參數選擇在叢集節點執行的 Windows 或 Linux 版本。  視需要自訂參數。

您可以視需要使用 [Azure PowerShell 指南 (英文)](/powershell/azure/overview) 中的指示來安裝 Azure PowerShell，然後執行 `Login-AzureRmAccount` 來建立與 Azure 的連線。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "建立 Service Fabric 叢集")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、叢集和所有相關資源。

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 建立新的 Service Fabric 叢集。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。

