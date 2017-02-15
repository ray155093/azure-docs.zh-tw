---
title: "針對 Linux VM 擴充功能的失敗進行疑難排解 | Microsoft Docs"
description: "了解如何針對 Linux VM 擴充功能的失敗進行疑難排解"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: top-support-issue,azure-resource-manager
ms.assetid: f05d93f3-42fc-4a09-9798-d92f7929c417
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cdb2f59d227b3c39c4ee21bf3f383f223577bf49


---
# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>針對 Azure Linux VM 擴充功能的失敗進行疑難排解
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>檢視擴充功能狀態
Azure Resource Manager 範本可以從 Azure CLI 執行。 一旦執行範本之後，即可以從 Azure 資源總管或命令列工具檢視延伸模組狀態。

下列是一個範例：

Azure CLI：

      azure vm get-instance-view


以下是範例輸出：

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extenson-failures"></a>針對延伸模組失敗進行疑難排解：
### <a name="re-running-the-extension-on-the-vm"></a>在 VM 上重新執行擴充功能
如果您使用自訂指令碼擴充功能在 VM 上執行指令碼，有時候可能會遇到雖然成功建立了 VM 但指令碼卻失敗的錯誤。 在這樣的情況下，若要從錯誤中復原，建議您移除延伸模組並再次重新執行範本。
請注意：未來將增強這項功能，以移除對解除安裝延伸模組的需求。

#### <a name="remove-the-extension-from-azure-cli"></a>從 Azure CLI 移除擴充功能
      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

其中 "publsher-name" 對應的延伸模組類型來自 "azure vm get-instance-view" 的輸出，而名稱是來自範本的延伸模組資源名稱

一旦移除了延伸模組，範本就可以重新執行並在 VM 上執行指令碼。




<!--HONumber=Nov16_HO3-->


