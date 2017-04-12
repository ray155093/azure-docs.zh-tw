---
title: "使用 Azure CLI 選取 Linux VM 映像 | Microsoft Docs"
description: "了解如何在使用資源管理員部署模型建立 Linux 虛擬機器時，判斷映像的發行者、訂閱詳情及 SKU。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1c4e8b5168d15c480d2aea41ddf7570d310e1252
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>如何使用 Azure CLI 尋找 Linux VM 映像
本主題說明如何針對您可能在其中進行部署的每個位置，尋找發行者、提供項目、SKU 及版本。 


## <a name="use-azure-cli-20"></a>使用 Azure CLI 2.0

當您[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 之後，請使用 `az vm image list` 命令來查看已快取的常用 VM 映像清單。 例如，以下的 `az vm image list -o table` 命令範例會顯示：

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>尋找所有目前的映像

若要取得目前的所有映像清單，請使用 `az vm image list` 命令搭配 `--all` 選項。 與 Azure CLI 1.0 命令不同，`az vm image list --all` 命令預設會傳回 **westus** 中所有的映像 (除非您指定特定的 `--location` 引數)，因此 `--all` 命令需要一些時間才能完成。 如果您打算以互動方式進行調查，請使用 `az vm image list --all > allImages.json`，這會傳回 Azure 上所有目前可用的映像清單，並將它儲存成檔案以供在本機使用。 

如果您心中已經有一或多個映像，您可以指定數個選項之一來僅限搜尋特定的位置、方案、發行者或 SKU。 如果未指定位置，則會傳回適用於 **westus** 的值。

### <a name="find-specific-images"></a>尋找特定映像

請使用 `az vm image list` 搭配篩選來尋找特定資訊。 例如，以下顯示 **Debian** 的可用「方案」(請記住，如果沒有 `--all` 參數，則只會搜尋通用映像的本機快取)：

```azurecli
az vm image list --offer Debian -o table --all
```

輸出會像這樣： 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

您可以在 **--publisher** 和 **--sku** 選項上執行類似的篩選。 您甚至可以在篩選上執行部分比對，例如搜尋 **--offer Deb** 來尋找所有 Debian 映像，或搜尋 **--publisher Micr** 來尋找所有 Microsoft 發行的映像。

如果您知道您要部署的位置，您便可以使用一般映像搜尋結果連同 `az vm image list-skus`、`az vm image list-offers` 和 `az vm image list-publishers` 命令，來尋找您確切想要的項目及可部署的位置。 例如，如果您從上述範例中得知 `credativ` 有 Debian 方案，您便可以使用 `--location` 和其他選項來尋找您所要的確切項目。 下列範例會在 **westeurope** 中尋找 Debian 8 映像：

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

而輸出如下：

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>使用 Azure CLI 1.0 

> [!NOTE]
> 本文說明如何使用支援 Azure Resource Manager 部署模型的 Azure CLI 1.0 或 Azure PowerShell 安裝，來瀏覽並選取虛擬機器映像。 請變更為 Resource Manager 模式，這是一個必要條件。 搭配 Azure CLI 時，請輸入 `azure config mode arm` 來進入該模式。 
> 

尋找映像的最快方式就是呼叫 `azure vm image list` 命令並傳遞位置、發行者名稱 (不區分大小寫！) 及方案 (如果您知道該方案)。 例如，如果您知道 "Canonical" 是 "UbuntuServer" 提供項目的發佈者，則下列清單只是簡短範例 (許多清單相當長)。

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

**Urn** 資料行將是您傳遞給 `azure vm quick-create` 的表單。

不過，您通常還不知道什麼可用。 在此情況下，您可以瀏覽映像，方法是使用 `azure vm image list-publishers` 命令，然後在出現提示時指定資料中心位置。 例如，下列清單是美國西部位置的所有映像發佈者 (將位置設為小寫並移除標準位置中的空格，來傳遞 location 引數)。

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

這些清單可能相當長，因此上述範例清單只是程式碼片段。 假設我注意到 Canonical 事實上是美國西部位置的映像發佈者。 您現在可以呼叫 `azure vm image list-offers` 來尋找其提供項目，並在提示字元中傳遞位置和發行者，如下列範例所示：

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

現在，我們知道在美國西部區域中，Canonical 在 Azure 上發佈 **UbuntuServer** 提供項目。 但是，是什麼 SKU？ 若要取得這些值，您需呼叫 `azure vm image list-skus`，然後使用您探索到的位置、發行者和方案來回應提示。

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

現在使用這項資訊在頂端呼叫原始呼叫，即可找到您所要的映像。

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>後續步驟
現在，您可以精確地選擇想要使用的映像。 若要使用您剛找到的 URN 資訊來快速建立虛擬機器，或使用範本搭配該 URN 資訊，請參閱[使用 Azure CLI 建立 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

