---
title: "適用於 Linux 的 N 系列驅動程式設定 | Microsoft Docs"
description: "如何針對 Azure 中執行 Linux 的 N 系列 VM 設定 NVIDIA GPU 驅動程式"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>為執行 Linux 的 N 系列 VM 設定 GPU 驅動程式

若要利用 Azure N 系列 VM (執行支援的 Linux 散發套件) 的 GPU 功能，您必須在部署之後於每個 VM 上安裝 NVIDIA 圖形驅動程式。 驅動程式設定資訊也適用於 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


> [!IMPORTANT]
> 目前，Linux GPU 支援僅適用於執行 Ubuntu Server 16.04 LTS 的 Azure NC VM。
> 

如需 N 系列 VM 規格、儲存體容量與磁碟的詳細資料，請參閱[虛擬機器的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 另請參閱 [N 系列 VM 的一般考量](#general-considerations-for-n-series-vms)。



## <a name="install-nvidia-cuda-drivers"></a>安裝 NVIDIA CUDA 驅動程式

以下是從 NVIDIA CUDA Toolkit 8.0 在 Linux NC VM 上安裝 NVIDIA 驅動程式的步驟。 C 和 C++ 開發人員可以選擇性地安裝完整 Toolkit，以建置 GPU 加速的應用程式。 如需詳細資訊，請參閱 [CUDA 安裝指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 以下提供發行當時的驅動程式下載連結。 如需最新的驅動程式，請瀏覽 [NVIDIA](http://www.nvidia.com/) 網站。

若要安裝 CUDA Toolkit，請透過 SSH 連線至每部 VM。 若要確認系統有 CUDA 功能的 GPU，請執行下列命令︰

```bash
lspci | grep -i NVIDIA
```
您會看到類似下列範例 (顯示 NVIDIA Tesla K80 卡) 的輸出︰

![lspci 命令輸出](./media/n-series-driver-setup/lspci.png)

然後執行您的配送映像特有的命令。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
安裝可能需要數分鐘的時間。

若要選擇性地安裝完整的 CUDA Toolkit，請輸入︰

```bash
sudo apt-get install cuda
```

重新啟動 VM 並繼續確認安裝。

## <a name="verify-driver-installation"></a>確認驅動程式安裝


若要查詢 GPU 裝置狀態，請透過 SSH 連線至 VM 並執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令列公用程式。 

![NVIDIA 裝置狀態](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>CUDA 驅動程式更新

我們建議您在部署後定期更新 CUDA 驅動程式。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

更新完成後，重新啟動 VM。


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* 不建議您在 Ubuntu NC VM 上安裝 X 伺服器或其他使用 nouveau 驅動程式的系統。 安裝 NVIDIA GPU 驅動程式之前，您必須停用 nouveau 驅動程式。  

* 如果您想要擷取已安裝 NVIDIA 驅動程式之 Linux VM 的映像，請參閱[如何一般化和擷取 Linux 虛擬機器](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)


