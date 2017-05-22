---
title: "Azure Cloud Shell (預覽) 快速入門 | Microsoft Docs"
description: "Azure Cloud Shell 的快速入門"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b087ce2b7ee71b867e2123a70a2bfbf0fbea1e3b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="quickstart"></a>快速入門
本文件會詳細說明如何在 [Azure 入口網站](https://ms.portal.azure.com/)中使用 Azure Cloud Shell。

## <a name="start-cloud-shell"></a>啟動 Cloud Shell
1. 從 Azure 入口網站的頂端導覽啟動 **Cloud Shell** <br>
![](media/shell-icon.png)
2. 選取要用來建立儲存體帳戶和 Azure 檔案共用的訂用帳戶 <br>
![](media/storage-prompt.png)
3. 選取 [建立儲存體]

> [!TIP]
> 在每個工作階段會自動驗證您以使用 Azure CLI 2.0。

### <a name="set-your-subscription"></a>設定您的訂用帳戶
1. 列出您可存取的訂用帳戶： <br>
`az account list`
2. 設定您慣用的訂用帳戶： <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> 系統會使用保留在 $Home 目錄中的 `azureProfile.json`，以記住您的訂用帳戶供未來工作階段使用。

### <a name="create-a-resource-group"></a>建立資源群組
在 WestUS 中建立名為 "MyRG" 的新資源群組： <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>建立 Linux VM
在您的新資源群組中建立 Ubuntu VM。 Azure CLI 2.0 將會建立 SSH 金鑰，並使用它們來設定 VM。 <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Azure CLI 2.0 預設會將用來驗證 VM 的公開金鑰和私密金鑰置於 `/User/.ssh/id_rsa` 和 `/User/.ssh/id_rsa.pub`。 您的 .ssh 資料夾會保存在所連接 Azure 檔案共用的 5-GB 映像中。

您在此 VM 上的使用者名稱，將會是用於 Cloud Shell 中的使用者名稱 ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>透過 SSH 連線到您的 Linux VM
1. 在 Azure 入口網站搜尋列中搜尋您的 VM 名稱
2. 按一下 [連線] 然後執行：`ssh username@ipaddress`

![](media/sshcmd-copy.png)

建立 SSH 連線時，應該會看到 Ubuntu 歡迎提示。
![](media/ubuntu-welcome.png)

## <a name="cleaning-up"></a>清除 
刪除您的資源群組以及其中的任何資源： <br>
執行 `az group delete -n MyRG`

## <a name="next-steps"></a>後續步驟
[了解在 Cloud Shell 上保存儲存體](persisting-shell-storage.md) [了解 Azure CLI 2.0] (https://docs.microsoft.com/cli/azure/) [了解 Azure 檔案儲存體] (https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 

