---
title: "適用於 Linux 的 OMS Azure 虛擬機器擴充功能 | Microsoft Docs"
description: "使用虛擬機器擴充功能在 Linux 虛擬機器上部署 OMS 代理程式。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>適用於 Linux 的 OMS 虛擬機器擴充功能

## <a name="overview"></a>概觀

Operations Management Suite (OMS) 可提供雲端和內部部署資產的監視、警示和警示補救功能。 Microsoft 已發佈和支援適用於 Linux 的 OMS 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 OMS 代理程式，並且在現有的 OMS 工作區中註冊虛擬機器。 本文件詳述適用於 Linux 的 OMS 虛擬機器擴充功能所支援的平台、組態和部署選項。

如需 Azure 虛擬機器擴充功能的一般資訊，請參閱[虛擬機器擴充功能概觀](./virtual-machines-linux-extensions-features.md)。

如需 Operations Management Suite 的詳細資訊，請參閱 [Operations Management Suite 概觀](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite)。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

可以針對這類 Linux 散發套件執行 OMS 代理程式擴充功能。

| 配送映像 | 版本 |
|---|---|
| CentOS Linux | 5、6 和 7 |
| Oracle Linux | 5、6 和 7 |
| Red Hat Enterprise Linux Server | 5、6 和 7 |
| Debian GNU/Linux | 6、7 和 8 |
| Ubuntu | 12.04 LTS、14.04 LTS、15.04 |
| SUSE Linux Enterprise Server | 11 和 12 |

### <a name="connectivity"></a>連線能力

適用於 Linux 的 OMS 代理程式擴充功能會要求目標虛擬機器連接到網際網路。 

## <a name="extension-configuration"></a>擴充功能組態

適用於 Linux 的 OMS 代理程式虛擬機器擴充功能需要來自目標 OMS 工作區的工作區識別碼和工作區金鑰。 工作區金鑰應被視為敏感性資料，所以會儲存在受保護的組態中。 Azure VM 擴充功能保護的組態資料會經過加密，只會在目標虛擬機器上解密。 公用和私人組態會在部署階段指定，本文件的後續章節有詳細說明。

### <a name="public-configuration"></a>公用組態

公用組態的結構描述︰

- workspaceId：(必要字串) 虛擬機器要上架的 OMS 工作區識別碼。

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>私人組態

公用組態的結構描述︰

- workspaceKey：(必要字串) 工作區的主要/次要共用金鑰。

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。 在 [Azure 快速啟動資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)上可找到包含 OMS 代理程式 VM 擴充功能的範例 Resource Manager 範本。 

使用此按鈕，就可以從這份文件部署這個範例︰

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

用來部署 OMS 代理程式 VM 擴充功能的 JSON 外觀類似以下的 JSON 範例︰

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將 OMS 代理程式 VM 擴充功能部署到現有的虛擬機器。 在部署 OMS 代理程式擴充功能之前，建立 public.json 和 protected.json 檔案。 本文件前面已詳細說明這些檔案的結構描述。

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
azure vm extension get myResourceGroup myVM
```

擴充功能執行輸出會記錄至下列檔案︰

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。



<!--HONumber=Dec16_HO1-->


