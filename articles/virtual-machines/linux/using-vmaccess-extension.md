---
title: "使用 VMAccess 擴充功能和 Azure CLI 2.0 重設存取 | Microsoft Docs"
description: "如何使用 VMAccess 擴充功能和 Azure CLI 2.0 在 Linux VM 上管理使用者及重設存取"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e23e2b2d5f30a2ec30564287c96ffc9c671c0dbf
ms.lasthandoff: 04/03/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>使用 VMAccess 擴充功能搭配 Azure CLI 2.0 在 Linux VM 上管理使用者、SSH 及檢查或修復磁碟
Linux VM 的磁碟顯示錯誤。 您不知怎麼重設 Linux VM的根密碼，或不小心刪除了 SSH 私密金鑰。 如果是過去資料中心的時代發生此狀況，您必須親赴現場，然後再開啟 KVM 才能存取伺服器主控台。 請將 Azure VMAccess 擴充功能想成 KVM 交換器，在此可以存取主控台重設 Linux 存取或執行磁碟等級維護。

本文將說明如何使用 Azure VMAcesss 延伸模組來檢查或修復磁碟、重設使用者存取、管理使用者帳戶或重設 Linux 上的 SSHD 組態。 您也可以使用 [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。


## <a name="ways-to-use-the-vmaccess-extension"></a>使用 VMAccess 擴充功能的方式
您可以透過兩種方式在 Linux VM 上使用「VMAccess 擴充功能」：

* 使用 Azure CLI 2.0 和必要的參數。
* [使用 VMAccess 擴充功能會處理然後作為行動依據的原始 JSON 檔案](#use-json-files-and-the-vmaccess-extension)。

下列範例會使用 [az vm access](/cli/azure/vm/access) 搭配適當的參數。 若要執行這些步驟，您需要安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

## <a name="reset-ssh-key"></a>重設 SSH 金鑰
下列範例會重設名為 `myVM` 的 VM 上使用者 `azureuser` 的 SSH 金鑰：

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>重設密碼
下列範例會重設名為 `myVM` 的 VM 上使用者 `azureuser` 的密碼：

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>重設 SSHD
下列範例會重設名為 `myVM` 之 VM 上的 SSHD 組態：

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>建立使用者
下列範例會在名為 `myVM` 的 VM 上，建立一個使用 SSH 金鑰進行驗證、名為 `myNewUser` 的使用者：

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>刪除使用者
下列範例會刪除名為 `myVM` 之 VM 上名為 `myNewUser` 的使用者：

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>使用 JSON 檔案和 VMAccess 擴充功能
下列範例會使用原始 JSON 檔案。 請使用 [az vm extension set](/cli/azure/vm/extension#set) 來接著呼叫您的 JSON 檔案。 您也可以從 Azure 範本呼叫這些 JSON 檔案。 

### <a name="reset-user-access"></a>重設使用者存取
如果您已無法存取 Linux VM 上的 root，您可以啟動 VMAccess 指令碼來重設使用者密碼。

若要重設使用者的 SSH 金鑰，請建立名為 `reset_ssh_key.json` 的檔案並新增下列內容：

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

若要重設使用者密碼，請建立名為 `reset_user_password.json` 的檔案並新增下列內容：

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="reset-ssh"></a>重設 SSH
如果您變更了 Linux VM SSHD 組態，並在驗證變更之前關閉 SSH 連線，您可能無法回到 SSH 作業。  VMAccess 可用來將 SSHD 組態重設回到已知的良好組態，而不需透過 SSH 登入。

若要重設 SSHD 組態，請建立名為 `reset_sshd.json` 的檔案並新增下列內容：

```json
{
  "reset_ssh": true
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>管理使用者
VMAccess 是一種 Python 指令碼，可用來管理 Linux VM 上的使用者，但不需要登入及使用 sudo 或根帳戶。

若要建立使用者，請建立名為 `create_new_user.json` 的檔案並新增下列內容：

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

若要刪除使用者，請建立名為 `delete_user.json` 的檔案並新增下列內容：

```json
{
  "remove_user":"myDeleteUser"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>檢查或修復磁碟
使用 VMAccess 可以執行在 Linux VM 磁碟上執行的 fsck。 您也可以使用 VMAccess 執行磁碟檢查和磁碟修復。

若要檢查磁碟然後加以修復，請使用下列 VMAccess 指令碼，建立名為 `disk_check_repair.json` 的檔案並新增下列內容：

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>後續步驟
使用 Azure VMAccess 擴充功能來更新 Linux 是一種對執行中的 Linux VM 進行變更的方法。 您也可以使用 cloud-init 和 Azure Resource Manager 範本之類的工具，在開機時修改您的 Linux VM。

[有關虛擬機器擴充功能和功能](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本](../windows/extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[在建立期間使用 cloud-init 自訂 Linux VM](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


