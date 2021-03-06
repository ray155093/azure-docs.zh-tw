---
title: "使用 Azure CLI 選取 Linux VM 映像 | Microsoft Docs"
description: "了解如何使用 Azure CLI 來判斷發行者、優惠、SKU 和 Marketplace VM 映像的版本。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/11/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 72c5c2efe2c8a60f13d18b595062448e6a0d1816
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Marketplace 中尋找 Linux VM 映像
本主題描述如何在 Azure Marketplace 中使用 Azure CLI 2.0 尋找 Windows VM 映像。 您可以使用此資訊，在建立 Linux VM 時指定 Marketplace 映像。

請確定您[已安裝](/cli/azure/install-az-cli2)最新的 Azure CLI 2.0 並登入 Azure 帳戶 (`az login`)。

## <a name="list-popular-images"></a>列出常用的映像

執行 [az vm image list](/cli/azure/vm/image#list) 命令，而不包含 `--all` 選項，以查看 Azure Marketplace 中的常用 VM 映像清單。 例如，執行下列命令，以資料表格式顯示常用映像的快取清單：

```azurecli
az vm image list --output table
```

輸出會包含 URN (Urn 資料行中的值)，是屬於表單發行者：優惠：Sku：版本。 透過 `az vm create` 建立 VM 時，可使用此值來指定映像。 使用其中一個常用 VM 映像建立 VM 時，您也可以指定 URN 別名，例如 UbuntuLTS。

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="list-all-current-images"></a>列出所有目前的映像

若要取得目前 Marketplace 中的所有 VM 映像清單，請使用 `az vm image list` 命令搭配 `--all` 選項。 這個版本的命令需要一些時間才能完成：


```azurecli
az vm image list --all
```

如果您未使用 `--location` 選項指定特定的位置，依預設就會傳回 `westus` 的值。 (執行 `az configure --defaults location=<location>` 以設定不同的預設位置。)


如果您想要以互動方式進行調查，請將輸出導向本機檔案。 例如：

```azurecli
az vm image list --all > allImages.json
```




## <a name="find-specific-images"></a>尋找特定映像

使用 `az vm image list` 搭配其他選項，將您的搜尋限制為特定位置、優惠、發行者或 SKU。 例如，以下命令會顯示所有的 Debian 優惠 (請記住，如果沒有 `--all` 參數，則只會搜尋通用映像的本機快取)：

```azurecli
az vm image list --offer Debian --all --output table 
```

輸出可能是很長的清單，因此截斷如下： 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
...
```


使用 `--location`、`--publisher` 和 `--sku` 選項套用類似的篩選條件。 您甚至可以執行篩選的部份相符，例如搜尋 `--offer Deb` 以尋找所有 Debian 映像。

例如，下列命令會列出 `westeurope` 中所有的 Debian 8 個 SKU：

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

輸出：

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```


## <a name="navigate-the-images"></a>瀏覽映像 
要在位置中找到映像的另一個方法是在序列中執行 [az vm image list-publishers](/cli/azure/vm/image#list-publishers)、[az vm image list-offers](/cli/azure/vm/image#list-offers) 和 [az vm image list-skus](/cli/azure/vm/image#list-skus) 命令。 您可以使用這些命令來判斷下列的值：

1. 列出映像發行者。
2. 針對指定的發行者，列出其提供項目。
3. 針對指定的提供項目，列出其 SKU。


例如，下列命令會列出美國西部位置中的映像發行者：

```azurecli
az vm image list-publishers --location westus --output table
```

輸出：

```
Location    Name
----------  ----------------------------------------------------
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
這些清單可能很長，因此本範例輸出只是程式碼片段。 使用這項資訊從特定的發行者尋找優惠。 例如，如果 Canonical 是美國西部位置的映像發行者，執行 `azure vm image list-offers` 可找到其優惠。 傳遞位置和發行者，如下列範例所示：

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

輸出：

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
您看到在美國西部區域中，Canonical 在 Azure 上發佈 **UbuntuServer** 優惠。 但是，是什麼 SKU？ 若要取得這些值，請執行 `azure vm image list-skus`並設定您探索到的位置、發行者和優惠：

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

輸出：

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

最後，使用 `az vm image list` 命令來您需要的 SKU 特定版本，例如，**14.04-LTS**：

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

輸出：

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
```
## <a name="next-steps"></a>後續步驟
現在，您可以記下 URN 值，精確地選擇想要使用的映像。 在指定映像時，您可以在包含「最新」的 URN 中，選擇性地取代版本號碼使用。 此版本一律為發佈的最新版本。 若要使用您剛找到的 URN 資訊來快速建立虛擬機器，請參閱[使用 Azure CLI 建立 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

